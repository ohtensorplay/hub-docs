# Model and Tokenizer


MEGA stores enough model and tokenizer metadata to make an artifact usable without keeping the original Hugging Face directory beside it.

## Model identity

Common model fields include:

| Field | Meaning |
| --- | --- |
| `model.architectures` | Architecture names from the source config. |
| `model.class` | Importable class or constructor spec used by `load_model`. |
| `model.init.*` | Constructor keyword arguments. |
| `model.state_dict.prefix` | Prefix stripped when loading into a model. |

When these fields are complete, loading can be direct:

```python
from megatensors import load_model

model = load_model("model.mega.index.json", device="cuda:0", assign=True)
```

## Architecture fields

Architecture-specific values use a model prefix. For Qwen-style models, examples include:

- `qwen3.context_length`
- `qwen3.embedding_length`
- `qwen3.attention.head_count`
- `qwen3.attention.head_count_kv`
- `qwen3.rope.dimension_count`
- `qwen3.vocab_size`

These fields are metadata, not runtime code. Runtime model construction still needs a compatible Python class.

## Missing model metadata

If a release lacks `model.class`, pass construction details manually:

```python
from transformers import AutoConfig, AutoModelForCausalLM
from megatensors import load_model

config = AutoConfig.from_pretrained("./Qwen3.5-0.8B", trust_remote_code=True)
model = load_model(
    "model.mega.index.json",
    model_class=AutoModelForCausalLM.from_config,
    model_kwargs={"config": config},
    device="cuda:0",
)
```

## Tokenizer metadata

Tokenizer fields include:

- `tokenizer.model`
- `tokenizer.tokens`
- `tokenizer.token_type`
- `tokenizer.merges`
- `tokenizer.pre_tokenizer`
- special token strings and ids
- `tokenizer.add_bos_token`
- `tokenizer.add_eos_token`

Load the tokenizer from the MEGA artifact:

```python
from megatensors import load_tokenizer

tokenizer = load_tokenizer("model.mega.index.json")
encoded = tokenizer("Hello from MEGA", return_tensors="pt")
```

## Release checklist

Before publishing a converted model:

- Confirm `load_tokenizer(...)` works without the original source directory.
- Confirm `load_state_dict(...)` returns the expected number of tensors.
- Confirm generation matches the original Hugging Face model on a short prompt.
- Document any required `trust_remote_code` or custom model class in the repository README.
