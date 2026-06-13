# Prompt Engineering Cheatsheet

The techniques that actually change outputs — not vibes. Each pattern has a *why*, a *when*, and a *template*.

---

## The mental model

An LLM predicts the next token given everything before it. "Prompt engineering" is just
**arranging the context so the most likely continuation is the answer you want.** Everything
below follows from that.

---

## 1. Be specific and explicit

Vague in, vague out. State the task, the format, the audience, and the constraints.

❌ "Summarize this."
✅ "Summarize the text below in 3 bullet points for a non-technical manager. Each bullet ≤ 20 words."

---

## 2. Few-shot prompting (show, don't tell)

Give 2–5 examples of input → output. This is the single highest-leverage technique for
format consistency and edge-case handling.

```
Classify the sentiment as POSITIVE, NEGATIVE, or NEUTRAL.

Text: "I love this!"      → POSITIVE
Text: "It's fine I guess" → NEUTRAL
Text: "Total waste."      → NEGATIVE
Text: "Best purchase ever" →
```

**When:** classification, formatting, style matching, structured extraction.

---

## 3. Chain-of-thought (let it think)

Asking the model to reason step by step before answering dramatically improves accuracy on
math, logic, and multi-step tasks.

```
Question: {hard question}
Think step by step, then give your final answer on a new line starting with "Answer:".
```

**Pro tip:** if you only need the final answer, ask the model to put its reasoning inside
`<thinking>` tags and the answer outside — then strip the tags. Many modern models also have
a dedicated "extended thinking" / reasoning mode; prefer that when available.

---

## 4. Role / persona prompting

Setting a role primes the relevant knowledge and tone.

```
You are a senior security engineer reviewing code for vulnerabilities.
```

Use the **system prompt** for role, rules, and persistent constraints. Use **user messages**
for the actual task.

---

## 5. Structured output

Want JSON? Ask for it precisely, and show the schema.

```
Return ONLY valid JSON matching this schema, with no markdown fences:
{"name": string, "score": number between 0 and 1, "tags": string[]}
```

For reliability, use a library like **Instructor** or the provider's native structured-output
mode instead of parsing free text.

---

## 6. Delimiters & structure

Separate instructions from data so the model never confuses them. This also reduces prompt
injection risk.

```
Summarize the text between the <doc> tags.

<doc>
{untrusted user content here}
</doc>
```

XML-style tags work especially well with Claude; triple backticks or `###` headers work
broadly.

---

## 7. Prefilling the response

Steer the start of the answer by putting words in the assistant's mouth (where supported).

```
User: List 3 fruits as JSON.
Assistant: ["    ← prefill forces JSON array start
```

---

## 8. Decomposition (prompt chaining)

Don't ask for everything at once. Break complex tasks into steps, each its own call:
*extract → transform → format*. Each step is easier to get right and debug.

---

## Parameters that matter

| Parameter | What it does | Rule of thumb |
|---|---|---|
| `temperature` | Randomness (0 = deterministic, higher = creative) | `0` for extraction/classification, `0.7–1.0` for creative writing |
| `max_tokens` | Caps output length | Set it — protects against runaway cost |
| `top_p` | Nucleus sampling | Usually leave default; tune temperature *or* top_p, not both |
| `stop` | Sequences that end generation | Useful for structured/delimited output |

---

## Common mistakes

- **Burying the instruction** in the middle of a huge prompt. Put key instructions at the start *and* restate them at the end for long prompts.
- **Negative-only instructions** ("don't be verbose"). Tell it what *to* do ("answer in ≤ 2 sentences").
- **Mixing data and instructions** with no delimiter → injection + confusion.
- **Over-prompting small tasks.** A 500-word prompt for "translate this" wastes tokens and adds noise.
- **Not iterating.** Treat prompts like code: test on real inputs, keep a few hard examples, measure.

---

## A reusable template

```
# Role
You are {role}.

# Task
{one clear sentence describing the goal}

# Rules
- {constraint 1}
- {constraint 2}

# Output format
{exact format, with an example}

# Input
<input>
{the data}
</input>
```

---

## Further reading

- [Anthropic Prompt Engineering Guide](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview)
- [OpenAI Prompt Engineering Guide](https://platform.openai.com/docs/guides/prompt-engineering)
- [Prompting Guide (DAIR.AI)](https://www.promptingguide.ai)
