# Repository Cards

The root `README.md` is the long-form card for a model, dataset, or Space. It explains what the repository contains and how to use it, while repository metadata supplies the compact description, tags, license, and visibility used by directory listings.

## Hugging Face YAML metadata

MEGA parses Hugging Face-compatible YAML frontmatter at the beginning of the root `README.md`. The metadata is validated before an HF/API upload is committed, stored with the repository read model after `main` advances, and returned as `cardData` through the HF-compatible API.

```markdown
---
license: apache-2.0
language: [en, zh]
tags: [transformers, text-classification]
pipeline_tag: text-classification
library_name: transformers
base_model: google-bert/bert-base-uncased
---
# Project name
```

`description` and `license` from the card take precedence over their repository-setting fallback values. Card tags are merged with repository tags. Visibility and access policy always remain repository settings and cannot be changed by a card. Removing a field from the card restores the corresponding repository-setting fallback.

The YAML document must be a mapping containing JSON-compatible scalar, sequence, and mapping values. Aliases are rejected, duplicate keys are invalid, and the closing `---` or `...` delimiter must occur within the first 2 MiB. Common HF fields such as `license`, `language`, `tags`, `pipeline_tag`, `library_name`, `datasets`, and `base_model` participate in HF metadata and discovery responses; unrecognized compatible fields remain available in `cardData` for clients that understand them.

Repositories that predate the Card metadata migration populate the structured metadata when `main` next advances. A rollout that needs existing Cards in discovery immediately can backfill them with an empty Git commit on `main`; no object content needs to change.

Repository settings can still supply compact fallback metadata independently:

```bash
mega repos settings owner/project \
  --description "One-sentence directory summary" \
  --license apache-2.0 \
  --tag text-generation \
  --tag 0.8b
```

For Spaces, runtime fields such as `sdk`, `app_file`, and `app_port` also configure the Space runtime and are hidden from the rendered card.

## Minimal model card

~~~~markdown
# Project name

One paragraph describing the model and the release in this repository.

## Intended use

Describe supported tasks, inputs, outputs, and users.

## Load the release

```python
from megatensors import mega_open

with mega_open("model.mega.index.json", device="cpu") as model:
    print(list(model.keys())[:10])
```

## Limitations

Document evaluation gaps, unsafe uses, and operational constraints.

## License and citation

State the license, upstream sources, and preferred citation.
~~~~

## Minimal dataset card

```markdown
# Dataset name

Describe the source, purpose, and unit represented by one sample.

## Files and splits

| Split | Path | Samples |
| --- | --- | ---: |
| Train | `data/train.jsonl` | 10,000 |
| Test | `data/test.jsonl` | 1,000 |

## Schema

Explain each field, type, nullability rule, and label vocabulary.

## Collection and processing

Record provenance, consent or licensing basis, filtering, and annotation.

## Limitations

Document bias, privacy, safety, quality, and representativeness limits.
```

## Supported Markdown

Cards support GitHub-flavored Markdown, including headings, tables, task lists, fenced code, images, links, and blockquotes. Inline and display math are rendered with KaTeX delimiters such as `$...$` and `$$...$$`.

HTML is restricted to a safe subset. Scripts, styles, forms, iframes, embeds, and unsafe URL schemes are discarded. Use Markdown for portable results instead of relying on custom HTML or JavaScript.

Repository-relative links and images resolve against the selected revision. Prefer relative paths when documentation and assets should remain pinned together:

```markdown
See the evaluation report in `reports/evaluation.md`.

![Accuracy by task](assets/accuracy.png)
```

The browser renders cards up to 2 MiB. Larger `README.md` files remain downloadable but are not loaded as the repository card; move generated reports into separate files and link to them.

## Link a paper

Use an `arxiv:<paper-id>` repository tag rather than placing machine-readable paper metadata in card frontmatter:

```bash
mega repos settings owner/project \
  --tag text-generation \
  --tag arxiv:2503.00948
```

The card should still include a human-readable citation and explain the relationship between the paper and the released artifact. See [Paper Pages](/docs/hub/papers).

## Quality checklist

- Put a clear summary before the first detailed section.
- Include a copyable install, load, or parse example.
- Pin example downloads to a release tag when reproducibility matters.
- Document licenses, upstream sources, and material limitations.
- Give images meaningful alternative text and use descriptive link labels.
- Keep secrets, private endpoints, and personal data out of cards and examples.
- Preview the card in the repository page after every structural change.
