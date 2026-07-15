# Conversion


`mega convert` reads a Hugging Face model directory and writes MEGA shards plus a `model.mega.index.json` manifest.

## Basic conversion

```bash
mega convert ./Qwen3.5-0.8B --output-dir ./Qwen3.5-0.8B/mega
```

The converter discovers:

- `*.safetensors` weight files.
- `config.json`.
- tokenizer files such as `tokenizer.json`, `tokenizer.model`, `tokenizer_config.json`, and `special_tokens_map.json`.
- architecture and model initialization metadata when available.

## Important options

| Option | Default | Use |
| --- | --- | --- |
| `--output-dir` | source-dependent | Directory for generated `.mega` files and index. |
| `--basename` | `model` | Base filename for shards and index. |
| `--max-shard-size` | `5GB` | Maximum payload size per shard. |
| `--alignment` | `4096` | Payload alignment in bytes. |
| `--compression` | `none` | Compression strategy. |
| `--compression-level` | `3` | Compression level when enabled. |
| `--sign-bundle` | none | PEM bundle used to sign generated artifacts. |

## Sharding strategy

Use a smaller shard size when releases are uploaded over unreliable networks or stored in object stores with small part limits:

```bash
mega convert ./Qwen3.5-7B \
  --output-dir ./Qwen3.5-7B/mega \
  --max-shard-size 2GB
```

Use a larger shard size for local NVMe workflows where fewer files are easier to manage:

```bash
mega convert ./Qwen3.5-7B \
  --output-dir ./Qwen3.5-7B/mega \
  --max-shard-size 20GB
```

## Signed conversion

Pass a signing bundle to bind the generated payload hash to a certificate-backed signature:

```bash
mega convert ./Qwen3.5-0.8B \
  --output-dir ./Qwen3.5-0.8B/mega \
  --sign-bundle ./signing/mega-release-bundle.pem
```

The bundle contains the private key, leaf certificate, and optional chain. Trusted roots are not embedded; verifiers supply roots from local policy.

## Output contract

A successful conversion should produce:

```text
model.mega.index.json
model-00001-of-00002.mega
model-00002-of-00002.mega
```

Check tensor names before publishing:

```bash
python megatensors/examples/extract_keys.py ./Qwen3.5-0.8B/mega
```

## When conversion should fail

Conversion should stop instead of producing a partial artifact when:

- A safetensors file is missing or unreadable.
- Tensor metadata is inconsistent across shards.
- Tokenizer metadata cannot be parsed.
- Signing is requested but the bundle is invalid.
- The output directory cannot be written safely.
