# API Quickstart Cheatsheet

First calls, streaming, tool use, and error handling for the major LLM providers. Copy, paste, adapt.

> **Setup:** Always load your API key from an environment variable — never hardcode it.
> ```bash
> export ANTHROPIC_API_KEY="sk-ant-..."
> export OPENAI_API_KEY="sk-..."
> export GEMINI_API_KEY="..."
> ```

---

## 1. Your first call

### Anthropic (Claude)

```python
import anthropic

client = anthropic.Anthropic()  # reads ANTHROPIC_API_KEY

resp = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    system="You are a concise assistant.",
    messages=[{"role": "user", "content": "Explain RAG in two sentences."}],
)
print(resp.content[0].text)
```

### OpenAI

```python
from openai import OpenAI

client = OpenAI()  # reads OPENAI_API_KEY

resp = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {"role": "system", "content": "You are a concise assistant."},
        {"role": "user", "content": "Explain RAG in two sentences."},
    ],
)
print(resp.choices[0].message.content)
```

### Google (Gemini)

```python
from google import genai

client = genai.Client()  # reads GEMINI_API_KEY

resp = client.models.generate_content(
    model="gemini-2.0-flash",
    contents="Explain RAG in two sentences.",
)
print(resp.text)
```

### Provider-agnostic (LiteLLM)

```python
from litellm import completion  # pip install litellm

resp = completion(
    model="anthropic/claude-sonnet-4-6",  # or "openai/gpt-4o", "gemini/gemini-2.0-flash"
    messages=[{"role": "user", "content": "Explain RAG in two sentences."}],
)
print(resp.choices[0].message.content)
```

---

## 2. Streaming responses

Stream tokens as they arrive for a responsive UX.

### Anthropic

```python
with client.messages.stream(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Write a haiku about databases."}],
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)
```

### OpenAI

```python
stream = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Write a haiku about databases."}],
    stream=True,
)
for chunk in stream:
    delta = chunk.choices[0].delta.content
    if delta:
        print(delta, end="", flush=True)
```

---

## 3. Tool use / function calling

Let the model call your code. The pattern is the same everywhere: define tools → model requests a call → you run it → you return the result.

### Anthropic

```python
tools = [{
    "name": "get_weather",
    "description": "Get the current weather for a city.",
    "input_schema": {
        "type": "object",
        "properties": {"city": {"type": "string"}},
        "required": ["city"],
    },
}]

resp = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1024,
    tools=tools,
    messages=[{"role": "user", "content": "What's the weather in Tokyo?"}],
)

# If resp.stop_reason == "tool_use", find the tool_use block, run your function,
# then send the result back as a "tool_result" content block in a new user turn.
```

### OpenAI

```python
tools = [{
    "type": "function",
    "function": {
        "name": "get_weather",
        "description": "Get the current weather for a city.",
        "parameters": {
            "type": "object",
            "properties": {"city": {"type": "string"}},
            "required": ["city"],
        },
    },
}]

resp = client.chat.completions.create(
    model="gpt-4o",
    tools=tools,
    messages=[{"role": "user", "content": "What's the weather in Tokyo?"}],
)
# Check resp.choices[0].message.tool_calls, run the function, append a
# {"role": "tool", ...} message, then call again.
```

---

## 4. Structured / JSON output

The reliable way to get machine-readable output.

### With Instructor (works across providers)

```python
import instructor
from pydantic import BaseModel
from anthropic import Anthropic

class User(BaseModel):
    name: str
    age: int

client = instructor.from_anthropic(Anthropic())

user = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=512,
    response_model=User,
    messages=[{"role": "user", "content": "John Doe is 30 years old."}],
)
print(user.name, user.age)  # -> John Doe 30
```

---

## 5. Robust error handling

Production code needs retries with backoff. Rate limits (429) and transient
server errors (5xx) are normal — handle them.

```python
import time
import anthropic

client = anthropic.Anthropic(max_retries=3)  # SDK retries automatically

def call_with_backoff(messages, attempts=5):
    for i in range(attempts):
        try:
            return client.messages.create(
                model="claude-sonnet-4-6",
                max_tokens=1024,
                messages=messages,
            )
        except anthropic.RateLimitError:
            wait = 2 ** i  # exponential backoff: 1, 2, 4, 8, ...
            print(f"Rate limited; retrying in {wait}s")
            time.sleep(wait)
        except anthropic.APIStatusError as e:
            if e.status_code >= 500 and i < attempts - 1:
                time.sleep(2 ** i)
                continue
            raise
    raise RuntimeError("Exhausted retries")
```

**Error checklist:**
- ✅ Retry on `429` and `5xx` with exponential backoff.
- ✅ Do **not** retry on `400`/`401`/`403` — fix the request or key.
- ✅ Set timeouts so a hung request doesn't block your app.
- ✅ Cap `max_tokens` to avoid runaway costs.

---

## Notes on model names

Model identifiers change as providers release new versions. The examples above use
representative IDs — always check the provider's current model list:
- Anthropic: https://docs.anthropic.com/en/docs/about-claude/models
- OpenAI: https://platform.openai.com/docs/models
- Google: https://ai.google.dev/gemini-api/docs/models
