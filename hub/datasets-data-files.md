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

Do not put access tokens, raw credentials, or unreviewed personal data in data
files or examples.
