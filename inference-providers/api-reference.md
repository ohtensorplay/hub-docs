# Router API Reference

MEGA provides an OpenAI-compatible inference API at
`https://inference.tensorplay.cn` and a Hub REST API at
`https://mega.tensorplay.cn/api`.

## Endpoints

| Method | Path | Purpose |
| --- | --- | --- |
| `GET` | `/health` | Router liveness; returns `204`. |
| `GET` | `/v1/models` | Authenticated live model list. |
| `POST` | `/v1/chat/completions` | Chat Completions task. |
| `POST` | `/v1/responses` | Responses task. |
| `POST` | `/v1/embeddings` | Embeddings task. |

All inference and model-list requests require a MEGA bearer token with `inference:run`. Unsupported paths or methods return an OpenAI-compatible `404` error.

## Request headers

| Header | Required | Meaning |
| --- | --- | --- |
| `Authorization: Bearer ...` | Yes | MEGA PAT; Provider credentials are rejected. |
| `Content-Type: application/json` | For `POST` | JSON request object. |
| `X-Mega-Inference-Billing` | No | `auto` by default, or `routed` / `byok`. |
| `X-Mega-Bill-To` | No | Eligible Team or Enterprise organization handle. |
| `X-Mega-Session-Id` | No | Stable, non-secret Chat or Responses affinity signal. |

The Router also recognizes `session_id` and `conversation_id` request headers, plus compatible session fields in the JSON body. Prefer `X-Mega-Session-Id` for a clear MEGA-specific contract.

## Model IDs

The JSON `model` field must use `owner/model` form and may include one selection suffix:

```text
owner/model
owner/model:fastest
owner/model:cheapest
owner/model:preferred
owner/model:provider-slug
```

Model IDs are limited to 256 characters and validated before routing. The selected mapping must match the endpoint's task.

## Response headers

Every Router response, including errors and preflight responses, includes:

```http
Inference-Id: inf_uuid
```

Dispatched Provider responses also include:

```http
X-Mega-Provider: provider-slug
```

Both are exposed to browser JavaScript through CORS. Responses use `Cache-Control: no-store` and `X-Content-Type-Options: nosniff`.

## Error shape

Router-generated failures use:

```json
{
  "error": {
    "message": "Human-readable detail",
    "type": "invalid_request_error",
    "param": null,
    "code": "machine_readable_code"
  }
}
```

Common outcomes are:

| Status | Typical code or condition | Action |
| ---: | --- | --- |
| `400` | `invalid_json`, `invalid_model`, `invalid_bill_to`, `invalid_billing_mode` | Correct the request before retrying. |
| `401` | `invalid_api_key` | Supply a valid MEGA PAT with `inference:run`. |
| `402` | Restricted account, insufficient credit, or monthly hard limit | Change billing owner or restore routed credit. |
| `403` | Organization role or billing-owner policy | Review membership and owner permissions. |
| `404` | `model_not_found`, a disabled explicit Provider, or unsupported path | Refresh the live task catalog, preferences, and model ID. |
| `409` | `provider_key_missing` or an unusable billing route | Save a key, choose routed billing, or change Provider. |
| `413` | `request_too_large` | Reduce the request body. |
| `429` | Upstream capacity or rate limit | Honor `Retry-After` when present and back off. |
| `502` | All pre-output candidates failed or a control-plane dependency is unavailable | Retry with backoff or pin a known healthy Provider. |

Once a Provider responds, compatible upstream success and error bodies are relayed after unsafe response headers are removed. Always inspect the HTTP status and keep `Inference-Id` even when the error body comes from a Provider.

## Size and timeout boundaries

The default request-body limit is 8 MiB and deployments may configure a different value between 1 KiB and 32 MiB. A buffered non-streaming Provider response is limited to 64 MiB.

The Router waits up to 30 seconds for upstream response headers and up to 10 minutes for a non-streaming response body. A streaming Provider must produce its first byte within 30 seconds. Client timeouts should be deliberate and may be shorter than these platform ceilings.

## Streaming

Chat Completions and Responses stream Provider Server-Sent Events when the JSON body contains `"stream": true`. Embeddings never stream.

The Router can fail over only before the first byte. After that boundary, a disconnect or timeout is returned as a partial stream and a client retry becomes a new request.

## Control-plane endpoints

The richer public catalog and authenticated settings APIs remain on the Hub hostname:

```text
GET /api/inference/models
GET /api/inference/providers/{provider}/models
GET /api/me/inference/overview
GET /api/me/inference/preferences
PUT /api/me/inference/preferences
GET /api/me/inference/provider-keys
PUT|DELETE /api/me/inference/provider-keys/{provider}
```

Organization settings replace `/api/me` with `/api/organizations/{handle}`. See [Model Catalog](/docs/inference-providers/model-catalog), [Custom Provider Keys](/docs/inference-providers/custom-provider-keys), and [Organization Billing](/docs/inference-providers/organization-billing) for schemas and permissions.
