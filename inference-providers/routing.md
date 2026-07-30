# Routing and Provider Selection

MEGA selects an eligible Provider route for each request from the public model
ID, task, account preferences, billing mode, price, and current availability.
The same model-selection values work across OpenAI-compatible SDKs and raw HTTP.

## Select a routing strategy

Append a suffix to the API request's `owner/model` value:

| Model ID | Selection behavior |
| --- | --- |
| `owner/model` | Fastest healthy route; this is the default. |
| `owner/model:fastest` | Explicit fastest selection. |
| `owner/model:cheapest` | Lowest output-token price for Chat and Responses, or lowest input-token price for Embeddings. |
| `owner/model:preferred` | First compatible Provider in the billing owner's configured order. |
| `owner/model:provider-slug` | Only that Provider's compatible healthy routes. |

For example:

```json
{"model": "mega/gpt-5.4-mini:cheapest", "input": "Hello"}
{"model": "mega/gpt-5.4-mini:preferred", "input": "Hello"}
{"model": "BAAI/bge-m3:mega", "input": ["Hello"]}
```

`auto` is a billing value, not a model suffix. Leave the model unsuffixed to
use the default fastest strategy.

## Candidate eligibility

Before ranking, the Router keeps only mappings that:

- match the exact Hub model ID and endpoint task;
- passed their latest conclusive protocol validation;
- belong to an enabled, non-blocked Provider;
- are currently available for new requests;
- are not disabled in the selected personal or organization settings;
- support the requested billing mode.

An explicit Provider suffix narrows this set but does not bypass health, validation, capacity, account policy, or billing requirements.

## How strategies are ranked

`fastest` uses recent latency, throughput, error rate, and availability. It is a
live ranking, not a permanent Provider label.

`cheapest` first compares the published standard price, then uses the normal health score as a tie breaker. BYOK may change what the Provider bills you, but it does not rewrite the catalog's standard price ranking.

`preferred` follows **Settings → Inference Providers → Settings**. Providers not listed in the explicit order remain eligible after ordered Providers unless disabled. Personal preferences apply by default; an explicit organization billing owner uses that organization's order and disabled list.

After changing a preference, allow the settings change to take effect before
retrying a request.

## Failover boundary

MEGA can try another eligible route after a connection failure or a retryable
status such as `408`, `425`, `429`, or `5xx`, but only before response output is
exposed.

For a streaming request, the first upstream byte closes the automatic failover window. MEGA never replays a prompt after streaming starts because that could duplicate output, tool calls, and billing. Applications should decide whether an interrupted stream is safe to retry.

## Sticky Provider sessions

Some upstream account pools benefit from conversation affinity. Supply a stable, non-secret signal:

```http
X-Mega-Session-Id: conversation-42
```

MEGA can also use compatible session fields in the request body as an affinity
signal. Use the dedicated header when your application needs a clear,
MEGA-specific session contract. Do not put credentials, email addresses, or
prompt text in either form of session identifier.

Stickiness does not override availability, disabled Providers, or an explicit
Provider suffix.

## Control routing per request

Routing strategy, billing mode, and billing owner are independent:

```bash
curl https://inference.tensorplay.cn/v1/responses \
  -H "Authorization: Bearer $MEGA_TOKEN" \
  -H "Content-Type: application/json" \
  -H "X-Mega-Inference-Billing: routed" \
  -H "X-Mega-Bill-To: research-lab" \
  -H "X-Mega-Session-Id: conversation-42" \
  --data '{"model":"mega/gpt-5.4-mini:preferred","input":"Hello"}'
```

See [Pricing and Billing](/docs/inference-providers/pricing-and-billing), [Custom Provider Keys](/docs/inference-providers/custom-provider-keys), and [Organization Billing](/docs/inference-providers/organization-billing) for those controls.
