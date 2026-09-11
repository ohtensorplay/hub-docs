# Model Repositories

Model repositories keep weights, configuration, tokenizer assets, documentation, and release history under one versioned `owner/name` identity. They use the same commit and access model as every MEGA repository, with model-specific discovery and download entry points.

## Create a model repository

Use [New Model](/new) in the browser or create one with the CLI:

```bash
mega repos create alice/qwen-demo \
  --type model \
  --description "Qwen checkpoint packaged for MEGA" \
  --license apache-2.0 \
  --tag text-generation \
  --tag 0.8b
```

Repositories are public unless `--private` is passed or an organization policy chooses a private default. The repository type cannot be changed after creation.

## Recommended release layout

A MEGA-native model release normally includes:

```text
qwen-demo/
├── README.md
├── config.json
├── tokenizer.json
├── model.mega.index.json
├── model-00001-of-00002.mega
└── model-00002-of-00002.mega
```

`README.md` is the model card shown on the repository page. The index is the stable runtime entry point for a sharded model. Keep configuration and tokenizer files beside the artifact so a pinned revision is self-contained.

See [Repository Cards](/docs/hub/repository-cards) for documentation guidance and [Artifact Format](/docs/megatensors/package_reference/format) for the binary and index contracts.

## Metadata and discovery

The root `README.md` may declare Hugging Face YAML metadata. Card `description` and `license` values override repository-setting fallbacks, while card and repository tags are merged. Visibility remains an access-controlled repository setting. See [Repository Cards](/docs/hub/repository-cards) for the complete precedence and validation rules.

Publish reproducible benchmark evidence with [Model Evaluations](/docs/hub/model-evaluations). Keep the score, metric, task, source, and release revision aligned with the model card.

On the model page, repository relationships are grouped as a model tree, Spaces
using the model, Collections including the model, and paper or article
references. The inference panel is separate: it reflects live model-to-Provider
routes rather than card tags. MEGA-native artifacts also receive a
[MegaTensors metadata summary](/docs/megatensors/package_reference/format#hub-repository-metadata-card)
from bounded inspection of the selected `.mega` file or `.mega.index.json`.

When a healthy `chat-completions` route exists, the same panel includes a
signed-in [Model Widget](/docs/hub/model-widgets). It uses the account's MEGA
Inference balance, supports Provider selection, and keeps bounded multi-session
history in account-scoped local browser storage. Model publishers cannot enable
the widget by adding card markup; the live Provider mapping is authoritative.

Fallback metadata can be updated without rewriting the card:

```bash
mega repos settings alice/qwen-demo \
  --description "Instruction-tuned 0.8B checkpoint" \
  --license apache-2.0 \
  --tag text-generation \
  --tag qwen \
  --tag 0.8b
```

Model search matches the repository ID, effective description, tags, and license. Task and library signals can also come from parsed card metadata. Use specific, stable tags rather than repeating prose from the card.

An inference task tag does not make a model callable by itself. Live Router availability is controlled by the separate model-to-Provider mapping shown at [Inference Models](/inference/models). See [Inference Providers](/docs/inference-providers/index) for routing and billing.

## Upload a release

Upload a directory through the typed model command:

```bash
mega models upload alice/qwen-demo ./release . \
  --commit-message "Publish MEGA release"
```

For a large release tree, the resumable uploader preserves completed multipart parts between attempts:

```bash
mega upload-large-folder alice/qwen-demo ./release --revision main
```

You can also push over Git. Large-object transfer is handled automatically;
never commit credentials, local caches, or private signing keys.

## Download and load

Download a complete revision or selected files:

```bash
mega models download alice/qwen-demo --revision main --local-dir ./model
mega download alice/qwen-demo config.json tokenizer.json --local-dir ./model
```

For reproducible automation, replace `main` with a release tag or commit ID. Python can download from the Hub and then open the pinned artifact:

```python
from megatensors.hub import MegaHubClient
from megatensors import mega_open

client = MegaHubClient()
root = client.snapshot_download("alice/qwen-demo", revision="v1.0")

with mega_open(root / "model.mega.index.json", device="cpu") as artifact:
    print(len(list(artifact.keys())))
```

Use the [Python SDK](/docs/hub/sdk) for the current download method signatures.

## Release checklist

- Add a model card with intended use, limitations, inputs, outputs, and provenance.
- Declare a license and task-oriented tags in repository metadata.
- Keep the model index, every referenced shard, configuration, and tokenizer in the same revision.
- Verify loading from a clean directory before creating a release tag.
- Pin deployments to a tag or commit instead of `main`.
- Use [Signing and Trust](/docs/hub/trust) when consumers require publisher provenance.
- Use [Gated Repositories](/docs/hub/gated-repositories) when access requires
  an application or license acknowledgement.
