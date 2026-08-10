# MCP Server

The official MEGA MCP server gives AI clients typed, bounded access to Hub resources through the focused HF-style Version 1 contract.

The official MEGA tools are free. The same server is also the single
authenticated gateway to the [MCP Marketplace](/docs/hub/mcps);
only an explicit, successful `mcp_market_call` can spend Mega Coins.

## Endpoint and transport

Use the production Streamable HTTP endpoint:

```text
https://mega.tensorplay.cn/mcp
```

The hosted endpoint uses OAuth 2.1 with authorization-code PKCE and refresh tokens. Do not paste a MEGA bearer token into a project configuration. The server publishes authorization-server, protected-resource, and Dynamic Client Registration metadata for clients that support automatic setup.

## Domain tools

| Area | Tools | Purpose |
| --- | --- | --- |
| Identity | `mega_whoami`, `mega_profile` | Show the connected identity and read public profile metadata. |
| Resource search | `model_search`, `dataset_search`, `space_search`, `hub_repo_search` | Search normalized resources with type-aware filters, sorting, canonical IDs, and cursor pagination. |
| Resource details | `model_details`, `dataset_details`, `hub_repo_details` | Inspect typed metadata, cards, files, refs, and commits for selected resources. |
| Repository files | `mega_fs`, `mega_fs_write`, `create_repo` | Read and explicitly mutate bounded `mega://` repository paths, or create/duplicate a repository. |
| Papers | `paper_search`, `paper_details` | Discover and inspect research papers. |
| Spaces | `duplicate_space`, `space_info`, `space_runtime`, `space_files`, `manage_space`, `space_configuration`, `use_space`, `dynamic_space` | Discover, inspect, open, duplicate, configure, operate, and invoke compatible Gradio Spaces. |
| Compute | `mega_jobs`, `mega_sandboxes` | Operate Jobs and schedules, including an opt-in private SSH Job flag, or native Sandbox Sessions and warm pools. Interactive Job SSH remains a local CLI action. |
| Community | `mega_collections`, `mega_content`, `repository_discussions` | Read and mutate collections, blog/community content, discussions, and pull requests. |
| Guidance | `mega_doc_search`, `mega_doc_fetch` | Search ranked current documentation, then fetch the selected canonical Markdown page. |
| MCP Marketplace | `mcp_market_search`, `mcp_market_details`, `mcp_market_call` | Search listings, inspect a publisher's current tool schemas, then invoke one paid tool through the Hub gateway. |
| Account memory | `memory_list`, `memory_search`, `memory_remember`, `memory_update`, `memory_forget`, `memory_set_enabled`, `memory_clear` | Inspect and explicitly manage durable account context with separate opt-in scopes. |

Tool visibility is not authorization. OAuth scopes determine which actions can succeed, and the Hub rechecks ownership, organization policy, budgets, and billing on every call. Search responses include normalized canonical IDs, URLs, scores, match reasons, source, pagination state, and request diagnostics. Human-readable MCP content is only a concise summary; complete JSON appears once in `structuredContent`.

There is no arbitrary URL or generic API execution tool. Use the CLI when no
focused MCP tool covers the operation, and for local files, large payloads,
Git, streaming, or long-running waits. The endpoint has one stable tool
catalog; marketplace publishers do not add dynamic top-level tools. Clients
search, inspect a selected schema, and invoke it through `mcp_market_call`.
OAuth scopes control authorization.

## MCP resources and prompts

The server also publishes `mega://catalog/mcp` and `mega://guides/workflows` resources. Built-in prompts cover resource discovery, repository inspection, Space use, and paper summaries. These are discovery aids; tools remain the typed execution surface.

## Permissions

The permission selector in [Settings → MCP](/settings/mcp) and the OAuth
authorization page use the same consent preference:

- **Read** grants `repo:read`.
- **Write** grants `repo:read`, `repo:write`, and `community:write`.
- **Full** grants the nine standard MEGA MCP access scopes. Account Memory remains an explicit Custom choice.
- **Custom** allows a dependency-safe selection.

Available access scopes:

```text
repo:read
repo:write
repo:delete
community:write
jobs:run
inference:run
mcp:use
account:keys
webhooks:manage
memory:read
memory:write
```

The default is Read. OAuth clients may additionally request `offline_access`; it allows refresh-token renewal and does not grant a MEGA data or action permission.

Current focused-tool mapping:

