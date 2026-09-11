# Artifact Format


MEGA files are intentionally not safetensors files. The format is optimized for explicit metadata, aligned payloads, and backend-specific loading paths.

## File types

| File | Purpose |
| --- | --- |
| `.mega` | Binary tensor shard containing metadata and payload bytes. |
| `.mega.index.json` | Manifest that maps tensor names to shards and carries shared metadata. |
| `.megakv` | Optional KV cache sidecar for prompt or prefix cache reuse. |

Single-shard releases may be opened directly through the `.mega` file. Multi-shard releases should use the `.mega.index.json` entry point.

## Shard layout

A shard carries:

1. A compact binary header.
2. A typed metadata section.
3. A tensor directory with names, dtypes, shapes, offsets, and lengths.
4. Aligned tensor payload bytes.
5. Optional footer overlay metadata.

Payload alignment is controlled during conversion:

```bash
mega convert Qwen3.5-0.8B \
  --output-dir Qwen3.5-0.8B/mega \
  --alignment 4096
```

Use larger alignment only when your storage backend benefits from it. The default is suitable for most local NVMe and object-backed workflows.

## Required metadata

Core metadata is stored under reserved namespaces:

| Namespace | Examples |
| --- | --- |
| `general.*` | `general.architecture`, `general.name`, `general.basename`, `general.size_label` |
| `mega.*` | `mega.tensor.count`, `mega.payload.nbytes`, `mega.layout.tensor_order` |
| `mega.shard.*` | shard ordinal, shard count, shard byte ranges |
| `mega.hash.*` | payload CRC32 and SHA-256 fields |
| `model.*` | model class, init arguments, state dict prefix |
| `tokenizer.*` | tokenizer model, tokens, merges, special tokens |

Do not write custom data into reserved namespaces unless the field is part of the MEGA schema.

## Custom metadata

Vendor or application metadata should use an owned prefix:

```text
tensorplay.release.channel = "stable"
tensorplay.eval.mmlu = "68.2"
acme.routing.partition = "gpu-a"
```

Prefer string values for metadata that needs to survive older readers. Use JSON strings for structured application-specific values.

## Index file

The index records shard files and tensor placement:

```json
{
  "metadata": {
    "general.name": "Qwen3.5-0.8B",
    "mega.tensor.count": "291"
  },
  "weight_map": {
    "model.embed_tokens.weight": "model-00001-of-00002.mega"
  }
}
```

Runtime APIs resolve the index first, then open only the shards required by the requested tensors.

## Hub repository metadata card

Model repository pages summarize MEGA-native artifacts in a **MegaTensors**
metadata card. The card inspects artifact data at the selected revision; it does
not infer tensor counts or dtypes from the model name.

The inspection entry point is deterministic:

- A release with one `.mega` file inspects that file directly.
- A release with multiple `.mega` shards inspects the first
  `.mega.index.json` path in lexical order.
- A multi-shard release without an index remains identifiable as MEGA, but the
  page cannot validate aggregate tensor metadata.

The rendered fields use these sources and fallbacks:

| Card field | Artifact source | Fallback |
| --- | --- | --- |
| Model size | `summary.parameter_count` from a directly inspected `.mega` file | A parameter-size token in repository identity or tags; otherwise stored size |
| Tensor type | Dtype counts in the inspected artifact, ordered by tensor count | A precision token in repository identity or tags; otherwise `Mixed` |
| Tensors | `summary.tensor_count` | Hidden when detailed inspection is unavailable |
| Files info | One file for a single artifact, or `summary.shard_count` for an index | Number of selected weight artifacts |
| Verified | Successful bounded artifact inspection | Hidden when inspection has not completed or failed |

Here **Verified** means that the Hub parsed and validated the artifact header or
index contract. It does not assert publisher identity, model quality, or
benchmark correctness. Use [Signing and Trust](/docs/hub/trust) for publisher
provenance and [Model Evaluations](/docs/hub/model-evaluations) for benchmark
evidence.

The card's **Files info** control opens the inspected artifact when there is a
stable file target. Otherwise it opens the selected repository tree so readers
can inspect the release layout themselves.

## Integrity

MEGA stores hashes for payload verification:

- `mega.hash.payload.crc32` for fast corruption checks.
- `mega.hash.payload.sha256` for stable release identity.
- Signing metadata under `mega.trust.*` when a release is signed.

Hash verification is separate from trust verification. A file can be internally consistent but still come from an unknown signer.

## Compatibility policy

Readers should ignore unknown metadata keys outside reserved namespaces. Writers should avoid changing tensor names unless they also record a mapping strategy in metadata or release notes.
