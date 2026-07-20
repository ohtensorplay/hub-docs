# Hugging Face Compatibility

MEGA supports core Hugging Face Hub workflows for models, datasets, Spaces, and
Storage Buckets. Existing `hf`, `huggingface_hub`, Transformers, Diffusers, and
Datasets workflows can target MEGA by changing the endpoint and token.

## Configure the endpoint

Set the endpoint before starting Python so compatible libraries read it during
import:

```bash
export HF_ENDPOINT=https://mega.tensorplay.cn
export HF_TOKEN=YOUR_MEGA_TOKEN

hf auth whoami
```

Use `repo:read` for private downloads, `repo:write` for uploads, and
`repo:delete` for deletion. `hf auth login --token "$HF_TOKEN"` is also
supported when a persisted credential is appropriate.

## Use the `hf` CLI

The standard repository lifecycle works against the configured endpoint:

```bash
hf repos create OWNER/demo --type model --exist-ok
hf upload OWNER/demo ./release . --commit-message "Publish release"
hf upload OWNER/demo ./release . --commit-description "Release notes in Markdown"
hf upload OWNER/demo ./release . --delete 'stale/**'
mega upload OWNER/demo ./release . --create-pr --commit-message "Propose release" \
  --commit-description "Explain the proposed change"
mega upload OWNER/demo ./watch --every 5
hf download OWNER/demo config.json --local-dir ./download
hf repos duplicate OWNER/demo OWNER/demo-copy --type model

hf repos branch create OWNER/demo next
hf repos tag create OWNER/demo v1.0 --revision main

hf buckets create OWNER/artifacts --private
hf buckets sync ./artifacts hf://buckets/OWNER/artifacts
```

Pass `--type dataset` or `--type space` to typed commands. Repository
visibility, branches, tags, files, and Bucket operations use the same MEGA
permission checks as native clients.

`--commit-description` is stored as the optional Markdown body of the immutable
commit and is returned by MEGA's native commit-history API. It is separate from
the Git commit subject used by `--commit-message`. Periodic `mega upload
--every` intentionally rejects a fixed description because every generated
commit needs its own description.

## Use Python libraries

`huggingface_hub` accepts either `HF_ENDPOINT` or an explicit endpoint:

```python
from huggingface_hub import HfApi, snapshot_download

api = HfApi(endpoint="https://mega.tensorplay.cn", token="YOUR_MEGA_TOKEN")
info = api.model_info("OWNER/demo")
root = snapshot_download("OWNER/demo", revision="main")
```

Libraries built on `huggingface_hub` can use the same configured endpoint:

```python
from transformers import AutoConfig

config = AutoConfig.from_pretrained("OWNER/demo")
```

Pin production downloads to a commit or tag instead of `main`.

## Supported boundary

The compatibility layer covers repository create, inspect, list, move,
duplicate, visibility, branches, tags, commits, file trees, and resolver
downloads; it also covers the matching Bucket lifecycle and sync workflows.
Repository-card metadata is parsed in the Hugging Face YAML format.

MEGA does not emulate every Hugging Face product. The `hf` compatibility write
endpoint does not accept implicit pull-request commits; use native `mega upload
--create-pr`, which creates a branch, performs the atomic upload, then opens a
native pull request. Dataset viewing and SQL exploration
are provided in MEGA's [Data Studio](/docs/hub/datasets-data-studio), rather
than through Hugging Face Dataset Viewer conversion APIs. Bucket S3 access and
managed Space volumes are available through MEGA's native public interfaces;
see [Bucket S3 Gateway](/docs/hub/storage-buckets-s3) and
[Space Storage](/docs/hub/spaces-storage). Use [Hub API](/docs/hub/api) and
the live [OpenAPI Explorer](/spaces/mega/openapi) to check the current public
contract.

For MEGA-native commands and SDKs, see the [CLI guide](/docs/megatensors/guides/cli)
and [Python SDK](/docs/hub/sdk).
