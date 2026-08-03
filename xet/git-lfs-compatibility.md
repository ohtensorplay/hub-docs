# Git LFS Compatibility

MEGA supports Git LFS-compatible repository workflows so existing projects can
continue using standard Git tools while moving large artifacts through MEGA.
You do not need to change repository history or teach collaborators a separate
storage protocol.

## Use the existing Git workflow

Clone the repository, make your change, commit it, and push as usual:

```bash
curl -sSfL https://mega.tensorplay.cn/git-xet/install.sh | sh
git clone https://git.tensorplay.cn/OWNER/REPOSITORY.git
cd REPOSITORY
git add .
git commit -m "Update weights"
git push origin main
```

The repository remains the source of truth for its visible files, history, and
revisions. Use the [MEGA CLI](/docs/megatensors/guides/cli) when a large release
tree needs resumable transfer or a scripted workflow.

MEGA's Git-Xet client advertises `xet` for upload and `xet-download` for
download. Once the Git LFS batch request is authorized, Git-Xet sends and
receives file bytes directly from the negotiated Xet CAS. The Hub and Git
Gateway stay on the control plane and do not proxy the large-file stream.

## Migrate gradually

Keep using the Git and Git LFS tooling that your project already requires.
Test a clone, checkout, and download with the same client versions used by your
collaborators and CI before changing a release process. Pin production users to
a tag or commit.

If a legacy client cannot transfer a large file, update its Git LFS support or
use the MEGA CLI snapshot workflow. Do not edit pointer files by hand.

## Buckets are different

Git LFS compatibility applies to versioned repository artifacts. A
[Storage Bucket](/docs/hub/storage-buckets) is a mutable file store and does
not provide repository commits, branches, or tags.
