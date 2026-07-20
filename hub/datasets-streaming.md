# Streaming Datasets

MEGA serves repository files through revision-aware download URLs. For a
dataset that is too large to materialize locally, design the reader to process
files incrementally and pin the repository revision it consumes.

Streaming is an application-level access pattern: MEGA does not change a data
format or infer a schema for your loader. Publish file layout, split names,
compression choices, and an example reader in the dataset card.

Use range-aware HTTP clients or a library that supports the format you publish.
For repeated processing or mutable intermediate outputs, consider a
[Storage Bucket](/docs/hub/storage-buckets) instead of rewriting a dataset
release in place.
