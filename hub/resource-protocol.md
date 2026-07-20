# Integrations

MEGA offers several public ways to work with Hub resources. Choose the
smallest interface that fits the job; this keeps credentials and permissions
easy to review.

## Choose an interface

| Interface | Best for | Reference |
| --- | --- | --- |
| Web application | Browsing, settings, collaboration, and guided workflows | The relevant Hub page |
| MEGA CLI | Local files, Git, scripts, resumable transfers, and Jobs | [CLI guide](/docs/megatensors/guides/cli) |
| Python SDK | Typed Python automation | [Python SDK](/docs/hub/sdk) |
| Hub API | Other languages and explicit HTTP integrations | [Hub API](/docs/hub/api) |
| MCP | Bounded AI-client actions and discovery | [MCP Server](/docs/hub/mcp) |
| Hugging Face-compatible client | Existing compatible tools | [Hugging Face Compatibility](/docs/hub/hugging-face-compatibility) |

## Resource identifiers

Repositories use an `OWNER/NAME` identity. Model repositories are addressed as
`OWNER/NAME`; dataset and Space routes add their respective type in the web
path. Buckets use `mega://buckets/OWNER/NAME/PATH` for MEGA-native CLI and
filesystem workflows. See [Hub Repositories](/docs/hub/repositories) and
[Storage Buckets](/docs/hub/storage-buckets) for complete examples.

Use returned canonical IDs and URLs rather than constructing identifiers from
display names. Pin automated reads to a commit or tag when reproducibility
matters.

## Authorization

Every integration uses the same resource permissions. A token scope permits a
class of action, while repository ownership, organization role, and resource
policy decide whether the action is allowed for a particular resource. Keep
tokens scoped to one automation boundary and never place a token in a
repository, card, or client-side application.

Browser-only security workflows may require an interactive session. For
automation, use a supported CLI, SDK, or documented API operation instead of
reusing browser cookies.

## Build reliable automation

- Follow opaque cursors exactly as returned; do not parse or alter them.
- Supply an idempotency key where the public API documents one.
- Handle `401`, `403`, `404`, `409`, `429`, and `5xx` responses explicitly.
- Respect `Retry-After` and use bounded exponential backoff with jitter.
- Preserve unknown response fields so additive API changes do not break your
  client.
- Use the [OpenAPI Explorer](/spaces/mega/openapi) as the source of truth for
  public request and response schemas.

## Supported boundary

MEGA does not provide a public endpoint for registering or operating underlying
compute or storage services. Publish applications with [Spaces](/docs/hub/spaces),
run containers with [Jobs](/docs/hub/jobs), and use the documented APIs for
their user-facing lifecycle.
