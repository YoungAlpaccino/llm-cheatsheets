# Model Selection Cheatsheet

How to actually choose a model instead of defaulting to the biggest one. The three axes are
**capability**, **cost**, and **latency** — you rarely max all three.

---

## Decision tree

```
Is the task simple? (classification, extraction, short rewrites, routing)
 ├─ YES ─▶ Use the smallest/fastest tier (e.g. Claude Haiku, GPT-4o mini, Gemini Flash).
 │         Cheap + fast. Upgrade only if quality is insufficient.
 └─ NO
     │
     Does it need deep reasoning? (multi-step logic, hard code, planning, analysis)
      ├─ YES ─▶ Use a top reasoning tier (e.g. Claude Opus, frontier GPT, Gemini Pro).
      │         Consider an explicit "thinking"/reasoning mode if available.
      └─ NO ─▶ Use a balanced mid tier (e.g. Claude Sonnet, GPT-4o, Gemini Pro).
                Best default for most production apps.
```

---

## The capability tiers (mental model)

Most providers ship three tiers. Names change; the *shape* doesn't:

| Tier | Good for | Trade-off |
|---|---|---|
| **Small / fast** | High-volume, simple, latency-sensitive tasks | Weaker on hard reasoning |
| **Balanced** | The default for most apps; great quality/cost ratio | — |
| **Frontier / large** | Hardest reasoning, agentic work, complex code | Slower, most expensive |

> Examples (check current model lists, these evolve):
> - Anthropic: Haiku → Sonnet → Opus
> - OpenAI: mini → flagship → reasoning models
> - Google: Flash → Pro

---

## Factors beyond raw capability

- **Context window** — How much can you stuff in? Long-document or large-codebase work needs
  big context (some models reach 1M+ tokens).
- **Latency** — Smaller models respond faster. Critical for chat UX and agent loops with many
  calls.
- **Cost** — Priced per million input/output tokens; output is usually pricier. At scale,
  tier choice dominates your bill.
- **Multimodality** — Need image/audio/video input? Confirm the model supports it.
- **Tool use quality** — For agents, tool-calling reliability varies a lot between models.
- **Open vs. hosted** — Self-hosting open weights (Llama, Mistral, Qwen) trades ops burden for
  control, privacy, and no per-token fees.

---

## A practical strategy: route, don't commit

Don't pick one model for everything. **Route** by task:

```python
def pick_model(task_complexity: str) -> str:
    return {
        "simple":   "claude-haiku-4-5",
        "balanced": "claude-sonnet-4-6",
        "hard":     "claude-opus-4-8",
    }[task_complexity]
```

Use a cheap model to *classify* the task, then route hard cases to the expensive model. This
can cut costs dramatically with little quality loss.

---

## How to evaluate (don't trust leaderboards alone)

1. Build a small **eval set** of 20–50 real examples from your use case.
2. Run 2–3 candidate models against it.
3. Score with a rubric (or an LLM judge) on *your* criteria, not a generic benchmark.
4. Compare quality **and** cost/latency. Pick the cheapest model that clears your bar.

> Public benchmarks measure general ability; your task is specific. A model that tops a
> leaderboard may lose on *your* data.

---

## Further reading

- [Cost & Tokens cheatsheet](./cost-and-tokens.md)
- Provider model lists: [Anthropic](https://docs.anthropic.com/en/docs/about-claude/models) · [OpenAI](https://platform.openai.com/docs/models) · [Google](https://ai.google.dev/gemini-api/docs/models)
