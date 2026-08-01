# Uploading Datasets

Create a dataset repository, organize its files by split or release, and
publish the card with the data.

```bash
mega repos create OWNER/DATASET --type dataset --private
mega datasets upload OWNER/DATASET ./dataset . --commit-message "Publish dataset"
```

For a large tree, use `mega upload-large-folder`. Keep data files, schema,
checksums, and documentation in the same revision. Prefer a private repository
until distribution rights and review are complete.

The `huggingface_hub` client can use the same repository contract:

```python
from huggingface_hub import HfApi

api = HfApi(endpoint="https://mega.tensorplay.cn", token="YOUR_MEGA_TOKEN")
api.create_repo("OWNER/DATASET", repo_type="dataset", exist_ok=True)
api.upload_folder(
    repo_id="OWNER/DATASET",
    repo_type="dataset",
    folder_path="./dataset",
    commit_message="Publish dataset",
)
```

For the corresponding `hf` CLI commands and compatibility limits, see
[Hugging Face Compatibility](/docs/hub/hugging-face-compatibility).

See [Large Files](/docs/hub/large-files) and
[Dataset Repositories](/docs/hub/datasets).
