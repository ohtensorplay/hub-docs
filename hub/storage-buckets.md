# Storage Buckets

Storage Buckets are mutable file stores for checkpoints, training artifacts,
media, exports, and Job inputs. A Bucket has an `owner/name` identity, but it
is not a Git repository: paths hold their current value and there are no
commits, branches, tags, or revisions.

Use a Bucket for files that change in place or a directory that must be synced
repeatedly. Use a [Hub Repository](/docs/hub/repositories) when reviewable
history, immutable revisions, or a repository card are part of the artifact
contract. For large, versioned repository artifacts, see [Xet](/docs/xet/index).

## Access and visibility

Bucket visibility controls who can read files. Ownership, organization roles,
resource groups, and token scopes control changes.

| Caller | Public Bucket | Private Bucket | Write or delete |
| --- | --- | --- | --- |
| Anonymous visitor | Read public files | No access | No |
| Owner | Read | Read | With the required token scope |
| Organization member | According to organization policy | According to role or resource group | According to role and scope |
| Service account | According to its organization permissions | According to its organization permissions | According to role and scope |

Private Buckets that a caller cannot access are presented as unavailable. Every
write requires authenticated access. Choose a region from the options shown
when creating a Bucket; availability depends on the owner and plan.

## Hugging Face compatibility

MEGA supports the core Bucket workflows exposed by current `huggingface_hub`.
Use `mega buckets` and `mega://buckets/...` for MEGA-native work, or configure
`HF_ENDPOINT` when migrating an existing Hugging Face workflow.

| Workflow | MEGA support |
| --- | --- |
| Create, list, inspect, move, and delete Buckets | Web, CLI, and Python API |
| List trees and path metadata; copy, remove, and sync files | CLI and Python API |
| Browse a directory and render its `README.md` | Web application |
| `hf buckets` and compatible Python clients | Supported with `HF_ENDPOINT` |
| `MegaFileSystem` paths | Supported with `mega://buckets/...` |
| S3-compatible gateway | Supported with per-Bucket credentials |
| Local and managed mounts | Supported through the mount client and Space volumes |
| Browser drag-and-drop upload | Not currently available |

Do not infer Hugging Face pricing, storage limits, or compliance claims from
protocol compatibility. See [Billing](/docs/hub/billing) for MEGA plans and
public pricing information.

## Create and inspect

Create a Bucket from [Storage](/storage) or with the CLI:

```bash
mega buckets create OWNER/training-artifacts --private --region us
mega buckets info OWNER/training-artifacts
mega buckets list OWNER
```

For a compatible Hugging Face CLI workflow:

```bash
export HF_ENDPOINT=https://mega.tensorplay.cn
export HF_TOKEN=YOUR_MEGA_TOKEN

hf buckets create OWNER/training-artifacts --private --region us --exist-ok
hf buckets info OWNER/training-artifacts
```

Only select a region offered by the create command or form. Before moving a
Bucket, read the command output and confirm how existing files are handled.

## Sync directories

Sync compares local and remote paths before it transfers data:

```bash
mega buckets sync ./checkpoints mega://buckets/OWNER/training-artifacts/checkpoints
mega buckets sync mega://buckets/OWNER/training-artifacts/checkpoints ./checkpoints
```

The compatible form uses `hf://buckets/...`:

```bash
hf buckets sync ./checkpoints hf://buckets/OWNER/training-artifacts/checkpoints
hf buckets sync hf://buckets/OWNER/training-artifacts/checkpoints ./checkpoints
```

Run `--dry-run` before a large synchronization. Use `--delete` only when the
destination must exactly mirror the source; deleted Bucket files do not have
repository history to restore from. Include and exclude filters help limit a
sync to the files you intend to change.

## Copy and remove files

`cp` supports local-to-Bucket, Bucket-to-local, repository-to-Bucket, and
Bucket-to-Bucket transfers. Writing from a Bucket into a repository is not
supported because repository changes must be made in an explicit commit.

```bash
mega buckets cp ./config.json mega://buckets/OWNER/training-artifacts/config.json
mega buckets cp mega://OWNER/model@main/model.safetensors \
  mega://buckets/OWNER/training-artifacts/model.safetensors
mega buckets rm OWNER/training-artifacts/reports/ --recursive --dry-run
```

A source directory ending in `/` copies its contents. Without the trailing
slash, the directory itself is copied into the destination.

## Python and filesystem access

The Hugging Face-compatible API works with an explicit endpoint:

```python
from huggingface_hub import HfApi

api = HfApi(endpoint="https://mega.tensorplay.cn", token="YOUR_MEGA_TOKEN")
bucket = api.create_bucket("OWNER/training-artifacts", private=True, region="us", exist_ok=True)
api.batch_bucket_files(bucket.bucket_id, add=[("./metrics.json", "runs/metrics.json")])
```

Use `MegaFileSystem` when a Python library accepts an fsspec-compatible
filesystem:

```python
from megatensors._hub import MegaFileSystem

fs = MegaFileSystem(token="YOUR_MEGA_TOKEN")
with fs.open("buckets/OWNER/training-artifacts/metrics.json", "wb") as file:
    file.write(b'{"loss": 0.12}')
```

`batch_bucket_files` can apply multiple changes, but it is not transactional:
an earlier change can remain visible if a later operation fails. Split risky
changes and use dry runs where available.

For S3-compatible clients, see [Bucket S3 Gateway](/docs/hub/storage-buckets-s3).
For filesystem-style and Space mounts, see
[Bucket Access Patterns](/docs/hub/storage-buckets-access).

## Security guidance

- Keep reproducible releases in repositories and mutable working data in
  Buckets.
- Prefer a private Bucket for logs, intermediate data, generated artifacts, and
  any file that is not ready to distribute.
- Use a token with only the required scope, and never put it in a Bucket file.
- Verify the destination before `sync --delete` or a recursive remove.
- Record a repository revision alongside any Bucket input used for an
  experiment so the result remains explainable.
