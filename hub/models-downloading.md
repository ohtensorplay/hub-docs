# Downloading Models

Download a complete model revision or the specific files your application
needs. Use a tag or commit ID for reproducible automation.

```bash
mega models download OWNER/MODEL --revision v1.0 --local-dir ./model
mega download OWNER/MODEL config.json tokenizer.json --revision v1.0 --local-dir ./model
```

Compatible Hugging Face clients can target MEGA with `HF_ENDPOINT`; see
[Hugging Face Compatibility](/docs/hub/hugging-face-compatibility).

For a gated or private model, authenticate with a token that has `repo:read`
and has access to the repository. Review the model card and license before
loading a model into a production workflow.
