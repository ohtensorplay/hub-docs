# Uploading Models

Publish model files, configuration, tokenizer assets, and the model card in one
revision. A complete release lets a reader download and load the same artifact
set later.

```bash
mega models upload OWNER/MODEL ./release . \
  --commit-message "Publish model release"
```

For a large tree, use the resumable uploader:

```bash
mega upload-large-folder OWNER/MODEL ./release --revision main
```

Before tagging a release, verify the model from a clean download and ensure the
card, license, configuration, tokenizer, index, and every referenced shard are
in the same commit. See [Large Files](/docs/hub/large-files) and
[Model Repositories](/docs/hub/models).
