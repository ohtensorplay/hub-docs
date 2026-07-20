# Quickstart


This guide starts with a local Hugging Face model directory and produces a MEGA artifact directory that can be loaded by the runtime.

## Prerequisites

- Python environment with PyTorch installed.
- A Hugging Face-style model folder containing `config.json`, tokenizer files, and one or more `.safetensors` files.
- Optional: CUDA and GPUDirect Storage if you want to use the GDS backend.

## Install

Install the published CLI and Python package:

```bash
uv tool install megatensors
# or: pipx install megatensors
# or: python -m pip install megatensors
```

Check that Python and the CLI resolve to the intended environment:

```bash
python -c "import megatensors; print(megatensors.__version__)"
mega version
```

When developing MEGA itself, use `python -m pip install -e ./megatensors` from the parent repository instead.

## Convert a model

Convert a Hugging Face directory into MEGA shards:

```bash
mega convert Qwen3.5-0.8B \
  --output-dir Qwen3.5-0.8B/mega \
  --basename model \
  --max-shard-size 5GB
```

The command prints a summary similar to:

```text
converted tensors=291 shards=2 payload=1.529GiB time=2.41s rate=0.63GiB/s index=Qwen3.5-0.8B/mega/model.mega.index.json
```

## Load tensors

Use `mega_open` when you want lazy access to tensor names and values:

```python
from megatensors import mega_open

with mega_open("Qwen3.5-0.8B/mega/model.mega.index.json", device="cuda:0") as artifact:
    print(len(list(artifact.keys())))
    weight = artifact.get_tensor("model.embed_tokens.weight")
```

Use `nogds=True` when CUDA/GDS is not configured or when you want the threaded host backend:

```python
with mega_open("Qwen3.5-0.8B/mega/model.mega.index.json", device="cuda:0", nogds=True) as artifact:
    weight = artifact.get_tensor("model.embed_tokens.weight")
```

## Load a state dict

```python
from megatensors import load_state_dict

state = load_state_dict(
    "Qwen3.5-0.8B/mega/model.mega.index.json",
    device="cuda:0",
    borrow=True,
)
```

`borrow=True` avoids cloning tensors out of the file buffer. Keep the returned state dict alive while the model uses those tensors, and call `state.close()` when you are done.

## Load tokenizer and model

```python
from megatensors import load_model, load_tokenizer

path = "Qwen3.5-0.8B/mega/model.mega.index.json"
tokenizer = load_tokenizer(path)
model = load_model(path, device="cuda:0", assign=True)
```

If the artifact does not include `model.class` and `model.init.*` metadata, pass the model class explicitly:

```python
from transformers import AutoConfig, AutoModelForCausalLM
from megatensors import load_model

config = AutoConfig.from_pretrained("Qwen3.5-0.8B", trust_remote_code=True)
model = load_model(
    "Qwen3.5-0.8B/mega/model.mega.index.json",
    model_class=AutoModelForCausalLM.from_config,
    model_kwargs={"config": config},
    device="cuda:0",
)
```

## Troubleshooting

| Symptom | Fix |
| --- | --- |
| `missing model.class metadata` | Pass `model_class` and `model_kwargs`, or reconvert with model metadata enabled. |
| GDS initialization fails | Use `--nogds` in examples or `nogds=True` in Python. |
| Tokenizer cannot load | Confirm tokenizer files were present in the source directory before conversion. |
| Tensor name mismatch | Use `artifact.keys()` or `extract_keys.py` to inspect names before loading. |
