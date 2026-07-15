# Backends


MEGA separates the public Python API from backend-specific I/O. The same artifact can be opened through different backends depending on the host.

## Backend matrix

| Backend | Best for | Notes |
| --- | --- | --- |
| GDS | GPU Direct Storage on Linux with compatible NVIDIA drivers. | Avoids unnecessary host copies when `cufile` is configured. |
| NoGDS | Portable CUDA or CPU loading. | Threaded host path with chunked requests. |
| Unified memory | Managed-memory deployments. | Useful for selected environments where explicit copies are costly. |
| DirectStorage | Windows storage path. | Intended for Windows DirectStorage deployments. |
| 3FS | Distributed storage experiments. | Retained for deployments that already use 3FS. |

## Choosing a backend

Default runtime behavior attempts the fastest supported path. Force NoGDS when debugging or when GDS is installed but not configured correctly:

```python
from megatensors import mega_open

with mega_open("model.mega.index.json", device="cuda:0", nogds=True) as artifact:
    tensor = artifact.get_tensor("model.embed_tokens.weight")
```

Examples expose the same switch:

```bash
python megatensors/examples/causal_lm_generate.py Qwen3.5-0.8B \
  --mega Qwen3.5-0.8B/mega/model.mega.index.json \
  --device cuda:0 \
  --nogds
```

## Configuration discovery

MEGA looks for backend configuration in this order:

1. `MEGATENSORS_CONFIG=/path/to/config.json`
2. `./megatensors.json`
3. Built-in defaults.

Use an explicit config path in production so process working directories do not change behavior:

```bash
export MEGATENSORS_CONFIG=/etc/mega/megatensors.json
```

## Operational checks

Before enabling GDS for a deployment, verify:

- The GPU driver, CUDA runtime, and `cufile` stack are installed.
- Model shards live on a filesystem supported by the GDS stack.
- The process has permission to read the shard files.
- No container profile blocks the required device files.

If any check fails, use `nogds=True` while you fix the host. The NoGDS path should still load the same artifacts.

## Performance notes

- Large shards reduce file-open overhead but can make retries and uploads more expensive.
- Smaller shards are friendlier to object storage and partial re-upload workflows.
- `borrow=True` and `assign=True` reduce cloning when loading PyTorch models.
- Tokenizer loading is metadata-bound, not storage-bandwidth-bound.
