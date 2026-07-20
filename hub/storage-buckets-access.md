# Bucket Access Patterns

Use `mega://buckets/OWNER/NAME/PATH` with MEGA-native clients and
`hf://buckets/OWNER/NAME/PATH` with configured Hugging Face-compatible clients.
Buckets also have an S3-compatible gateway and can be attached to a Space as a
platform-managed volume. Choose the access pattern that matches the data
lifecycle.

| Need | Recommended approach |
| --- | --- |
| Immutable, reviewable release | Hub repository and tag |
| Mutable checkpoints or exports | Storage Bucket |
| Local-to-remote synchronization | `mega buckets sync` |
| One file copy | `mega buckets cp` |
| Python file access | `MegaFileSystem` |
| Existing S3 tool or SDK | [Bucket S3 Gateway](/docs/hub/storage-buckets-s3) |
| Files available inside a Space | Managed Bucket volume |

## Mount a Bucket locally

The MEGA mount client can expose a Bucket as a local filesystem for tools that
expect ordinary file paths. Install the published mount extension, then use its
help to choose the backend and start the mount:

```bash
mega extensions install mega-mount
mega extensions exec mount -- --help
```

Use a Bucket mount for mutable working data. Do not use it as the only copy of a
release artifact: Bucket paths do not have commits or tags. Stop the mount
cleanly before removing its local mount directory, and keep credentials outside
the mounted path.

## Attach a managed Bucket volume to a Space

A Space can attach a Bucket as a managed, read-only volume. The platform makes
the selected Bucket path available at the requested mount path; no client mount
process is needed inside the application. For example:

```bash
mega spaces volumes set OWNER/app \
  -v mega://buckets/OWNER/training-artifacts/checkpoints:/mnt/checkpoints:ro
```

The Space owner must be able to read the Bucket. Bucket volumes, like model and
dataset volumes, are read-only snapshots. Use the Space's persistent `/data`
storage for mutable files, or write to a Bucket through its client API with a
separately scoped credential. See [Space Storage](/docs/hub/spaces-storage)
for the full volume request format.

Use `--dry-run` before a destructive sync and authenticate with a scope and
organization role that permit the destination operation. See
[Storage Buckets](/docs/hub/storage-buckets).
