# Hub API

The canonical Hub API is the language-neutral interface for repositories, accounts, organizations, community workflows, compute, billing, and platform integrations. Its routes are rooted directly at `/api`; use the live OpenAPI document for exact schemas and this guide for the shared conventions.

## Base URLs

| Surface | URL |
| --- | --- |
| Hub Web API | `https://mega.tensorplay.cn/api` |
| Spaces API | `https://mega.tensorplay.cn/api/spaces` |
| OpenAPI JSON | `https://mega.tensorplay.cn/.well-known/openapi.json` |
| Interactive explorer | [MEGA OpenAPI Space](/spaces/mega/openapi) |
| OpenAI-compatible inference | `https://inference.tensorplay.cn/v1` |

Inference uses a separate public Router hostname and error shape. See [Inference Providers](/docs/inference-providers/index) and the [Router API Reference](/docs/inference-providers/api-reference) before sending model traffic there.

## Authenticate

Send a fine-grained token as a bearer credential:

```bash
curl https://mega.tensorplay.cn/api/whoami \
  -H "Authorization: Bearer $MEGA_TOKEN"
```

Public repository and catalog reads can be anonymous. Private reads and mutations require authentication plus the necessary token scope and owner or organization permission.

Browser-session endpoints under `/api/me`, checkout, organization settings, and other interactive security flows may deliberately reject bearer-token automation. Use the CLI or typed client for supported automation instead of copying browser cookies.

## Send JSON correctly

JSON mutations require `Content-Type: application/json`:

```bash
curl -X POST https://mega.tensorplay.cn/api/repos \
  -H "Authorization: Bearer $MEGA_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{
    "repo_id": "alice/api-demo",
    "repo_type": "model",
    "private": true,
    "description": "Repository created through the Hub API",
    "tags": ["demo"]
  }'
```

The response status is `201` for a new repository. Use the returned repository and revision fields rather than reconstructing server identifiers.

## Repository paths and types

All four repository types use the same API collection:

```text
/api/repos/{owner}/{name}
```

The `repo_type` field distinguishes `model`, `dataset`, `space`, and `mcp`.
Browser presentation adds `/datasets/`, `/spaces/`, or `/mcps/` prefixes, but
those prefixes are not part of the canonical `/api/repos` identity.

Repository file reads resolve a branch, tag, or commit through:

```text
GET /api/repos/{owner}/{name}/resolve/{path}?revision=v1.0
```

Resolver responses include content length, ETag, SHA-256, and resolved-commit metadata. Byte ranges are supported for large artifacts; a valid range returns `206` with `Content-Range`.

## Paginate collections

Collection responses use an opaque continuation field, usually `next_cursor`:

```json
{
  "repos": [],
  "next_cursor": "opaque-value-or-null"
}
```

Pass the value back as `cursor` with the same filters and sort order. Do not decode, edit, compare, or persist assumptions about cursor contents. A `null` cursor means the collection is complete.

Limits differ by resource. Repository search accepts up to 500 items, while Papers and other collections use smaller caps. Follow the OpenAPI constraint for the specific operation.

## Handle errors

Most Hub failures return a JSON object with a human-readable `error` string and the matching HTTP status:

```json
{ "error": "repository not found" }
```

Common statuses are:

| Status | Meaning |
| ---: | --- |
| `400` | Invalid parameters, JSON, content type, or state transition. |
| `401` | Missing, expired, revoked, or otherwise invalid authentication. |
| `403` | Authentication succeeded but scope, role, or policy denied the action. |
| `404` | Resource is absent or intentionally hidden from the caller. |
| `409` | Name, revision, checkout, or concurrent-state conflict. |
| `413` | Request body or artifact exceeds the accepted limit. |
| `422` | A valid request asks for an unsupported compute capability. |
| `429` | A rate bucket is exhausted; wait for `Retry-After`. |
| `502` / `503` | A temporary MEGA service dependency is unavailable. |

OAuth, SCIM, Git LFS, and OpenAI-compatible inference follow their protocol-specific error contracts. Use the relevant OpenAPI response instead of assuming the generic Hub shape.

## Endpoint groups

| Workflow | Primary prefix |
| --- | --- |
| Identity and account | `/api/whoami`, `/api/me/*` |
| Repositories, files, refs, commits | `/api/repos/*` |
| Discussions and pull requests | `/api/repos/*/discussions/*` |
| Organizations and policy | `/api/organizations/*` |
| Jobs and schedules | `/api/jobs/*` |
| Spaces runtime | `/api/spaces/*` |
| Webhooks | `/api/me/webhooks/*` |
| Papers | `/api/papers/*` |
| Pricing and billing | `/api/pricing`, `/api/billing/*` |

## Choose a client

- Use the [MEGA CLI](/docs/megatensors/guides/cli) for shell workflows, resumable transfers, and CI-friendly exit codes.
- Use [MegaHubClient](/docs/hub/sdk) for typed Python repository, Job, and webhook methods.
- Use the raw API for other languages or when the typed client does not expose a newly deployed operation yet.
- Use [MCP](/docs/hub/mcp) when an AI client needs a deliberately selected tool surface rather than unrestricted HTTP access.

Generate clients only from the live OpenAPI document and preserve unknown response fields so additive server changes do not break consumers.
