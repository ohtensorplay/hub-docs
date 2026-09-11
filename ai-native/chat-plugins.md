# Plugins in TensorPlayChat

TensorPlayChat can install packages that follow the Codex plugin protocol. A
plugin is a package, not a product-specific integration: its manifest declares
the presentation metadata and points to Skills, MCP servers, Apps, hooks, and
assets included with the package.

Cloudflare and GitHub are the first catalog entries. TensorPlayChat reads both
from the [official OpenAI plugin repository](https://github.com/openai/plugins)
and processes them through the same installer used for developer packages.
There is no Cloudflare- or GitHub-specific plugin schema.

## Package layout

The package root must contain `.codex-plugin/plugin.json`. Components use
paths relative to that root.

```text
my-plugin/
├── .codex-plugin/plugin.json
├── .mcp.json
├── .app.json
├── skills/
│   └── inspect/SKILL.md
├── hooks/
└── assets/
```

The manifest must provide a kebab-case name, semantic version, description,
publisher, and interface metadata. Referenced component and asset paths must
stay inside the package. See the
[Codex plugin build guide](https://developers.openai.com/plugins/build/plugins)
for the upstream field contract.

## Install from the catalog

Open **Chat → Plugins**, choose a package, review its publisher, capabilities,
external service, and data-sharing notice, then select **Install**. Installation
does not silently authorize an external account. Configure each declared MCP
connection separately and keep **Allow actions without asking** off unless the
server and requested action set are trusted.

The first catalog includes:

- Cloudflare, from `openai/plugins/plugins/cloudflare`;
- GitHub, from `openai/plugins/plugins/github`.

The package version, Skill instructions, connection endpoints, icons, and
other metadata come from the repository package rather than TensorPlayChat
source-code branches.

## Create a Skill through Chat

Open the **Skills** tab and select the **+** button. This starts the built-in
Skill Creator mode in the current conversation. Creation is deliberately
conversational rather than a one-shot form:

1. The Agent asks for concrete examples, intended triggers, and boundaries.
2. It proposes a concise Skill name, trigger description, workflow, and any
   genuinely reusable scripts, references, or assets.
3. It shows the final draft and asks you to reply **Approve and install** or
   **批准并安装** in a later message.
4. After approval, the account tool validates and installs the Skill.

The installer follows the same conventions as the built-in Codex
`skill-creator` and `skill-installer` workflows. It generates only the required
frontmatter fields, adds `agents/openai.yaml` UI metadata, enforces kebab-case
names and package size/path limits, and wraps the result in a normal plugin
package. The resulting source is marked **Created in Chat** and appears in the
same Skills list as Skills installed from catalog, repository, or ZIP packages.

Enabled Skill instructions are applied to Chat automatically; they do not
depend on the remote MCP toggle. Bundled references are loaded on demand when
an enabled Skill requests them and **Plugins** mode is on.
Generated scripts are stored as package resources but are not executed during
creation or by the browser runtime.

## Developer mode

Enable **Developer mode** at the bottom of the Plugins view to reveal
**New plugin**. Developer mode provides three inputs:

- **Repository** resolves a public HTTPS GitHub URL, optional ref, and optional
  plugin subdirectory. The installer pins the ref to a commit before reading
  package files.
- **Upload ZIP** accepts an archive containing one plugin root. The compressed
  limit is 8 MiB, the expanded limit is 24 MiB, and a package may contain at
  most 512 files.
- **Server URL** creates a protocol-compatible package around one remote HTTPS
  MCP server. Use this for a custom MCP server that does not ship a complete
  package yet.

Developer packages are not reviewed. The installer rejects ambiguous roots,
path traversal, private or local server addresses, invalid manifests, missing
referenced assets, oversized component files, and unsupported repository URL
forms. A successful validation does not establish that the package or server
is trustworthy.

## OAuth discovery and registration

For an OAuth-protected remote MCP server, TensorPlayChat performs the standard
MCP authorization sequence:

1. Call the MCP endpoint and read the `resource_metadata` URI from its
   `401 Unauthorized` challenge.
2. Fetch OAuth Protected Resource Metadata and pin the advertised resource to
   the configured MCP endpoint.
3. Discover OAuth Authorization Server Metadata and validate the issuer and
   endpoints.
4. Use a Client ID Metadata Document (CIMD) when the server advertises
   `client_id_metadata_document_supported`.
5. Otherwise, use Dynamic Client Registration (DCR) when a
   `registration_endpoint` is advertised.
6. Complete Authorization Code with PKCE and an exact resource indicator.

The discovery screen shows the authorization, token, registration, resource,
scope, and optional OpenID Connect metadata before authorization begins. CIMD
client metadata uses a public, opaque TensorPlayChat URL; it contains no
account identifier or credential. DCR client secrets, access tokens, refresh
tokens, and manually supplied bearer tokens are encrypted at rest and never
returned by the settings API. OAuth state is one-time and expires after ten
minutes. See the current
[MCP authorization specification](https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization)
for the registration priority and protocol requirements.

## Supported package surfaces

| Surface | TensorPlayChat behavior |
| --- | --- |
| `.codex-plugin/plugin.json` | Validated and used for identity, discovery, consent, and presentation |
| `skills/*/SKILL.md` | Installed, individually switchable, visible with included files, and automatically supplied as bounded Chat guidance; text resources can be loaded on demand while Plugins mode is on |
| Remote HTTP MCP | Discovered and invoked through the bounded Chat tool loop |
| `.app.json` | Parsed and shown as package integration metadata; a connector ID is used only when the corresponding Chat connector is available |
| Hooks and other included files | Preserved and shown with the package; the Web runtime does not launch arbitrary local processes |
| Channels | Not supported |

Local `stdio` MCP processes and arbitrary package executables cannot run in a
browser request. Publish a remote HTTPS MCP endpoint for Web Chat. This is a
runtime boundary, not a different plugin format: the package remains portable
to plugin hosts that provide those local capabilities.

## Permissions and removal

Each plugin and each Skill can be disabled without deleting the package.
Remote tools stay unavailable to the model while **Allow actions without
asking** is off. Disconnecting removes the stored connection credential;
removing the plugin deletes its account installation and OAuth state but does
not delete data at the external service. Revoke the external authorization at
that service as well when access should end completely.
