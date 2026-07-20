# Clients and SDKs

MEGA's public Router is OpenAI-compatible, so most applications can use an OpenAI SDK with a different base URL and a MEGA token. The MEGA CLI and Python `InferenceClient` add model discovery and routing conveniences.

## Shared configuration

All clients use:

```text
Base URL: https://inference.tensorplay.cn/v1
Bearer token: MEGA PAT with inference:run
```

Keep the token in an environment variable or secret manager:

```bash
export MEGA_TOKEN="mega_..."
```

Do not use a Provider key as `MEGA_TOKEN`. Save it through [Custom Provider Keys](/docs/inference-providers/custom-provider-keys) and continue authenticating the Router with a MEGA PAT.

## MEGA CLI

The CLI is the fastest way to inspect routing and test credentials:

```bash
mega inference models --task chat-completions
mega inference chat mega/gpt-5.4-mini "Hello"
mega inference responses mega/gpt-5.4-mini "Hello" --stream
mega inference embeddings BAAI/bge-m3 "Hello" --format json
```

It reads the active login or `MEGA_TOKEN`. `Inference-Id` and selected Provider are written as hints without corrupting JSON stdout.

Shared execution options are:

| Option | Purpose |
| --- | --- |
| `--provider` | `auto`, `fastest`, `cheapest`, `preferred`, or a Provider slug. |
| `--billing` | `auto`, `routed`, or `byok`. |
| `--bill-to` | Select an eligible organization billing owner. |
| `--session-id` | Add privacy-preserving affinity for Chat or Responses. |
| `--token` | Override the saved token for one command. |
| `--stream` | Stream Chat or Responses as text or JSON Lines. |

## OpenAI Python SDK

```bash
python -m pip install openai
```

```python
import os
from openai import OpenAI

client = OpenAI(
    base_url="https://inference.tensorplay.cn/v1",
    api_key=os.environ["MEGA_TOKEN"],
)

chat = client.chat.completions.create(
    model="mega/gpt-5.4-mini:fastest",
    messages=[{"role": "user", "content": "Hello"}],
)
print(chat.choices[0].message.content)
```

Set MEGA request controls as default headers when needed:

```python
client = OpenAI(
    base_url="https://inference.tensorplay.cn/v1",
    api_key=os.environ["MEGA_TOKEN"],
    default_headers={
        "X-Mega-Inference-Billing": "routed",
        "X-Mega-Bill-To": "research-lab",
    },
)
```

Create separate clients when workloads use different billing owners or billing modes. This makes accidental cross-charging less likely.

## MEGA Python InferenceClient

Install the MEGA package:

```bash
python -m pip install megatensors
```

```python
import os
from megatensors import InferenceClient

client = InferenceClient(
    provider="preferred",
    api_key=os.environ["MEGA_TOKEN"],
    bill_to="research-lab",
    headers={"X-Mega-Inference-Billing": "auto"},
)

result = client.chat.completions.create(
    model="mega/gpt-5.4-mini",
    messages=[{"role": "user", "content": "Hello"}],
    max_tokens=64,
)
print(result.choices[0].message.content)
```

`provider="auto"` and `provider="fastest"` leave the model on the default Router strategy. `cheapest` and `preferred` append the corresponding model selection. Use the OpenAI SDK directly for the Responses API.

## Raw HTTP

Raw HTTP is appropriate for languages without an OpenAI client or when you need exact control over headers. Always set a request timeout, parse the OpenAI-compatible error object, and log `Inference-Id`.

See [Router API Reference](/docs/inference-providers/api-reference) for endpoints and headers. Use the task guides for request examples:

- [Chat Completions](/docs/inference-providers/tasks/chat-completions)
- [Responses API](/docs/inference-providers/tasks/responses)
- [Embeddings](/docs/inference-providers/tasks/embeddings)

## Production checklist

- Pin dependencies and test the exact SDK version you deploy.
- Use a fine-grained `inference:run` token for each workload.
- Set `X-Mega-Bill-To` explicitly for organization workloads.
- Bound input and maximum output tokens before dispatch.
- Treat streaming retries as new billable requests.
- Record model, task, billing mode, `Inference-Id`, and selected Provider without logging content.
