# MEGA Documentation


MEGA combines a model artifact runtime with a collaborative Hub. Use the same platform to publish models, datasets, and Spaces; automate repositories; run bounded compute Jobs; and load verifiable tensor artifacts from Python.

## What MEGA provides

- A binary `.mega` tensor shard format with aligned payloads and typed metadata.
- A `model.mega.index.json` manifest for multi-shard releases.
- Runtime helpers for lazy tensor access, PyTorch state dictionaries, model construction, and tokenizer loading.
- A Docker-like `mega jobs` interface for bounded containers and recurring compute.
- Three integration surfaces: the MEGA CLI, typed `MegaHubClient`, and versioned `/api/v1` Web API.
- Optional certificate-backed signing for source and artifact verification.
- `.megakv` sidecars for prompt or prefix cache reuse outside the model artifact.

## Common workflows

| Goal | Start here |
| --- | --- |
| Choose an integration surface | [CLI](/docs/megatensors/guides/cli), [Python SDK](/docs/megatensors/package_reference/hub_client), or [OpenAPI Explorer](/spaces/mega/openapi) |
| Create and version a Hub repository | [Repositories](/docs/hub/repositories) |
| Publish an interactive application | [Spaces](/docs/hub/spaces) |
| Run a container or recurring task | [Jobs](/docs/hub/jobs) |
| Browse the live HTTP contract | [OpenAPI Explorer](/spaces/mega/openapi) |
| Handle quotas and `429` responses | [Rate limits](/docs/hub/rate-limits) |
| Configure signed event delivery | [Webhooks](/docs/hub/webhooks) |
| Convert a local safetensors directory | [Quickstart](/docs/hub/quickstart) and [Conversion](/docs/megatensors/guides/conversion) |
| Understand artifact files and metadata | [Format](/docs/megatensors/package_reference/format) |
| Load tensors or models from Python | [Tensor Runtime API](/docs/megatensors/index) |
| Choose an I/O backend | [Backends](/docs/megatensors/package_reference/backends) |
| Sign a release | [Signing and Trust](/docs/hub/trust) |

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

Managing account keys through an access token requires the `account:keys` permission. Browser sessions can manage keys for the signed-in account directly.

## Next steps

1. Follow [Quickstart](/docs/hub/quickstart) to convert and load a model.
2. Read [Format](/docs/megatensors/package_reference/format) before writing custom metadata.
3. Use [Examples](/docs/megatensors/examples) to run generation and verification scripts.
