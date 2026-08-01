# Downloading Datasets

Download a complete snapshot or selected files from a dataset repository. Pin
the revision used by an experiment.

```bash
mega snapshot OWNER/DATASET --revision v1.0 --local-dir ./dataset
mega download OWNER/DATASET README.md --revision v1.0 --local-dir ./dataset
```

For a private or gated dataset, sign in with an account that has access and a
token with `repo:read`. Compatible Hugging Face clients can target MEGA with
`HF_ENDPOINT`; see [Hugging Face Compatibility](/docs/hub/hugging-face-compatibility).

For a supported file format, Datasets can load a pinned revision directly:

```python
from datasets import load_dataset

dataset = load_dataset(
    "OWNER/DATASET",
    data_files="data.jsonl",
    split="train",
    revision="v1.0",
    token="YOUR_MEGA_TOKEN",
)
print(dataset)
```

Use [Data Studio](/docs/hub/datasets-data-studio) when you need a browser
preview or SQL exploration rather than a local Datasets object.
