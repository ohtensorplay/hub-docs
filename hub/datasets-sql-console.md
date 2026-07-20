# Dataset SQL Console

Dataset SQL Console lets you run a read-only SQL query against the selected
configuration and split in a dataset's **Data Studio** tab. It is intended for
quick exploration and validation without changing the published dataset.

## Run a query

1. Open a dataset repository and select **Data Studio**.
2. Select the configuration and split to query.
3. Open **SQL Console**, write a query against the `data` view, and run it.

For example:

```sql
SELECT category, COUNT(*) AS examples
FROM data
GROUP BY category
ORDER BY examples DESC
LIMIT 20
```

Queries must be a single `SELECT` statement over the selected `data` view.
The console does not permit writes, file access, or changes to the repository.
Keep result sets focused with `LIMIT`, and use the Viewer to inspect individual
rows and column statistics.

## Save a personal query

Signed-in users can save a query from SQL Console and reopen it later for the
same dataset. Saved queries are personal: other readers of a public or shared
dataset do not see them. A saved query retains its configuration and split, so
check the selected revision and data schema before treating an old result as a
new release check.

Use [Data Studio](/docs/hub/datasets-data-studio) to ask for a query suggestion,
or [Uploading Datasets](/docs/hub/datasets-uploading) when a validated change
should become a new repository revision.
