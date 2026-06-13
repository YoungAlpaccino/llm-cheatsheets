# Awesome LLM Engineering [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> A curated, no-fluff list of the best tools, libraries, guides, and cheatsheets for building **real applications with Large Language Models** — and for actually understanding how they work.

Most "awesome LLM" lists are walls of links. This one is opinionated and maintained: every entry is here because it's genuinely useful for **shipping** something. Cheatsheets live in [`/cheatsheets`](./cheatsheets) so you can copy-paste your way to a working prototype.

<p align="center">
  <a href="#-quick-start">Quick Start</a> •
  <a href="#-cheatsheets">Cheatsheets</a> •
  <a href="#-models--providers">Models</a> •
  <a href="#-frameworks--orchestration">Frameworks</a> •
  <a href="#-rag--vector-search">RAG</a> •
  <a href="#-contributing">Contribute</a>
</p>

![License: CC0-1.0](https://img.shields.io/badge/License-CC0_1.0-lightgrey.svg)
![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)
![Last Commit](https://img.shields.io/github/last-commit/USERNAME/awesome-llm-engineering)
![Stars](https://img.shields.io/github/stars/USERNAME/awesome-llm-engineering?style=social)

> **Note:** Replace `USERNAME` in the badge URLs above with your GitHub username after you create the repo.

---

## Contents

- [⚡ Quick Start](#-quick-start)
- [📑 Cheatsheets](#-cheatsheets)
- [🧠 Models & Providers](#-models--providers)
- [🛠️ Frameworks & Orchestration](#-frameworks--orchestration)
- [✍️ Prompt Engineering](#-prompt-engineering)
- [📚 RAG & Vector Search](#-rag--vector-search)
- [🤖 Agents & Tool Use](#-agents--tool-use)
- [🔬 Evaluation & Observability](#-evaluation--observability)
- [🏃 Local & Self-Hosted Inference](#-local--self-hosted-inference)
- [💰 Cost & Token Management](#-cost--token-management)
- [🔐 Security & Safety](#-security--safety)
- [🎓 Learning Resources](#-learning-resources)
- [🤝 Contributing](#-contributing)

---

## ⚡ Quick Start

New to building with LLMs? This is the 5-minute path from zero to a working call.

1. **Pick a provider.** Start with a hosted API (Anthropic, OpenAI, or Google) — no GPU needed.
2. **Get an API key** and set it as an environment variable. Never hardcode keys.
3. **Make your first call.** See the [API Quickstart cheatsheet](./cheatsheets/api-quickstart.md).
4. **Learn to prompt.** See the [Prompt Engineering cheatsheet](./cheatsheets/prompt-engineering.md).
5. **Add your own data.** See the [RAG cheatsheet](./cheatsheets/rag.md).

```bash
# The "hello world" of LLM apps (Anthropic example)
export ANTHROPIC_API_KEY="sk-ant-..."
pip install anthropic
python -c "import anthropic; print(anthropic.Anthropic().messages.create(model='claude-sonnet-4-6', max_tokens=100, messages=[{'role':'user','content':'Say hi in 5 words'}]).content[0].text)"
```

---

## 📑 Cheatsheets

The heart of this repo — dense, copy-pasteable references.

| Cheatsheet | What's inside |
|---|---|
| [API Quickstart](./cheatsheets/api-quickstart.md) | First calls, streaming, tool use, and error handling for the major providers |
| [Prompt Engineering](./cheatsheets/prompt-engineering.md) | Patterns that actually move the needle: few-shot, chain-of-thought, structured output, role prompting |
| [RAG](./cheatsheets/rag.md) | Chunking, embeddings, retrieval, and the mistakes everyone makes |
| [Model Selection](./cheatsheets/model-selection.md) | How to choose a model: capability vs. cost vs. latency, with a decision tree |
| [Token & Cost Management](./cheatsheets/cost-and-tokens.md) | Counting tokens, prompt caching, batching, and slashing your bill |

---

## 🧠 Models & Providers

### Hosted APIs

- **[Anthropic — Claude](https://docs.anthropic.com)** — Strong reasoning, long context, excellent tool use. Models: Opus (most capable), Sonnet (balanced), Haiku (fast/cheap).
- **[OpenAI](https://platform.openai.com/docs)** — Broad ecosystem, GPT family, strong tooling and SDKs.
- **[Google — Gemini](https://ai.google.dev)** — Very long context windows, multimodal, tight Google Cloud integration.
- **[Mistral](https://docs.mistral.ai)** — Strong open-weight and hosted models, European data residency.
- **[Cohere](https://docs.cohere.com)** — Enterprise focus, excellent embeddings and rerankers.

### Open-weight model families

- **[Llama](https://www.llama.com)** (Meta) — The de facto open baseline.
- **[Mistral / Mixtral](https://docs.mistral.ai)** — Efficient open weights, mixture-of-experts options.
- **[Qwen](https://github.com/QwenLM)** (Alibaba) — Strong multilingual and coding variants.
- **[Gemma](https://ai.google.dev/gemma)** (Google) — Lightweight open models.
- **[DeepSeek](https://github.com/deepseek-ai)** — Competitive reasoning and coding models.

> 📄 See the [Model Selection cheatsheet](./cheatsheets/model-selection.md) for how to actually choose.

---

## 🛠️ Frameworks & Orchestration

- **[LangChain](https://github.com/langchain-ai/langchain)** — The kitchen-sink framework for chains, agents, and integrations.
- **[LlamaIndex](https://github.com/run-llama/llama_index)** — Data framework focused on RAG and indexing.
- **[Haystack](https://github.com/deepset-ai/haystack)** — Production-grade pipelines for search and RAG.
- **[DSPy](https://github.com/stanfordnlp/dspy)** — Programming (not prompting) LLMs; optimize prompts automatically.
- **[Instructor](https://github.com/jxnl/instructor)** — Structured outputs with Pydantic, the easy way.
- **[LiteLLM](https://github.com/BerriAI/litellm)** — One API for 100+ LLM providers. Great for provider-agnostic code.
- **[Pydantic AI](https://github.com/pydantic/pydantic-ai)** — Type-safe agent framework from the Pydantic team.

---

## ✍️ Prompt Engineering

- **[Anthropic Prompt Engineering Guide](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview)** — Practical, model-specific, excellent.
- **[OpenAI Prompt Engineering Guide](https://platform.openai.com/docs/guides/prompt-engineering)** — Solid fundamentals.
- **[Learn Prompting](https://learnprompting.org)** — Free, structured course.
- **[Prompt Engineering Guide (DAIR.AI)](https://www.promptingguide.ai)** — Comprehensive techniques + papers.
- 📄 **[Our Prompt Engineering cheatsheet](./cheatsheets/prompt-engineering.md)** — the TL;DR of all of the above.

---

## 📚 RAG & Vector Search

### Vector databases

- **[Chroma](https://github.com/chroma-core/chroma)** — Dead-simple local-first vector DB. Best for getting started.
- **[Qdrant](https://github.com/qdrant/qdrant)** — Fast, production-ready, great filtering.
- **[Weaviate](https://github.com/weaviate/weaviate)** — Feature-rich, hybrid search built in.
- **[Milvus](https://github.com/milvus-io/milvus)** — Scales to billions of vectors.
- **[pgvector](https://github.com/pgvector/pgvector)** — Vectors inside Postgres. Use what you already have.

### Embeddings & reranking

- **[Sentence Transformers](https://github.com/UKPLab/sentence-transformers)** — Local embeddings, huge model zoo.
- **[Cohere Rerank](https://docs.cohere.com/docs/rerank)** — Cheap, large accuracy boost over raw vector search.

> 📄 See the [RAG cheatsheet](./cheatsheets/rag.md) for chunking strategy and common failure modes.

---

## 🤖 Agents & Tool Use

- **[Anthropic Tool Use Guide](https://docs.anthropic.com/en/docs/build-with-claude/tool-use)** — How function calling actually works.
- **[Model Context Protocol (MCP)](https://modelcontextprotocol.io)** — Open standard for connecting LLMs to tools and data.
- **[OpenAI Function Calling](https://platform.openai.com/docs/guides/function-calling)** — The OpenAI flavor of tool use.
- **[smolagents](https://github.com/huggingface/smolagents)** — Minimal agent library from Hugging Face.
- **[CrewAI](https://github.com/crewAIInc/crewAI)** — Multi-agent orchestration.

---

## 🔬 Evaluation & Observability

- **[Ragas](https://github.com/explodinggradients/ragas)** — Evaluation framework specifically for RAG.
- **[DeepEval](https://github.com/confident-ai/deepeval)** — "Pytest for LLMs."
- **[Langfuse](https://github.com/langfuse/langfuse)** — Open-source tracing, evals, and prompt management.
- **[Phoenix (Arize)](https://github.com/Arize-ai/phoenix)** — Open-source LLM observability and tracing.
- **[promptfoo](https://github.com/promptfoo/promptfoo)** — Test and compare prompts/models from the CLI.

---

## 🏃 Local & Self-Hosted Inference

- **[Ollama](https://github.com/ollama/ollama)** — Run open models locally with one command. Best starting point.
- **[llama.cpp](https://github.com/ggml-org/llama.cpp)** — Efficient CPU/GPU inference in C/C++.
- **[vLLM](https://github.com/vllm-project/vllm)** — High-throughput serving for production.
- **[LM Studio](https://lmstudio.ai)** — Desktop GUI for running local models.
- **[Text Generation Inference (TGI)](https://github.com/huggingface/text-generation-inference)** — Hugging Face's production server.

---

## 💰 Cost & Token Management

- **[tiktoken](https://github.com/openai/tiktoken)** — Fast BPE tokenizer (OpenAI models).
- **[Anthropic Token Counting](https://docs.anthropic.com/en/docs/build-with-claude/token-counting)** — Count before you spend.
- **Prompt caching** — Reuse large, static context across calls for major savings. Supported by Anthropic, OpenAI, and Google.
- **Batching APIs** — Trade latency for ~50% cost cuts on non-urgent workloads.

> 📄 See the [Cost & Tokens cheatsheet](./cheatsheets/cost-and-tokens.md).

---

## 🔐 Security & Safety

- **[OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/)** — Start here. Prompt injection, data leakage, and more.
- **[Prompt injection primer](https://simonwillison.net/series/prompt-injection/)** — Simon Willison's ongoing, essential writing.
- **Golden rules:** never trust model output blindly, never put secrets in prompts, always validate tool calls, and sandbox anything the model can execute.

---

## 🎓 Learning Resources

- **[Andrej Karpathy — Neural Networks: Zero to Hero](https://karpathy.ai/zero-to-hero.html)** — Build a transformer from scratch.
- **[Hugging Face LLM Course](https://huggingface.co/learn/llm-course)** — Free, hands-on.
- **[The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/)** — The classic visual explainer.
- **[Anthropic Cookbook](https://github.com/anthropics/anthropic-cookbook)** — Runnable recipes.
- **[OpenAI Cookbook](https://github.com/openai/openai-cookbook)** — Huge collection of examples.

---

## 🤝 Contributing

Contributions make this list better for everyone. See [CONTRIBUTING.md](./CONTRIBUTING.md) for the (short) guidelines.

**Quick version:** open a PR that adds a genuinely useful resource, keep the one-line description honest and specific, and put it in the right section. Self-promotion of low-effort projects will be declined.

---

## License

[![CC0](https://licensebuttons.net/p/zero/1.0/88x31.png)](LICENSE)

To the extent possible under law, the contributors have waived all copyright and related rights to this work. See [LICENSE](./LICENSE).

---

<p align="center">
  <i>If this saved you time, a ⭐ helps others find it.</i>
</p>
