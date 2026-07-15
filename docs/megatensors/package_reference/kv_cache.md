# KV Cache Sidecars


KV cache data is stored outside the model artifact in `.megakv` sidecars. This keeps model releases immutable while allowing deployments to reuse prompt-specific or prefix-specific cache data.

## Open a sidecar

```python
from megatensors import open_kv_cache

cache = open_kv_cache("prompt.megakv")
print(cache.keys())
```

## Load a KV tensor

```python
from megatensors import load_kv_tensor

key = load_kv_tensor("prompt.megakv", "kv.layer.0.k", device="cuda:0")
value = load_kv_tensor("prompt.megakv", "kv.layer.0.v", device="cuda:0")
```

## Link to a model

MEGAKV metadata can bind a cache to a model artifact:

| Field | Purpose |
| --- | --- |
| `mega.kv.model_payload_hash` | Hash of the model payload the cache was built against. |
| `mega.kv.tokenizer_hash` | Hash of tokenizer metadata or tokenizer files. |
| `mega.kv.prompt_hash` | Application-level prompt or prefix identity. |

Verifiers should reject a sidecar when the model payload hash or tokenizer hash does not match the active model.

## Writing sidecars

Use `write_kv_cache` when producing cache artifacts from an inference job:

```python
from megatensors import write_kv_cache

write_kv_cache(
    "prompt.megakv",
    tensors={
        "kv.layer.0.k": key_tensor,
        "kv.layer.0.v": value_tensor,
    },
    metadata={
        "mega.kv.model_payload_hash": model_hash,
        "mega.kv.tokenizer_hash": tokenizer_hash,
    },
)
```

## Operational guidance

- Keep sidecars separate from model versioning.
- Treat sidecars as invalid after tokenizer or RoPE configuration changes.
- Use deterministic prompt hashing so cache reuse is auditable.
- Prefer short retention windows for user-specific prompts.
