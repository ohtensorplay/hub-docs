# Your First Inference Provider Call

This guide takes you from a live model mapping to a successful routed request. MEGA runs the model on a healthy registered Provider; you do not deploy an endpoint first.

## Prerequisites

You need a MEGA account and a Personal Access Token (PAT) with the `inference:run` scope. Create the token under **Settings → Access Tokens**, copy it once, and keep it outside source control:

```bash
export MEGA_TOKEN="mega_..."
```

## Step 1: find a live model

In the browser, open the [Inference Provider model comparison](/inference/models) to
filter live model/Provider routes and compare price, context length, latency,
throughput, tool calling, and structured-output support.

The same live catalog is available through HTTP:

```bash
curl https://inference.tensorplay.cn/v1/models \
  -H "Authorization: Bearer $MEGA_TOKEN"
```

Use the public Hub catalog when you need MEGA-specific Provider, price, and
capability fields:

```bash
curl https://mega.tensorplay.cn/api/inference/models
```

Each model contains its live Provider mappings. Prices are USD per one million
tokens, and one model can expose multiple Providers or tasks.

## Step 2: call with the OpenAI SDK

Install the OpenAI SDK:

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

## Step 3: call with raw HTTP

```bash
curl -i https://inference.tensorplay.cn/v1/chat/completions \
  -H "Authorization: Bearer $MEGA_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{
    "model": "mega/gpt-5.4-mini",
    "messages": [{"role": "user", "content": "Reply with exactly: MEGA is ready"}]
  }'
```

Every success, error, and streaming response includes an `Inference-Id` header. Record it when reporting a failed call; MEGA does not need the prompt or response content to trace routing and billing state.

## Choose a Provider

Provider selection is encoded in the API's `model` field and works identically
in OpenAI-compatible SDKs and raw HTTP:

| Model value | Behavior |
| --- | --- |
| `owner/model` | Fastest healthy Provider; the default. |
| `owner/model:fastest` | Explicit fastest selection. |
| `owner/model:cheapest` | Lowest standard output-token price, then latency. |
| `owner/model:preferred` | First healthy Provider in your account or organization preference order. |
| `owner/model:provider-slug` | Restrict the request to one Provider. |

For example, send `mega/gpt-5.4-mini:cheapest` or
`mega/gpt-5.4-mini:mega` as the `model` value.

MEGA can fail over only before response headers or the first streaming byte are sent. It never transparently retries after content starts, preventing duplicated output and duplicated settlement.

## Routed billing, custom keys, and organizations

The default `auto` billing mode follows the configured account: when the selected Provider has a saved custom key, MEGA swaps it in and records zero MEGA inference cost; otherwise MEGA preauthorizes Routed Inference and settles the Provider's standard price without a service markup. Your request code does not change after adding or rotating a saved key.

After you save an encrypted Provider key in **Settings → Inference Providers**, normal
`auto` calls use it automatically when that Provider is selected. To require a saved
key and fail instead of falling back to routed billing, set a request header:

```python
byok = OpenAI(
    base_url="https://inference.tensorplay.cn/v1",
    api_key=os.environ["MEGA_TOKEN"],
    default_headers={"X-Mega-Inference-Billing": "byok"},
)
response = byok.responses.create(
    model="mega/gpt-5.4-mini:groq",
    input="Hello",
)
```

MEGA authenticates the PAT, decrypts the saved Provider key only for dispatch,
and records zero MEGA inference cost. The Provider bills the key owner directly.
The key is never returned to the SDK or browser.

Set `X-Mega-Inference-Billing: routed` to ignore a saved key for one call. Set
it to `byok` to require a saved key and fail instead of falling back to MEGA
billing.

Eligible Team and Enterprise members with `write` or `admin` access can bill an organization:

```text
X-Mega-Bill-To: research-lab
```

Omit `X-Mega-Inference-Billing`, or set it to `auto`, for the normal automatic key-swap behavior.

Continue with [Routing and Provider Selection](/docs/inference-providers/routing), [Pricing and Billing](/docs/inference-providers/pricing-and-billing), or the [Router API Reference](/docs/inference-providers/api-reference). For failures, keep the `Inference-Id` and follow [Troubleshooting](/docs/inference-providers/troubleshooting).
