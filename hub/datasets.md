# Dataset Repositories

Dataset repositories provide revisioned storage for training data, evaluation sets, annotations, schemas, and the documentation needed to use them responsibly. Their canonical browser path is `/datasets/{owner}/{name}`.

## Create a dataset repository

Use [New Dataset](/new-dataset) or the CLI:

```bash
mega repos create research/evaluation-prompts \
  --type dataset \
  --description "Versioned prompts and expected outputs" \
  --license cc-by-4.0 \
  --tag text \
  --tag evaluation
```

Add `--private` for data that should be visible only to the owner and authorized organization members. Repository permissions apply to metadata, cards, file listings, resolver downloads, and Git access.

## Organize the release

MEGA stores dataset files without rewriting their format. A small, portable layout might look like:

```text
evaluation-prompts/
├── README.md
├── data/
│   ├── train.jsonl
│   └── test.jsonl
├── schema.json
└── LICENSE
```

For larger datasets, use stable shard names and record the split-to-file mapping in the card. Keep schema, license, collection method, and filtering notes in the same tagged revision as the data they describe.

## Write the dataset card

The root `README.md` is rendered as the dataset card. At minimum, document:

- what each row or sample represents;
- available splits and file formats;
- collection, labeling, and filtering methods;
- licenses and source attribution;
- known bias, privacy, safety, and quality limitations;
- a minimal loading or parsing example.

See [Repository Cards](/docs/hub/repository-cards) for the supported Markdown surface and a reusable template.

## Explore data in the browser

Open the repository's **Data Studio** tab to preview indexed configurations and
splits, inspect column statistics, and run a read-only query. Signed-in users
can also save personal queries and ask the Data Studio Agent about the selected
split. See [Dataset Viewing](/docs/hub/datasets-viewer),
[Dataset SQL Console](/docs/hub/datasets-sql-console), and
[Data Studio](/docs/hub/datasets-data-studio).

## Upload data

Upload a folder with the typed dataset command:

```bash
mega datasets upload research/evaluation-prompts ./dataset . \
  --commit-message "Publish evaluation set"
```

Use include and exclude filters when a working directory contains generated files:

```bash
mega datasets upload research/evaluation-prompts ./dataset . \
  --include "README.md" \
  --include "data/**" \
  --include "schema.json" \
  --exclude "**/.cache/**"
```

For very large folders, use `mega upload-large-folder`. The client selects the
appropriate resumable transfer workflow. See [Xet](/docs/xet/index) for
versioned large-artifact guidance.

## Download a snapshot

```bash
mega datasets download research/evaluation-prompts \
  --revision v1.0 \
  --local-dir ./evaluation-prompts

mega datasets download research/evaluation-prompts \
  data/test.jsonl schema.json \
  --revision v1.0 \
  --local-dir ./evaluation-prompts
```

Use `--dry-run`, `--include`, and `--exclude` before copying a large snapshot. Immutable files are cached by revision; pinning a tag or commit makes a training or evaluation run reproducible.

## Versioning guidance

Use a branch for work in progress and a tag for a dataset release:

```bash
mega repos branch create research/evaluation-prompts next --revision main
mega repos tag create research/evaluation-prompts v1.0 \
  --revision main \
  --message "First reviewed release"
```

Do not silently replace the meaning of an existing tagged split. Publish a new commit and tag, then describe migrations or incompatible schema changes in the card.

## Link research

Add an `arxiv:<paper-id>` repository tag when a public dataset supports a paper:

```bash
mega repos settings research/evaluation-prompts \
  --tag text \
  --tag evaluation \
  --tag arxiv:2503.00948
```

Once the paper and authorship record are verified, MEGA connects the public release to its [Paper Page](/docs/hub/papers).

## Publication checklist

- Verify that no credential, private key, or unintended personal data is present.
- Declare the license and provenance for both original and derived material.
- Test parsing every published split from a clean checkout.
- Document sample counts, schema, checksums, and known exclusions.
- Use a private repository when distribution rights or review are incomplete.
- Use [Gated Repositories](/docs/hub/gated-repositories) when the dataset can
  be discoverable but each reader must accept access terms.
- Tag the exact revision consumed by downstream experiments.
