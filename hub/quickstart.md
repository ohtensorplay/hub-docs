# Quickstart

Publish and retrieve your first versioned repository on MEGA Hub. This path uses a small model card so you can verify authentication, repository creation, upload, revision history, and download before moving large artifacts.

## Install the CLI

Install the published `mega` command with one of the supported Python tool runners:

```bash
uv tool install megatensors
# or: pipx install megatensors
# or: python -m pip install megatensors
```

Confirm the command is available:

```bash
mega version
mega --help
```

## Sign in

Start the browser device flow, then confirm the active identity:

```bash
mega auth login
mega auth whoami
```

The output shows the account handle you can use as `OWNER` below. For a headless environment, set `MEGA_TOKEN` or use `mega auth login --token "$MEGA_TOKEN"` instead. See [Authentication](/docs/hub/authentication) for token scopes and automation guidance.

## Create a repository

Create a public model repository. Replace `OWNER` with your account or organization handle:

```bash
mega repos create OWNER/hello-mega \
  --type model \
  --public \
  --description "My first MEGA Hub repository" \
  --exist-ok
```

Use `--private` instead when the release should only be visible to authorized members and tokens.

## Upload a first revision

Create a minimal repository card locally:

```bash
mkdir -p hello-mega
printf '# Hello MEGA\n\nMy first versioned release.\n' > hello-mega/README.md
```

Upload the folder to the repository root:

```bash
mega models upload OWNER/hello-mega ./hello-mega . \
  --commit-message "Publish first revision"
```

Every successful upload creates an immutable commit and advances the selected branch. Uploading a directory does not delete unrelated remote files unless you explicitly pass `--sync`.

## Inspect and download

Inspect the repository and its current file list:

```bash
mega repos info OWNER/hello-mega
mega models list OWNER/hello-mega --revision main
```

Download the full snapshot into a clean directory:

```bash
mega models download OWNER/hello-mega \
  --revision main \
  --local-dir ./downloaded-release
```

For reproducible consumers, replace `main` with a release tag or commit ID. See [Hub Repositories](/docs/hub/repositories) for branches, tags, Git, large uploads, and revision history.

## Publish a real artifact

The Hub stores ordinary repository files as well as MEGA-native model artifacts. A production model release normally includes its card, configuration, tokenizer, `model.mega.index.json`, and every referenced shard in the same revision.

1. [Convert safetensors to MEGA](/docs/megatensors/guides/conversion).
2. Follow the [Model Repository](/docs/hub/models) layout and release checklist.
3. [Load the downloaded artifact](/docs/megatensors/index) from Python.
4. Add related models, datasets, Spaces, or papers to a [Collection](/docs/hub/collections).

> [!TIP]
> Keep this small repository as a connectivity smoke test. It separates authentication and Hub permissions from conversion, GPU, and large-file failures.

## Troubleshooting

| Symptom | Check |
| --- | --- |
| `mega` is not found | Confirm the tool runner's bin directory is on `PATH`, then run `mega version`. |
| The repository owner is rejected | Run `mega auth whoami` and use an account or organization where you have write access. |
| Upload returns `401` or `403` | Sign in again or use a token with `repo:write`. |
| A private download returns `404` | Authenticate with an identity that can read the repository; private resources are not disclosed to unauthorized callers. |
| A large upload is interrupted | Re-run `mega upload` or `mega upload-large-folder`; the resumable workflow reuses transferred data where possible. |
