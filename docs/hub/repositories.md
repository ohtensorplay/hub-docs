# Hub Repositories


MEGA Hub repositories provide one revisioned storage model for `model`, `dataset`, and `space` content. Every file mutation creates an immutable commit, while branches and tags provide readable revision names.

## Repository identity

A repository ID has the form `owner/name`:

```text
mega/qwen-release
research/evaluation-corpus
alice/demo-space
```

Repository type is selected at creation and is one of `model`, `dataset`, or `space`. Visibility can be public or private.

## Clone and push with Git

HTTPS works for public clone and bearer-backed credential helpers; SSH is the recommended interactive write transport after registering a public key:

> Use `git.tensorplay.cn` directly for HTTPS Git and `ssh.tensorplay.cn` for SSH.
> Existing `mega.tensorplay.cn` Git remotes remain compatible through a streamed
> fallback, but new clones should use the dedicated Git data plane for performance.

```bash
curl -sSfL https://mega.tensorplay.cn/git-xet/install.sh | sh
git clone https://git.tensorplay.cn/mega/qwen-release.git
git clone git@ssh.tensorplay.cn:mega/qwen-release

cd qwen-release
git add .
git commit -S -m "Publish signed release"
git push origin main
```

MEGA validates repository authorization and commit integrity before publishing a
new revision. A rejected push does not partially advance the branch.

## Create and inspect

```bash
mega repos create mega/qwen-release --type model --private
mega repos info mega/qwen-release
mega repos list --owner mega --type model
```

Typed command groups provide the common list, info, files, upload, and download workflows:

```bash
mega models list
mega datasets files research/evals --revision main
mega spaces download alice/demo --local-dir ./space
```

## Upload and download

Upload one file or a directory:

```bash
mega upload mega/qwen-release ./config.json config.json
mega upload mega/qwen-release ./model --revision main --commit-message "Publish model"
```

For large directories, use the resumable uploader:

```bash
mega upload-large-folder mega/qwen-release ./model --revision main
```

Files at least 16 MiB use multipart upload. The client retains a token-free resume record below `~/.mega/uploads/` and skips completed parts on the next attempt.

Download selected files or a complete snapshot:

```bash
mega download mega/qwen-release config.json tokenizer.json --local-dir ./release
mega snapshot mega/qwen-release --revision main --local-dir ./release
```

## Copy with MEGA URIs

`mega cp` accepts local paths and typed Hub URIs:

```bash
mega cp mega://models/mega/source@main/config.json ./config.json
mega cp ./README.md mega://models/mega/source@main/README.md
```

Use `models`, `datasets`, or `spaces` in the URI authority to make the repository type explicit.

## Branches, tags, and history

```bash
mega repos branch list mega/qwen-release
mega repos branch create mega/qwen-release staging --revision main
mega repos tag create mega/qwen-release v1.0 --message "First release"
mega repos history mega/qwen-release --limit 20
mega repos commit mega/qwen-release v1.0
```

History and commit output includes `signature_status`, `signer_fingerprint`, `signer_subject`, and `author_email`. Use JSON output for a release gate:

```bash
mega repos commit mega/qwen-release v1.0 --format json
```

Tags are immutable revision pointers until explicitly deleted. Use a branch for ongoing work and a tag for a release boundary.

## Move, duplicate, and delete

```bash
mega repos move mega/old-name mega/new-name
mega repos duplicate mega/source research/copy --private
mega repos delete mega/obsolete
```

Moving can rename a repository or transfer it into an administered namespace. Duplicating copies repository history and metadata without transferring the underlying object bytes.

## Discussions and pull requests

Repository community operations use `mega discussions`:

```bash
mega discussions list mega/qwen-release
mega discussions create mega/qwen-release --title "Release notes" --body-file ./RELEASE.md
mega discussions comment mega/qwen-release 3 --body "Verified on CUDA 13."
```

Fine-grained tokens need `community:write` for mutations. Repository discussions and pull requests share numbering, message history, attachments, reactions, and moderation boundaries.

## Python SDK

```python
from megatensors.hub import MegaHubClient

client = MegaHubClient()
repo = client.create_repo(
    "mega/qwen-release",
    repo_type="model",
    private=True,
    exist_ok=True,
)

client.upload_file(
    "mega/qwen-release",
    "./config.json",
    path_in_repo="config.json",
)

for file in client.list_files("mega/qwen-release", revision="main"):
    print(file.path, file.size, file.sha256)
```

Use [Hub Python SDK](/docs/megatensors/package_reference/hub_client) for the typed method map and the live [OpenAPI Explorer](/spaces/mega/openapi#tag/Repositories) for direct HTTP integration.

## Operational guidance

- Pin deployments to a commit or tag instead of a mutable branch.
- Use `--dry-run` and include/exclude filters before large downloads.
- Keep repository type stable; create a separate repository when content semantics change.
- Prefer resumable multipart upload for large release trees.
- Require explicit confirmation for destructive branch, tag, file, and repository deletion.
