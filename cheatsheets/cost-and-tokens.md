# Token & Cost Management Cheatsheet

LLM bills are driven by **tokens**. Understand them, count them, and you can cut costs by
50–90% without hurting quality.

---

## What is a token?

A token is a chunk of text — roughly **¾ of a word** in English (≈ 4 characters). "Tokenization"
splits text into these chunks before the model sees it.

- ~750 words ≈ ~1,000 tokens (English, rough rule).
- You pay for **input tokens** (your prompt) **and output tokens** (the response).
- Output tokens are usually **more expensive** than input tokens.
- Cost = `(input_tokens × in_price + output_tokens × out_price)`, priced per **million** tokens.

---

## Count tokens before you send

### OpenAI (tiktoken)

```python
import tiktoken
enc = tiktoken.encoding_for_model("gpt-4o")
print(len(enc.encode("How many tokens is this sentence?")))
```

### Anthropic (count endpoint)

```python
import anthropic
client = anthropic.Anthropic()
count = client.messages.count_tokens(
    model="claude-sonnet-4-6",
    messages=[{"role": "user", "content": "How many tokens is this?"}],
)
print(count.input_tokens)
```

---

## The big cost levers (in order of impact)

### 1. Pick the right model tier
The single biggest lever. A small model can be 10–30× cheaper than a frontier one. Route
simple tasks to cheap models — see the [Model Selection cheatsheet](./model-selection.md).

### 2. Prompt caching
If you reuse a large, static prefix (system prompt, big document, few-shot examples) across
many calls, **cache it**. Cached input tokens are dramatically cheaper and faster. Supported
by Anthropic, OpenAI, and Google.

> Put the *static* content first (cached), and the *changing* content (the user's question)
> last. Order matters — caches match a prefix.

### 3. Batch APIs
For non-urgent, high-volume jobs (classifying a backlog, generating embeddings), batch
endpoints typically cost **~50% less** in exchange for slower turnaround.

### 4. Trim the prompt
- Remove boilerplate, redundant instructions, and dead few-shot examples.
- Summarize or truncate long histories in chat apps (sliding window).
- In RAG, **rerank and send fewer chunks** instead of dumping top-50.

### 5. Cap output
Set `max_tokens` to the real ceiling you need. Ask for concise answers. Output is the pricey
side.

### 6. Cache at the application layer
Identical or near-identical requests? Cache the *response* in your own app (e.g. Redis) and
skip the API call entirely.

---

## Quick savings checklist

- [ ] Am I using the cheapest model that meets quality?
- [ ] Is my large static context cached?
- [ ] Can this workload run on a batch API?
- [ ] Is `max_tokens` set sensibly?
- [ ] Am I sending only the chunks/history I actually need?
- [ ] Am I logging token usage per request to spot regressions?

---

## Estimating monthly cost (back-of-envelope)

```
monthly_cost ≈ requests/day × 30
             × ( avg_input_tokens/1e6  × input_price_per_million
               + avg_output_tokens/1e6 × output_price_per_million )
```

Log real token counts from production for a day, then plug in actual averages — estimates
based on guesses are usually wrong by 2–5×.

---

## Further reading

- [Anthropic Token Counting](https://docs.anthropic.com/en/docs/build-with-claude/token-counting)
- [Anthropic Prompt Caching](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching)
- [OpenAI Pricing](https://openai.com/api/pricing/)
- [tiktoken](https://github.com/openai/tiktoken)
