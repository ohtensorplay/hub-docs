# Model Catalog

The Inference Provider catalog lists model and Provider routes that are healthy, validated, and available for new requests. Use it instead of assuming that every model repository or every Provider can serve every task.

## Browse live routes

Open [Inference Models](/inference/models) to compare one row per model, Provider, and task. The table exposes price, context length, first-token latency, throughput, tool support, and structured-output support.

The same catalog is available without authentication from the Hub API:

```bash
curl https://mega.tensorplay.cn/api/inference/models
curl https://mega.tensorplay.cn/api/inference/providers/groq/models
```

The Provider-filtered route returns the same response shape with only that Provider's live mappings. Hub catalog responses may be cached for up to 60 seconds.

The OpenAI-compatible Router exposes an authenticated model list:

```bash
curl https://inference.tensorplay.cn/v1/models \
  -H "Authorization: Bearer $MEGA_TOKEN"
```

Use the Hub catalog when you need MEGA-specific route details. Use `/v1/models` when an OpenAI-compatible client expects the standard model-list endpoint.

## Filter live routes

Use [Inference Models](/inference/models) to filter by task or Provider and sort
by latency, throughput, and price. For automation, read the Hub catalog JSON or
the Provider-filtered endpoint shown above; do not infer availability from a
repository tag.

## Read a catalog record

Each model contains a `providers` array. Important fields are:

| Field | Meaning |
| --- | --- |
| `id` | Public Hub model ID in `owner/model` form. |
| `provider` | Provider slug accepted as a model suffix. |
| `task` | `chat-completions`, `responses`, or `embeddings`. |
| `pricing.input` | Standard input price in USD per one million tokens. |
| `pricing.output` | Standard output price in USD per one million tokens. |
| `context_length` | Validated context window for this mapping. |
| `supports_tools` | Whether the current validation permits tool calling. |
| `supports_structured_output` | Whether the current validation permits structured output. |
| `supports_custom_key` | Whether the Provider can accept a saved custom key. |
| `verified` | Whether MEGA has verified the Provider route. Organization ownership alone does not set this field. |
| `provider_organization` | Public organization identity for the route operator, including its independent `verified` and `official` status when present. |
| `first_token_latency_ms` | Best current first-token latency for an available route, when measured. |
| `throughput` | Best current token throughput for an available route, when measured. |

Prices and performance are route properties, not permanent model properties. Compare the route you intend to use and refresh long-lived caches.

## Why a route appears or disappears

A route is published only when all of the following are true:

- the public model repository exists at the same `owner/model` ID and its `main` branch contains `README.md`;
- the model mapping is live and has not been deleted;
- the Provider is enabled and not blocked by risk policy;
- an eligible route is available, has capacity, and supports the requested task;
- the mapping supports the requested task.

Repository tags help users discover a model but do not make it callable by
themselves. If a route becomes unavailable or incompatible, it is removed from
active selection until it can serve the task again. Check the live catalog and
[Troubleshooting](/docs/inference-providers/troubleshooting) when a route you
previously used disappears.

## Choose the next step

- Read [Routing and Provider Selection](/docs/inference-providers/routing) before choosing `fastest`, `cheapest`, or `preferred`.
- Read the relevant task guide for [Chat Completions](/docs/inference-providers/tasks/chat-completions), [Responses](/docs/inference-providers/tasks/responses), or [Embeddings](/docs/inference-providers/tasks/embeddings).
- Check [Pricing and Billing](/docs/inference-providers/pricing-and-billing) before setting spending limits or forcing a billing mode.
