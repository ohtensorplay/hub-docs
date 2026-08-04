# Dataset Viewing

Dataset Viewer is the browser preview in a dataset repository's **Data Studio**
tab. It lets readers inspect indexed dataset configurations and splits without
downloading the full repository.

## Automatic conversion

When a supported dataset revision is published, MEGA automatically prepares a
Parquet view for Data Studio. The source files are unchanged, and readers can
continue to download the exact published revision.

The converter recognizes CSV, TSV, JSON, JSON Lines, NDJSON, Parquet, Arrow,
Feather, plain text, common image/audio/video files, PDF, ZIP, TAR, and
WebDataset-style TAR archives. Tabular files may also use `gz`, `bz2`, `xz`,
`zst`, or `zstd` compression. A dataset card can select files with Hugging Face
`configs`, `data_files`, `data_dir`, path lists and globs, `default: true`,
`delimiter` or `sep`, and `encoding` fields. Without an explicit configuration,
MEGA infers `train`, `validation`, and `test` from file names and uses `train`
for remaining files.

Large or partially supported datasets may expose an indexed subset. Data Studio
labels that view as `partial` so readers do not mistake it for the complete
source dataset. Set `viewer: false` in the dataset card front matter when a
repository should not prepare a Viewer.

## Open a split

Open a dataset repository and select **Data Studio**. Choose a configuration
and split to view:

- pages of up to 100 rows across the indexed Parquet split;
- the selected split's row count, file size, and column count when available;
- full-split text search, column distribution filters, and row deep links;
- per-column type, value count, null count, and interactive distributions;
- inline image, audio, video, and structured agent-trace cells when present.

The Viewer respects repository visibility. A private dataset can be explored
only by people who can read that dataset, and a Viewer session always reflects
the dataset revision currently published by the repository.

When the Viewer reports a partial dataset, its search, filters, pages, and SQL
queries cover only the indexed Parquet subset. Check the repository files and
dataset card before treating that subset as the full source dataset.

Search, filters, pagination, and SQL cover the indexed Parquet view of the
selected split. They do not alter the published dataset revision.

## Compatible access

Compatible clients can read split metadata, first rows, schema, sizes,
statistics, Croissant metadata, and generated Parquet URLs through the Hub API.
Parquet downloads support partial requests and follow the same public/private
permissions as the dataset. See the live [OpenAPI Explorer](/spaces/mega/openapi)
for the current request and response schemas.

## When a preview is not ready

Data Studio prepares views for supported dataset revisions before it can show a
split. If the page says that the Viewer is still preparing data, wait for the
dataset view to become available and refresh the page. Keep the dataset card
useful in the meantime: document the schema, split names, formats, and a small
loading example.

For filtering, aggregation, and validation across a split, use the
[Dataset SQL Console](/docs/hub/datasets-sql-console). For a guided way to
understand a schema or draft a query, see [Data Studio](/docs/hub/datasets-data-studio).
