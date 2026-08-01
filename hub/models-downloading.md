# Downloading Models

Download a complete model revision or the specific files your application
needs. Use a tag or commit ID for reproducible automation.

```bash
mega models download OWNER/MODEL --revision v1.0 --local-dir ./model
mega download OWNER/MODEL config.json tokenizer.json --revision v1.0 --local-dir ./model
```

Compatible Hugging Face clients can target MEGA with `HF_ENDPOINT`; see
[Hugging Face Compatibility](/docs/hub/hugging-face-compatibility).

For a conventional model layout, the same endpoint can be used with
`huggingface_hub` and Transformers:

```python
from huggingface_hub import hf_hub_download
from transformers import AutoConfig

config_path = hf_hub_download(
    repo_id="OWNER/MODEL",
    filename="config.json",
    revision="v1.0",
    endpoint="https://mega.tensorplay.cn",
    token="YOUR_MEGA_TOKEN",
)
config = AutoConfig.from_pretrained(
    "OWNER/MODEL",
    revision="v1.0",
    token="YOUR_MEGA_TOKEN",
)
print(config_path, config.model_type)
```

The client downloads files; it does not convert a MEGA-native
`model.mega.index.json` release into a Transformers checkpoint. Open that
format with [Megatensors](/docs/megatensors/index) instead.

For a gated or private model, authenticate with a token that has `repo:read`
and has access to the repository. Review the model card and license before
loading a model into a production workflow.
