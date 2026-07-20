# Dataset Viewing

Dataset Viewer is the browser preview in a dataset repository's **Data Studio**
tab. It lets readers inspect indexed dataset configurations and splits without
downloading the full repository.

## Open a split

Open a dataset repository and select **Data Studio**. Choose a configuration
and split to view:

- a schema-aware preview of the first rows;
- the selected split's row count, file size, and column count when available;
- per-column type, value count, and null count.

The Viewer respects repository visibility. A private dataset can be explored
only by people who can read that dataset, and a Viewer session always reflects
the dataset revision currently published by the repository.

## When a preview is not ready

Data Studio prepares views for supported dataset revisions before it can show a
split. If the page says that the Viewer is still preparing data, wait for the
dataset view to become available and refresh the page. Keep the dataset card
useful in the meantime: document the schema, split names, formats, and a small
loading example.

For filtering, aggregation, and validation across a split, use the
[Dataset SQL Console](/docs/hub/datasets-sql-console). For a guided way to
understand a schema or draft a query, see [Data Studio](/docs/hub/datasets-data-studio).
