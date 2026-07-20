# Tensor Runtime


This page documents the local tensor runtime. For Hub repositories, Jobs, authentication, and webhooks, use the [Hub Python SDK](/docs/megatensors/package_reference/hub_client).

The runtime API is intentionally small. Most performance-sensitive work stays in the C++ extension and backend implementations.

## Lazy artifact access

Use `mega_open` when you want explicit control over the artifact lifetime:

```python
from megatensors import mega_open

with mega_open("model.mega.index.json", device="cuda:0") as artifact:
    print(list(artifact.keys())[:10])
    tensor = artifact.get_tensor("model.layers.0.self_attn.q_proj.weight")
```

Common options:

| Option | Default | Meaning |
| --- | --- | --- |
| `device` | `"cpu"` | Target device such as `"cuda:0"`. |
| `framework` | `"pt"` | Framework adapter. |
| `nogds` | `False` | Force the threaded host backend instead of GDS. |

## Iterate tensors

```python
from megatensors import iter_tensors

for name, tensor in iter_tensors("model.mega.index.json", device="cuda:0"):
    print(name, tuple(tensor.shape), tensor.dtype)
```

Filter or rename tensors while loading:

```python
state = dict(iter_tensors(
    "model.mega.index.json",
    tensor_filter=lambda name: name.startswith("model.layers.0."),
    key_mapping=lambda name: name.removeprefix("model."),
))
```

## Load one tensor

```python
from megatensors import load_tensor

embedding = load_tensor(
    "model.mega.index.json",
    "model.embed_tokens.weight",
    device="cuda:0",
)
```

## Load a state dict

```python
from megatensors import load_state_dict

state = load_state_dict(
    "model.mega.index.json",
    device="cuda:0",
    borrow=False,
)
```

Set `borrow=True` for lower-copy loading:

```python
state = load_state_dict("model.mega.index.json", device="cuda:0", borrow=True)
try:
    model.load_state_dict(state, assign=True)
finally:
    state.close()
```

## Load a model

```python
from megatensors import load_model

model = load_model(
    "model.mega.index.json",
    device="cuda:0",
    strict=True,
    assign=True,
)
```

If model construction metadata is not present, pass it:

```python
model = load_model(
    "model.mega.index.json",
    model_class="transformers.AutoModelForCausalLM",
    model_kwargs={"config": config},
    device="cuda:0",
)
```

## Load a tokenizer

```python
from megatensors import load_tokenizer

tokenizer = load_tokenizer("model.mega.index.json")
tokens = tokenizer("MEGA loads tensors", return_tensors="pt")
```

The tokenizer loader reads tokenizer metadata embedded during conversion. It does not require the original Hugging Face directory if the metadata is complete.

## KV cache sidecars

```python
from megatensors import open_kv_cache, load_kv_tensor

cache = open_kv_cache("prompt.megakv")
key = load_kv_tensor("prompt.megakv", "kv.layer.0.k", device="cuda:0")
```

See [KV Cache Sidecars](/docs/megatensors/package_reference/kv_cache) for metadata and writing guidance.
