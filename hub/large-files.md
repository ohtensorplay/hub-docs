# Large Files

Use MEGA's resumable upload and download commands for large model weights,
dataset archives, and Space assets. The client selects the appropriate
transfer method; you do not need to configure storage internals.

## Upload a release tree

```bash
mega upload-large-folder OWNER/REPOSITORY ./release --revision main
```

The command resumes completed transfer work when possible. Keep the local tree
stable until it finishes, and use a release tag after validating the published
revision.

## Download reliably

```bash
mega snapshot OWNER/REPOSITORY --revision v1.0 --local-dir ./release
```

Use an explicit tag or commit for production consumption. Retain the manifest,
configuration, and referenced files together so a reader can reproduce the
release.

## Avoid common mistakes

- Never add credentials or local caches to an upload tree.
- Prefer a [Storage Bucket](/docs/hub/storage-buckets) for mutable work files.
- Use `--dry-run` where a copy or sync command supports it.
- Retry an interrupted transfer with the same destination rather than creating
  several concurrent copies.
