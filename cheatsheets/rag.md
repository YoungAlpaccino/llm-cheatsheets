# RAG Cheatsheet

Retrieval-Augmented Generation: give the model *your* data at query time instead of
fine-tuning. This is the most common way to build LLM apps over private/changing knowledge.

---

## The pipeline in one picture

```
                 ┌─────────────── INDEXING (offline, once) ───────────────┐
Documents ─▶ Chunk ─▶ Embed ─▶ Store vectors in a vector DB
                 └─────────────────────────────────────────────────────────┘

                 ┌─────────────── QUERY TIME (per request) ───────────────┐
User question ─▶ Embed ─▶ Search top-k similar chunks ─▶ (Rerank) ─▶
   ─▶ Stuff chunks into prompt ─▶ LLM generates grounded answer
                 └─────────────────────────────────────────────────────────┘
```

---

## 1. Chunking — where most RAG quality is won or lost

- **Start with ~500–1000 tokens per chunk, with ~10–20% overlap.** Overlap prevents
  splitting an answer across a boundary.
- **Respect structure.** Split on headings, paragraphs, or code blocks — not blindly every N
  characters.
- **Keep metadata** with each chunk (source, title, section, URL). You'll need it for
  citations and filtering.
- **Too big** = irrelevant text dilutes the signal and wastes tokens. **Too small** = chunks
  lose context. Tune on real queries.

---

## 2. Embeddings

Turn text into vectors so "similar meaning" = "close in space."

```python
# Local, free, good quality
from sentence_transformers import SentenceTransformer
model = SentenceTransformer("all-MiniLM-L6-v2")
vectors = model.encode(["chunk one", "chunk two"])
```

```python
# Hosted (OpenAI example)
from openai import OpenAI
client = OpenAI()
v = client.embeddings.create(model="text-embedding-3-small", input="chunk one")
```

**Rules:** use the *same* embedding model for indexing and querying. Normalize vectors if your
DB uses cosine similarity.

---

## 3. Store & search (minimal Chroma example)

```python
import chromadb

client = chromadb.Client()
col = client.create_collection("docs")

col.add(
    documents=["The mitochondria is the powerhouse of the cell.", "Paris is in France."],
    ids=["d1", "d2"],
    metadatas=[{"src": "bio"}, {"src": "geo"}],
)

hits = col.query(query_texts=["What produces energy in a cell?"], n_results=2)
print(hits["documents"])
```

---

## 4. Rerank (cheap, big quality win)

Vector search gets you ~50 roughly-relevant chunks; a reranker reorders them by true
relevance so the *best* few go into the prompt.

```python
import cohere
co = cohere.Client()
reranked = co.rerank(model="rerank-v3.5", query=q, documents=chunks, top_n=5)
```

---

## 5. Assemble the prompt

```
Answer the question using ONLY the context below. If the answer isn't in the
context, say "I don't know." Cite sources by their [id].

<context>
[d1] The mitochondria is the powerhouse of the cell.
[d2] ...
</context>

Question: What produces energy in a cell?
```

The "say I don't know" instruction is what reduces hallucination.

---

## Common failure modes (and fixes)

| Symptom | Likely cause | Fix |
|---|---|---|
| Right docs retrieved, wrong answer | Too many/irrelevant chunks crowding the prompt | Rerank, reduce top-k |
| Relevant doc never retrieved | Bad chunking or embedding mismatch | Fix chunk boundaries; check same model both sides |
| Hallucinated facts | Model ignoring context | Add "use ONLY the context" + "say I don't know" |
| Can't answer multi-part questions | Single retrieval misses sub-topics | Query decomposition / multi-query retrieval |
| Slow | Reranking everything, huge top-k | Vector-search wide, rerank narrow |

---

## When NOT to use RAG

- The knowledge fits in the context window → just put it in the prompt.
- The task needs reasoning/style, not facts → prompt engineering or fine-tuning.
- You need exact, structured lookups → a real database query beats semantic search.

---

## Further reading

- [LlamaIndex docs](https://docs.llamaindex.ai)
- [Ragas — RAG evaluation](https://github.com/explodinggradients/ragas)
- [Anthropic: Contextual Retrieval](https://www.anthropic.com/news/contextual-retrieval)
