# Use Xet Storage

Use Xet through MEGA's ordinary repository tools. For most workflows, select a
repository, keep the source tree stable while it transfers, and use a commit or
tag when the release is ready.

## Upload a large release tree

Use the resumable uploader for large directories:

```bash
mega upload-large-folder OWNER/REPOSITORY ./release --revision main
```

Run the command again with the same source and destination after an
interruption. Do not start several uploads into the same destination at once.
Once the release is verified, add an explicit tag for consumers.

## Download a release

Download a complete revision with an explicit tag or commit:

```bash
mega snapshot OWNER/REPOSITORY --revision v1.0 --local-dir ./release
```

Keep the manifest, configuration, and artifact files together. For selected
files or small changes, use the repository commands in
[Hub Repositories](/docs/hub/repositories).

## Work with Git

Standard Git repository workflows continue to work for MEGA repositories:

```bash
git clone https://mega.tensorplay.cn/OWNER/REPOSITORY.git
cd REPOSITORY
git add .
git commit -m "Publish release"
git push origin main
```

Use [Git LFS Compatibility](/docs/xet/git-lfs-compatibility) when an existing
project or automation already relies on Git LFS tooling.

## Choose the right storage target

Use a repository for release assets that need history and review. Use a
[Storage Bucket](/docs/hub/storage-buckets) for mutable job output,
checkpoints, or files synchronized in place.
