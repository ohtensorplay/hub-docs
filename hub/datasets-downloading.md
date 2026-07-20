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
