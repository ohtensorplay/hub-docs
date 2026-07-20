# Uploading Datasets

Create a dataset repository, organize its files by split or release, and
publish the card with the data.

```bash
mega repos create OWNER/DATASET --type dataset --private
mega datasets upload OWNER/DATASET ./dataset . --commit-message "Publish dataset"
```

For a large tree, use `mega upload-large-folder`. Keep data files, schema,
checksums, and documentation in the same revision. Prefer a private repository
until distribution rights and review are complete.

See [Large Files](/docs/hub/large-files) and
[Dataset Repositories](/docs/hub/datasets).
