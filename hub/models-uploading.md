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

## Use a Hugging Face-compatible client

The standard `huggingface_hub` client can create the repository and upload a
folder when its endpoint is pointed at MEGA:

```python
from huggingface_hub import HfApi

api = HfApi(endpoint="https://mega.tensorplay.cn", token="YOUR_MEGA_TOKEN")
api.create_repo("OWNER/MODEL", repo_type="model", exist_ok=True)
api.upload_folder(
    repo_id="OWNER/MODEL",
    repo_type="model",
    folder_path="./release",
    commit_message="Publish model release",
)
```

The same workflow is available through `hf upload` after setting
`HF_ENDPOINT`. See [Hugging Face Compatibility](/docs/hub/hugging-face-compatibility)
for supported operations and limits.

Before tagging a release, verify the model from a clean download and ensure the
card, license, configuration, tokenizer, index, and every referenced shard are
in the same commit. See [Large Files](/docs/hub/large-files) and
[Model Repositories](/docs/hub/models).
