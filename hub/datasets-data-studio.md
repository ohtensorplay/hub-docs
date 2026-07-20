# Data Studio

Data Studio is the dataset workspace available from the **Data Studio** tab of
every dataset repository. It brings together Dataset Viewer, SQL Console, and
the Data Studio Agent for a selected configuration and split.

## Explore before publishing

Use Data Studio to answer practical questions about a dataset revision:

- inspect its schema, sample rows, and column statistics in Dataset Viewer;
- use SQL Console for a read-only filter, aggregation, or quality check;
- save a useful query for your own later review;
- ask the Data Studio Agent to explain the selected schema, preview values,
  data-quality questions, or suggest a SQL query.

The Agent requires sign-in and uses your MEGA Inference balance. Treat its
answers and suggested SQL as a starting point: run the query and review the
result before making a decision about the data.

## Publish a data change

Data Studio explores a published revision; dataset files remain versioned in
the repository. To make a change durable, edit or upload the dataset files in
the repository, commit the change to a branch or revision, validate it again in
Data Studio, and tag the reviewed release. Update the dataset card whenever a
schema, split, provenance, or quality characteristic changes.

This workflow preserves a clear connection between the result you inspected and
the exact data that downstream users can download. See
[Uploading Datasets](/docs/hub/datasets-uploading) and
[Dataset Viewing](/docs/hub/datasets-viewer) for the corresponding workflows.
