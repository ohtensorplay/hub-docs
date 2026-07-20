# Xet Storage Overview

Xet makes large files practical in versioned MEGA model, dataset, and Space
repositories. It works with the existing repository revision model: commits,
branches, tags, visibility, and repository permissions remain the way you
organize and share a release.

## When to use it

Use the normal repository workflow for model weights, dataset shards, media,
and other release artifacts that should be tied to a commit or tag. The MEGA
CLI provides resumable upload and download commands for large trees.

```bash
mega upload-large-folder OWNER/REPOSITORY ./release --revision main
mega snapshot OWNER/REPOSITORY --revision v1.0 --local-dir ./release
```

Use [Storage Buckets](/docs/hub/storage-buckets) instead for mutable files that
must change in place or be synchronized repeatedly.

## What to expect

- Large transfers can resume after an interruption when you retry the same
  workflow and destination.
- Repeated or incrementally updated content can reuse existing data, reducing
  unnecessary transfer work.
- Repository access rules and visibility apply to large artifacts just as they
  do to other repository files.
- A repository revision remains the unit you review, publish, and reproduce.

## Keep releases reproducible

Publish the model configuration, data description, and other required files in
the same repository revision as the large artifacts. Pin consumers to a commit
or tag, not a moving branch. See [Hub Repositories](/docs/hub/repositories) and
[Large Files](/docs/hub/large-files) for release guidance.
