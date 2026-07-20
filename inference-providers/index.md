# Inference Providers

MEGA Inference Providers is a routed, OpenAI-compatible inference service at `inference.tensorplay.cn`. Use one MEGA token to call models across supported Providers, choose a routing strategy, or use your own Provider key. Availability, performance, and pricing are live route properties, so start integrations from the current model catalog.

Routed calls include monthly credit: Community users receive $0.10, PRO users receive $2.00, and Team or Enterprise organizations receive $2.00 per billed seat. See [Pricing and Billing](/docs/inference-providers/pricing-and-billing) for eligibility, settlement, and spending limits.

## Choose a path

| Goal | Start here |
| --- | --- |
| Make the first request | [Your First Inference Provider Call](/docs/inference-providers/guides/first-api-call) |
| Compare live models, Providers, price, and capabilities | [Model Catalog](/docs/inference-providers/model-catalog) |
| Understand `fastest`, `cheapest`, `preferred`, and failover | [Routing and Provider Selection](/docs/inference-providers/routing) |
| Configure Routed Inference credit and spending limits | [Pricing and Billing](/docs/inference-providers/pricing-and-billing) |
| Add or rotate an external Provider credential | [Custom Provider Keys](/docs/inference-providers/custom-provider-keys) |
| Charge a Team or Enterprise workspace | [Organization Billing](/docs/inference-providers/organization-billing) |
| Integrate a client | [Clients and SDKs](/docs/inference-providers/guides/clients) |
| Look up endpoints, headers, and errors | [Router API Reference](/docs/inference-providers/api-reference) |
| Review token, content, and Provider data boundaries | [Security and Privacy](/docs/inference-providers/security) |
| Investigate a failed request | [Troubleshooting](/docs/inference-providers/troubleshooting) |

## Public endpoints

| Method | Endpoint | Task |
| --- | --- | --- |
| `GET` | `https://inference.tensorplay.cn/v1/models` | Authenticated live model catalog. |
| `POST` | `https://inference.tensorplay.cn/v1/chat/completions` | OpenAI Chat Completions. |
| `POST` | `https://inference.tensorplay.cn/v1/responses` | OpenAI Responses. |
| `POST` | `https://inference.tensorplay.cn/v1/embeddings` | OpenAI Embeddings. |

The initial public release does not expose image, video, Gemini-native, or Anthropic-native APIs.

The public [model comparison page](/inference/models) expands every validated
model into its live Provider routes and exposes the same price, context,
capability, latency, and throughput fields as the catalog API. `fastest` and
`cheapest` badges are computed within each model and task.

## Authentication

Send a MEGA PAT with `inference:run`:

```http
Authorization: Bearer mega_...
```

Use a MEGA PAT as the bearer token. Provider credentials are configured separately and are never accepted as public MEGA bearer tokens. Token and membership changes can take up to 60 seconds to reach every inference request.

## Model and Provider selection

Model IDs use `owner/model[:selection]`:

| Selection | Candidate order |
| --- | --- |
| no suffix or `:fastest` | Recent first-token latency, throughput, error rate, and available capacity. |
| `:cheapest` | Standard output-token price, then latency. |
| `:preferred` | User or organization Provider order, skipping disabled and unhealthy Providers. |
| `:provider-slug` | Only healthy Deployments registered under that Provider. |

Within one Provider, MEGA ranks healthy endpoints using available capacity, error rate, throughput, and recent latency. A route temporarily disappears when it is unavailable or fails compatibility checks. See [Routing and Provider Selection](/docs/inference-providers/routing) for the complete eligibility and failover rules.

Only routes that pass MEGA's protocol, latency, and advertised-capability checks appear as live. The default compatibility standard requires Chat Completions and Responses to produce a first token within five seconds and Embeddings to finish within 30 seconds. Failed routes are removed from selection until they pass again.

Task-specific request examples are available for [Chat Completions](/docs/inference-providers/tasks/chat-completions), [Responses](/docs/inference-providers/tasks/responses), and [Embeddings](/docs/inference-providers/tasks/embeddings).

## Automatic key selection and Routed Inference

With no billing header, the Router uses `auto`:

```http
X-Mega-Inference-Billing: auto
```

