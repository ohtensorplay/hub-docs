# Xet Troubleshooting

For a large-artifact problem, first identify the repository, revision, client,
and operation that failed. Keep the error message and the time of the attempt,
but never include a token or private file contents in a support request.

## An upload or download was interrupted

Retry the same `mega upload-large-folder` or `mega snapshot` command with the
same source and destination. Keep the local source tree unchanged until the
transfer completes. Avoid concurrent transfers into the same revision.

## A Git or Git LFS client cannot transfer a file

Confirm that the repository URL, signed-in account, and Git LFS installation
match the workflow used by the project. Retry with the MEGA CLI snapshot or
large-folder command to isolate whether the issue is client-specific. See
[Git LFS Compatibility](/docs/xet/git-lfs-compatibility).

## You chose the wrong storage type

Use a Hub repository when the output needs commits, branches, tags, or review.
Use a [Storage Bucket](/docs/hub/storage-buckets) for mutable checkpoints,
logs, and synchronization. Moving files between those targets changes their
history semantics, so verify the destination before copying.

## Get help safely

Include the repository URL, revision, command category, approximate time, and
safe error text. Redact tokens, private keys, and proprietary artifact data.
For general Hub availability or API issues, see
[Service status and support](/docs/hub/deployment).