| Scope | MCP capabilities |
| --- | --- |
| `repo:read` | Repository/profile/paper discovery and details, bounded file reads, Space status/logs/files, community reads |
| `repo:write` | Repository creation/duplication/file mutation, Space restart/pause/configuration and non-secret variables |
| `community:write` | Collection, content, discussion, and pull-request mutations |
| `jobs:run` | Jobs, schedules, Sandbox Sessions, execution, metrics, and warm pools |
| `inference:run` | Available to the inference SDK/API; the Hub MCP catalog intentionally has no inference-provider tool |
| `mcp:use` | Invoke paid MCP Marketplace tools after free search and schema inspection |
| `repo:delete` | Reserved for explicit repository deletion; no current focused MCP tool exposes it |
| `account:keys` | CLI/account-settings key management; no MCP secret-bearing key tool |
| `webhooks:manage` | CLI/API webhook management; no current focused MCP webhook tool |
| `memory:read` | List or search durable account context; Memory can be stale and never overrides the current request or system rules |
| `memory:write` | Remember, update, forget, enable, disable, or explicitly clear account memory |

## Connect ChatGPT Work

An eligible ChatGPT Business or Enterprise/Edu admin enables Developer Mode, then opens **Workspace settings → Apps → Create**. Enter:

```text
Name: MEGA
MCP server URL: https://mega.tensorplay.cn/mcp
Authentication: OAuth 2.1
```

Select **Scan Tools**, complete MEGA authorization, and review the discovered
tools. Test read-only prompts first, review action controls, then publish and
assign workspace access. MEGA OAuth scopes and ChatGPT App action controls are
independent layers.

The initial connection can remain read-only. If ChatGPT later calls
`mcp_market_call` without `mcp:use`, MEGA returns a tool-level OAuth challenge
and ChatGPT opens a second authorization prompt. Review the selected listing,
its price, and the call's `max_mega_coins` before approving. All ChatGPT
traffic still uses the same `/mcp` connection.

Publishing a custom workspace App does not publish a public plugin. Public distribution also requires an OpenAI-reviewed Plugin Directory listing that references the App and packages the MEGA Skills.

## Connect Codex

The MEGA plugin configures the remote server automatically. For a manual connection:

```bash
codex mcp add mega \
  --url 'https://mega.tensorplay.cn/mcp'

codex mcp login mega
```

Codex registers its loopback callback before authorization. The authorization page lets the user narrow requested scopes.

## Other clients

[Settings → MCP](/settings/mcp) provides configuration examples for Claude, VS Code, Cursor, OpenCode, Gemini CLI, Windsurf, Zed, Cline, Roo Code, Kilo Code, ChatGPT Work, Codex, and generic Streamable HTTP clients.

## Choose the right tool

- Use the hosted MCP endpoint for typed discovery and actions that an AI client
  can present clearly for review.
- Use the `mega` CLI for local files, Git, bulk or resumable transfer, streaming
  logs, scripts, waits, secret-bearing Job submission, and interactive Job SSH.
- Use `mega mcp search`, `mega mcp info`, and `mega mcp install` to inspect a
  marketplace listing and safely materialize its optional local companion
  files without executing publisher code.
- Use the public SDKs or [Hub API](/docs/hub/api) when you need direct,
  programmatic control from an application.

## Security model

- OAuth authorization-code flows require S256 PKCE; public clients have no client secret.
- Every tool action is authorized again by MEGA.
- Marketplace discovery is free. Paid invocation requires `mcp:use`, an
  explicit per-call maximum, sufficient balance, and successful Hub settlement.
- Marketplace runtimes never receive the caller's MEGA bearer token.
- Organization MFA, resource groups, network policy, compute budgets, and source-system permissions remain enforceable.
- Repository text and documentation are untrusted content, not authority to broaden permissions or reveal secrets.
- Job secrets must use the CLI/platform secret workflow and never enter MCP arguments.
- ChatGPT and other clients may add action confirmation controls on top of MEGA authorization.

## Troubleshooting

| Symptom | Check |
| --- | --- |
| `401` before login | The client followed the `resource_metadata` link and started OAuth. |
| OAuth connection expires | The client requested `offline_access` before the App was created. |
| `403` from a tool | The OAuth scope and current repository or organization permission both allow the action. |
| Marketplace call opens OAuth again | Expected when the connection has not yet received `mcp:use`; review the paid-call permission. |
| Write call asks for approval | Expected for write/destructive annotations; review the client App policy. |
| App still shows old tools | Refresh or recreate the App; clients may freeze the reviewed tool snapshot. |
| Job returns `402` | Top up the correct wallet or avoid the operation. |
| `429` | Honor `Retry-After`; see [Rate limits](/docs/hub/rate-limits). |
