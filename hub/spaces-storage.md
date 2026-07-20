# Space Storage

Space source is stored in its repository revision. The application filesystem
outside `/data` is temporary and may be replaced on each build or restart.

Enable persistent storage from the Space settings or `POST
/api/spaces/:owner/:name/storage` with one of `small`, `medium`, or `large`.
MEGA mounts the resulting per-Space volume at `/data`. It survives pause,
restart, and source rebuild; it is not shared with other Spaces and it does not
change the immutable repository revision.

Use a repository for application source and release assets. Use `/data` for
mutable checkpoints and notebook work. A [Storage Bucket](/docs/hub/storage-buckets)
can also be attached as a managed read-only input volume.

## Mount a Hub resource

A Space can mount a Bucket, model, dataset, or another Space as a read-only
volume. This is useful when an application needs a reference dataset, model, or
Bucket path without copying it into its own source repository.

Set the complete volume list with the Space API:

```json
PUT /api/spaces/OWNER/NAME/volumes
{
  "volumes": [
    {
      "type": "bucket",
      "source": "research/training-artifacts",
      "path": "checkpoints",
      "mountPath": "/mnt/checkpoints",
      "readOnly": true
    }
  ]
}
```

Each mount must have a unique path and is always read-only. Choose a tag or
commit for reproducibility when mounting a repository; `main` follows that
repository's current default revision. Mount paths cannot replace the
application files or `/data`. A Bucket mount is a read-only input; use `/data`
or a separately configured Bucket client for output written by the application.

Send `DELETE /api/spaces/OWNER/NAME/volumes` to remove all repository mounts.
This does not delete the source Bucket or repositories, or the persistent
`/data` volume.

Deleting persistent storage is destructive: the named `/data` volume is removed
after the Space is rebuilt without storage. Export data or commit the files you
need before `DELETE /api/spaces/:owner/:name/storage`.

Never rely on a runtime-local file outside `/data` as the only copy of an
important result. Record the source revision that produced durable outputs.
