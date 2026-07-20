# MEGA Documentation


MEGA combines a model artifact runtime with a collaborative Hub. Use the same platform to publish models, datasets, and Spaces; automate repositories; run bounded compute Jobs; and load verifiable tensor artifacts from Python.

## What MEGA provides

- A binary `.mega` tensor shard format with aligned payloads and typed metadata.
- A `model.mega.index.json` manifest for multi-shard releases.
- Runtime helpers for lazy tensor access, PyTorch state dictionaries, model construction, and tokenizer loading.
- A Docker-like `mega jobs` interface for bounded containers and recurring compute.
- Four integration surfaces: the MEGA CLI, typed Python clients, the canonical Hub Web API, and the OpenAI-compatible Inference Router.
- Optional certificate-backed signing for source and artifact verification.
- `.megakv` sidecars for prompt or prefix cache reuse outside the model artifact.

## Common workflows

| Goal | Start here |
| --- | --- |
| Choose an integration surface | [CLI](/docs/megatensors/guides/cli), [Python SDK](/docs/hub/sdk), [Hub API](/docs/hub/api), or [MCP](/docs/hub/mcp) |
| Create and version a Hub repository | [Repositories](/docs/hub/repositories) |
| Publish a model release | [Model Repositories](/docs/hub/models) |
| Publish a dataset | [Dataset Repositories](/docs/hub/datasets) |
| Write a useful model or dataset card | [Repository Cards](/docs/hub/repository-cards) |
| Propose or review a change | [Discussions and Pull Requests](/docs/hub/discussions) |
| Find repositories and follow publishers | [Search and Discovery](/docs/hub/discovery) |
| Curate models, datasets, Spaces, and papers | [Collections](/docs/hub/collections) |
| Link a release to verified research | [Paper Pages](/docs/hub/papers) |
| Work in a shared namespace | [Organizations](/docs/hub/organizations) |
| Set up phishing-resistant browser sign-in | [Passkeys](/docs/hub/passkeys) |
| Secure accounts and automation | [Account Security](/docs/hub/security) |
| Understand plans and compute credit | [Billing](/docs/hub/billing) |
| Publish an interactive application | [Spaces](/docs/hub/spaces) |
| Publish large versioned artifacts | [Xet](/docs/xet/index) |
| Synchronize mutable checkpoints and work files | [Storage Buckets](/docs/hub/storage-buckets) |
| Run a container or recurring task | [Jobs](/docs/hub/jobs) |
| Choose a public integration surface | [Integrations](/docs/hub/resource-protocol) |
| Make a first routed model call | [First Inference Provider Call](/docs/inference-providers/guides/first-api-call) |
| Configure routing, BYOK, and organization billing | [Inference Providers](/docs/inference-providers/index) |
| Browse the live HTTP contract | [Hub API](/docs/hub/api) and [OpenAPI Explorer](/spaces/mega/openapi) |
| Handle quotas and `429` responses | [Rate limits](/docs/hub/rate-limits) |
| Configure signed event delivery | [Webhooks](/docs/hub/webhooks) |
| Publish a first Hub repository | [Quickstart](/docs/hub/quickstart) |
| Convert a local safetensors directory | [Conversion](/docs/megatensors/guides/conversion) |
| Understand artifact files and metadata | [Format](/docs/megatensors/package_reference/format) |
| Load tensors or models from Python | [Tensor Runtime API](/docs/megatensors/index) |
| Choose an I/O backend | [Backends](/docs/megatensors/package_reference/backends) |
| Sign a release | [Signing and Trust](/docs/hub/trust) |
| Get help with an unavailable service | [Service status and support](/docs/hub/deployment) |

## Mental model

A MEGA release normally contains:

```text
qwen3.5-0.8b/
├── config.json
├── tokenizer.json
├── model.mega.index.json
├── model-00001-of-00002.mega
└── model-00002-of-00002.mega
```

The index file is the stable entry point. It records shard locations, shared metadata, and tensor routing. Runtime calls can accept either a single `.mega` file or the `.mega.index.json` file.

## Installation

Install the published CLI and Python package with one of the supported Python tool runners:

```bash
uv tool install megatensors
# or: pipx install megatensors
# or: python -m pip install megatensors
```

Confirm the CLI is available:

```bash
mega version
mega --help
```

For source development, clone the MEGA repository and use an editable install from its root:

```bash
python -m pip install -e ./megatensors
```

## Authentication

Use the browser device flow for an interactive login:

```bash
mega auth login
```

Automation can pass a token directly:

```bash
mega auth login --token "$MEGA_TOKEN"
```

The token is stored in the local MEGA config and reused by Hub download, upload, and snapshot commands.

See [Authentication](/docs/hub/authentication) for device flow, automation tokens, scopes, and public-key management.

Register only public SSH or GPG keys. The CLI rejects private-key material before contacting the service:

```bash
mega auth keys add ~/.ssh/id_ed25519.pub --name "Work laptop"
gpg --armor --export user@example.com > signing-key.asc
mega auth keys add signing-key.asc --type gpg --name "Release signing"
mega auth keys list
```

Managing account keys through an access token requires the `account:keys` permission. Browser sessions and service administrators can manage their own account keys directly.

## Next steps

1. Follow [Quickstart](/docs/hub/quickstart) to sign in, publish a small repository, and download its first revision.
2. Choose [Models](/docs/hub/models), [Datasets](/docs/hub/datasets), or [Spaces](/docs/hub/spaces) for the resource you want to publish.
3. Read [Authentication](/docs/hub/authentication), [Passkeys](/docs/hub/passkeys), and [Account Security](/docs/hub/security) before creating automation credentials.
4. Use [Examples](/docs/megatensors/examples) for focused runtime workflows or the [OpenAPI Explorer](/spaces/mega/openapi) for direct HTTP integration.