For each selected Provider, the Hub first uses an eligible personal or organization custom key when one is configured. If no key is available, the same request falls back to Routed Inference. This matches the Settings page: adding or rotating a key changes future automatic calls without changing application code.

Force MEGA billing for one request with `X-Mega-Inference-Billing: routed`. Force a saved key, with no routed fallback, with `X-Mega-Inference-Billing: byok`.

The Hub preauthorizes a bounded maximum cost before dispatch. After completion, it reconciles the Provider's usage and standard price, settles the actual integer nano-USD amount, and releases the unused reservation. MEGA applies no additional inference markup.

Some Providers report final usage asynchronously. If MEGA cannot confirm the final cost within approximately 30 minutes, it releases the reservation and does not charge the account for that request.

## Custom Provider Key (BYOK)

Save a personal key or an organization key under **Settings → Inference Providers**. Keys are encrypted at rest and excluded from logs, analytics, catalog responses, and error messages.

Require BYOK per request:

```http
X-Mega-Inference-Billing: byok
```

MEGA still authenticates the PAT and applies account preferences and permissions before dispatch. The same key selection happens automatically when the header is omitted and an eligible saved key exists. MEGA records route and token metrics but charges zero MEGA inference cost; the Provider bills the key owner. BYOK is available only for Providers that support custom keys.

## Organization billing

Set the organization handle explicitly:

```http
X-Mega-Bill-To: research-lab
```

The organization must be on Team or Enterprise, and the caller must have `write` or `admin` membership. Organization Provider order, disabled list, custom keys, and monthly hard limit take precedence for that request. Without the header, the personal account is the billing owner.

## Streaming and failover

Chat Completions and Responses preserve Provider Server-Sent Events. The Router may try another healthy candidate only before response headers or the first stream byte are exposed. Once output begins, the request is never transparently replayed.

All responses include:

```http
Inference-Id: inf_uuid
```

Any response dispatched to a Provider also exposes the selected Provider, including BYOK and upstream error responses:

```http
X-Mega-Provider: provider-slug
```

Browsers may read both headers through CORS. Keep `Inference-Id` in application logs; do not add prompt content to an incident report.

## Sticky Provider sessions

Some OAuth-backed upstream accounts require conversation affinity. Pass a stable, non-secret session signal in the dedicated header:

```http
X-Mega-Session-Id: conversation-42
```

The Router hashes this header together with the billing owner and model, then forwards only an irreversible `mega_…` session identifier. It can also derive affinity from `previous_response_id`, `session_id`, `conversation_id`, or `prompt_cache_key` in a compatible JSON body; those body fields remain part of the upstream protocol and are forwarded unchanged. Use `X-Mega-Session-Id` when the original application signal must not reach the Provider.

Stickiness does not override health, a disabled Provider, capacity admission, or an explicit Provider suffix.

## Privacy and observability

MEGA does not retain prompts, responses, embedding inputs, or tool arguments. It retains only content-free request metadata needed for routing, reliability, abuse prevention, and billing, such as request hashes, byte and token counts, model, Provider, cost, latency, and status. Authorization headers, cookies, and Provider keys are redacted. The selected Provider necessarily receives the content required to perform inference; review that Provider's data policy before use.

## CLI reference

```bash
mega inference models --task chat-completions
mega inference chat OWNER/MODEL "Hello" --provider fastest
mega inference responses OWNER/MODEL "Hello" --provider preferred
mega inference embeddings OWNER/MODEL "text to embed"
```

Shared request options are:

| Option | Meaning |
| --- | --- |
| `--provider` | `auto`, `fastest`, `cheapest`, `preferred`, or a Provider slug. |
| `--billing` | `auto` (saved key, then routed), `routed`, or `byok` (key required). |
| `--bill-to` | Eligible organization handle. |
| `--session-id` | Stable conversation affinity signal for Chat/Responses. |
| `--token` | One request's MEGA PAT; otherwise use `MEGA_TOKEN` or the active login. |
| `--stream` | Stream Chat/Responses as text or JSON Lines. |

For the runnable setup path, start with [Your First Inference Provider Call](/docs/inference-providers/guides/first-api-call).
