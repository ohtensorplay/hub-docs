# Dataset File Organization

Organize a dataset so a reader can identify files, splits, and schema without
guessing. A simple release layout is:

```text
dataset/
├── README.md
├── data/
│   ├── train.parquet
│   ├── validation.parquet
│   └── test.parquet
└── schema.json
```

The names are your contract: document every split, format, compression method,
field type, and identifier policy in the card. Publish checksums for important
exports and create a new revision when the meaning of an existing split changes.

Dataset Viewer can infer conventional `train`, `validation`, and `test` file
names automatically. For more than one configuration, nonstandard names, or a
custom delimiter, declare the mapping in the dataset card front matter:

```yaml
---
configs:
  - config_name: english
    default: true
    data_dir: data/en
    data_files:
      - split: train
        path:
          - train-*.jsonl.gz
      - split: validation
        path: validation.jsonl.gz
  - config_name: french
    data_files:
      train: data/fr/train.csv
      test: data/fr/test.csv
    delimiter: ","
    encoding: utf-8
---
```

`data_files` accepts a string, a list, split-to-path mappings, path lists, and
repository-relative globs. A `data_dir` is prepended to paths within that
configuration. Keep configuration and split names stable because compatible
Viewer and Parquet URLs include both values.

For image, audio, video, PDF, or WebDataset archives, keep file extensions and
archive member names meaningful. The Viewer preserves semantic media features
and exposes member paths in previews; the original binary data remains in the
derived Parquet representation.

Do not put access tokens, raw credentials, or unreviewed personal data in data
files or examples.
