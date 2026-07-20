# Your First Inference Provider Call

This guide takes you from a live model mapping to a successful routed request. MEGA runs the model on a healthy registered Provider; you do not deploy an endpoint first.

## Prerequisites

You need a MEGA account and a Personal Access Token (PAT) with the `inference:run` scope. Create the token under **Settings → Access Tokens**, copy it once, and keep it outside source control:

```bash
export MEGA_TOKEN="mega_..."
```

An interactive workstation may use `mega auth login` instead. The CLI and Python client resolve the selected token automatically.

## Step 1: find a live model

In the browser, open the [Inference Provider model comparison](/inference/models) to
filter live model/Provider routes and compare price, context length, latency,
throughput, tool calling, and structured-output support.

Install the MEGA CLI if needed:

```bash
uv tool install megatensors
```

Alternatively, install with `pipx` or the active Python environment:

```bash
pipx install megatensors
python -m pip install megatensors
```

List healthy Chat Completions mappings:

```bash
mega models ls \
  --pipeline-tag chat-completions \
  --warm \
  --sort first-token-latency
```

Narrow the result to one Provider or emit machine-readable records:

```bash
mega models ls --inference-provider mega --format json
mega inference models --task embeddings --format json
```

Each row is one live model/Provider mapping. Prices are USD per one million tokens. A model can appear more than once when multiple Providers serve it.

## Step 2: make the call from the CLI

The shortest first call is:

```bash
mega inference chat mega/gpt-5.4-mini "Reply with exactly: MEGA is ready"
```

Omit the prompt to read it from standard input. This avoids putting sensitive input in shell history:

```bash
printf '%s' 'Reply with exactly: MEGA is ready' \
  | mega inference chat mega/gpt-5.4-mini
```

The result is an OpenAI-compatible Chat Completion. The CLI writes its `Inference-Id` and selected Provider to stderr so JSON on stdout remains parseable.

Use the other first-party routes in the same way:

```bash
mega inference responses mega/gpt-5.4-mini "Explain routed inference briefly"
mega inference embeddings BAAI/bge-m3 "MEGA routes inference"
```

Add `--stream` to Chat Completions or Responses. Human and quiet modes print text as it arrives; agent and JSON modes emit one JSON event per line.

## Step 3: call from Python

`InferenceClient` uses the same saved token and Router:

```python
import os

from megatensors import InferenceClient

client = InferenceClient(
    provider="auto",
    api_key=os.environ["MEGA_TOKEN"],
)

result = client.chat.completions.create(
    model="mega/gpt-5.4-mini",
    messages=[{"role": "user", "content": "Reply with exactly: MEGA is ready"}],
    max_tokens=32,
)

print(result.choices[0].message.content)
```

`provider="auto"` uses MEGA's default `fastest` strategy. `fastest`, `cheapest`, and `preferred` may also be passed explicitly. For embeddings:

```python
embedding = client.feature_extraction(
    "MEGA routes inference",
    model="BAAI/bge-m3",
)
print(embedding.shape)
```

## Use the OpenAI SDK

MEGA exposes an OpenAI-compatible base URL. No protocol adapter is required:

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

response = client.responses.create(
    model="mega/gpt-5.4-mini",
    input="Reply with exactly: MEGA is ready",
)
print(response.output_text)
```

Chat Completions and Embeddings use their normal OpenAI SDK methods:

```python
chat = client.chat.completions.create(
    model="mega/gpt-5.4-mini",
    messages=[{"role": "user", "content": "Hello"}],
)

vectors = client.embeddings.create(
    model="BAAI/bge-m3",
    input=["first input", "second input"],
)
```

## Use curl

```bash
curl https://inference.tensorplay.cn/v1/chat/completions \
  -H "Authorization: Bearer $MEGA_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{
    "model": "mega/gpt-5.4-mini",
    "messages": [{"role": "user", "content": "Reply with exactly: MEGA is ready"}]
  }' \
  -D -
```

Every success, error, and streaming response includes an `Inference-Id` header. Record it when reporting a failed call; MEGA does not need the prompt or response content to trace routing and billing state.

## Choose a Provider

Provider selection is encoded in the model ID and works identically in the CLI, SDK, and raw HTTP API:

| Model value | Behavior |
| --- | --- |
| `owner/model` | Fastest healthy Provider; the default. |
| `owner/model:fastest` | Explicit fastest selection. |
| `owner/model:cheapest` | Lowest standard output-token price, then latency. |
| `owner/model:preferred` | First healthy Provider in your account or organization preference order. |
| `owner/model:provider-slug` | Restrict the request to one Provider. |

The CLI appends the suffix for you:

```bash
mega inference chat mega/gpt-5.4-mini "Hello" --provider cheapest
mega inference chat mega/gpt-5.4-mini "Hello" --provider mega
```

MEGA can fail over only before response headers or the first streaming byte are sent. It never transparently retries after content starts, preventing duplicated output and duplicated settlement.

## Routed billing, custom keys, and organizations

The default `auto` billing mode follows the configured account: when the selected Provider has a saved custom key, MEGA swaps it in and records zero MEGA inference cost; otherwise MEGA preauthorizes Routed Inference and settles the Provider's standard price without a service markup. Your request code does not change after adding or rotating a saved key.

After you save an encrypted Provider key in **Settings → Inference Providers**, normal
`auto` calls use it automatically when that Provider is selected. To require a saved
key and fail instead of falling back to routed billing, select BYOK explicitly:

```bash
mega inference chat mega/gpt-5.4-mini "Hello" \
  --provider groq \
  --billing byok
```

MEGA authenticates the PAT, decrypts the saved Provider key only for dispatch, and records zero MEGA inference cost. The Provider bills the key owner directly. The key is never returned to the CLI or browser.

Use `--billing routed` to ignore a saved key for one call. Use `--billing byok` to require a saved key and fail instead of falling back to MEGA billing.

Eligible Team and Enterprise members with `write` or `admin` access can bill an organization:

```bash
mega inference responses mega/gpt-5.4-mini "Hello" \
  --bill-to research-lab
```

For raw HTTP or the OpenAI SDK, the equivalent headers are:

```text
X-Mega-Inference-Billing: byok
X-Mega-Bill-To: research-lab
```

Omit `X-Mega-Inference-Billing`, or set it to `auto`, for the normal automatic key-swap behavior.

Continue with [Routing and Provider Selection](/docs/inference-providers/routing), [Pricing and Billing](/docs/inference-providers/pricing-and-billing), or the [Router API Reference](/docs/inference-providers/api-reference). For failures, keep the `Inference-Id` and follow [Troubleshooting](/docs/inference-providers/troubleshooting).
