# Deduplication

Xet can reuse data that has already been transferred when large repository
artifacts are repeated or incrementally updated. This reduces unnecessary data
transfer without changing the repository's visible files, revision history, or
access controls.

## What this means for releases

When you update a large checkpoint or append to a dataset, prefer an
incremental revision over creating unrelated duplicate copies. Keep related
artifacts in the same release tree, then publish a commit or tag once the tree
is verified.

```bash
mega upload-large-folder OWNER/REPOSITORY ./release --revision main
mega repos tag create OWNER/REPOSITORY v1.1 --message "Incremental release"
```

The exact transfer amount depends on the files and changes. Do not rely on a
specific storage or network saving when designing a workflow.

## Copies and reproducibility

Repository duplication and compatible copies can reuse existing artifact data
where possible. They still create an independent repository or destination
with its own visibility and ownership rules. Record the source repository and
revision in release notes so readers can trace the artifact lineage.

For mutable files, use [Storage Buckets](/docs/hub/storage-buckets) and record
the repository revision that produced the bucket content.
