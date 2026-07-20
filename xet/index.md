# Xet

Xet provides the large-artifact storage experience for MEGA Hub repositories.
Use it through the standard repository, Git, and MEGA CLI workflows when you
publish or download large model files, dataset archives, and other binary
release assets.

Xet is for versioned repository content. [Storage Buckets](/docs/hub/storage-buckets)
remain a separate Hub feature for mutable checkpoints, logs, and working files
that are synchronized in place.

## Start with the normal repository workflow

Clone, commit, and push a repository as usual, or use the MEGA CLI for a large
release tree. You do not need to configure a storage service or expose storage
credentials in your project.

```bash
mega upload-large-folder OWNER/REPOSITORY ./release --revision main
mega snapshot OWNER/REPOSITORY --revision v1.0 --local-dir ./release
```

Use a tag or commit for a production release so readers can reproduce the
exact artifact set. See [Use Xet Storage](/docs/xet/using-xet-storage) for the
recommended workflows.

## Choose Xet or a Bucket

| Need | Use |
| --- | --- |
| Reviewable history, branches, tags, and a repository card | A Hub repository with Xet-backed large artifacts |
| Mutable files, checkpoints, logs, or repeated synchronization | A [Storage Bucket](/docs/hub/storage-buckets) |

Read [Xet Storage Overview](/docs/xet/overview),
[Git LFS Compatibility](/docs/xet/git-lfs-compatibility), and
[Xet Security](/docs/xet/security) before moving a large existing workflow.
