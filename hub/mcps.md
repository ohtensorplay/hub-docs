# MCP Marketplace

The MCP Marketplace lets MEGA users publish a public MCP repository backed by
a separate managed private runtime and earn Mega Coins when other users make
successful tool calls.

The marketplace catalogue is separate from the free official MEGA tools, but
all clients invoke marketplace tools through the
[official MEGA MCP Server](/docs/hub/mcp).

## URLs

| Surface | URL |
| --- | --- |
| Official MEGA MCP page and endpoint | `https://mega.tensorplay.cn/mcp` |
| Marketplace | `https://mega.tensorplay.cn/mcps` |
| Marketplace detail, files, and Community | `https://mega.tensorplay.cn/mcps/<namespace>/<name>` |

Every ChatGPT, Codex, CLI, Plugin, and generic MCP client connects only to
`https://mega.tensorplay.cn/mcp`. The plural `/mcps` tree is the repository and
marketplace surface, never an independent protocol endpoint.

## Gateway workflow

The official endpoint exposes three stable tools:

1. `mcp_market_search` finds public listings.
2. `mcp_market_details` reads the selected listing and its current downstream
   tool schemas for free.
3. `mcp_market_call` invokes one selected tool with an explicit
   `max_mega_coins` value.

Marketplace listings therefore do not expand the client's reviewed top-level
tool catalogue. Publisher descriptions, schemas, and results are untrusted
content and do not broaden the caller's authorization.

## Repository and runtime are separate

Each listing has two independent resources:

- A **public MCP repository** owns the marketplace name and page. It has the
  first-class `repo_type=mcp` value and the same README, files, versions, Git,
  and Community workflow as a Model repository.
- A **private Space runtime** executes MCP requests. It is bound internally to
  the listing and is not linked or identified on the public repository page.

The public repository is where publishers version documentation and optional
local companions. An MCP that needs a CLI, Skill, Plugin, or client
configuration can publish those files alongside its README. The private
runtime source is not part of this file tree.

Install a listing's versioned companion snapshot with the MEGA CLI:

```bash
mega mcp search xpuoj
mega mcp info mega/xpuoj
mega mcp install mega/xpuoj
```

The same repository can be created and updated explicitly:

```bash
mega repos create alice/my-tools --type mcp
mega upload alice/my-tools ./README.md README.md --type mcp
```

`mega mcp install` stores files under
`~/.local/share/mega/mcp/<namespace>/<name>`, requires a README, and replaces
files atomically. It never executes publisher code; review the README before
enabling a CLI, Skill, or Plugin.

## Mega Coins

Every registered account receives 10 Mega Coins once.

- A publisher chooses an integer price per successful `tools/call`.
- Tool discovery, connection setup, and the publisher's own calls are free.
- A failed or over-limit tool call returns the reserved coins.
- The seller receives the coins after a successful response.
- Coins cannot currently be purchased. Selling MCP calls is the only way to
  earn more.

The official MEGA tools are not marketplace listings and do not charge coins.
Only successful calls routed through `mcp_market_call` charge the selected
listing price.

## Hosting contract

Marketplace MCP runtimes use a personal, private Space controlled by MEGA:

| Limit | Contract |
| --- | --- |
| Hardware | CPU Basic: 0.3 vCPU and 384 MiB |
| Request body | At most 1 MiB |
| Request duration | At most 30 seconds |
| Docker runtime | Node 22 starter, Streamable HTTP at `/mcp` |
| Gradio runtime | Managed MCP endpoint at `/gradio_api/mcp/` |

The private runtime must remain on CPU Basic while listed. Unlist the MCP
before changing that hardware. Marketplace publishing does not accept custom
upstream URLs, seller-provided Worker bindings, or public Spaces.

The official Hub tools are not listings and remain free. A source-controlled
allowlist lets `mega` listings use fixed Worker service bindings; ordinary
publishers cannot select this runtime or provide a Worker URL. Allowlisted
listings still follow their displayed marketplace price.

## Authentication and routing

The initial `/mcp` OAuth connection can use `repo:read`. Search and tool-schema
inspection are free. A paid call requires `mcp:use`; clients such as ChatGPT
can obtain it through a tool-level second OAuth prompt when
`mcp_market_call` returns an insufficient-scope challenge.

Hub then rechecks the OAuth token, the per-call maximum, listing price, balance,
rate limits, and runtime policy. It reserves the required coins and routes one
call to the bound private runtime. Caller authorization headers and cookies are
not forwarded to the publisher's runtime. Failed calls are refunded.

The application inside the private Space does not implement MEGA account
authentication. Hub is the public router and security boundary.

## First MEGA listing: XPUOJ

[`mega/xpuoj`](/mcps/mega/xpuoj) publishes its plugin README, CLI companion,
Skill, manifest, and artwork together. A fixed MEGA Worker binding handles
remote calls. Account-specific actions still need the local XPUOJ CLI or
plugin and the user's existing browser sign-in.
