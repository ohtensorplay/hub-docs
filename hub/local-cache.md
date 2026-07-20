# Local Cache

MEGA clients cache downloaded revisions locally to avoid fetching immutable
files again. A cache is an optimization, not a source of truth: use an explicit
revision when a workflow must be reproducible.

## Work with cached downloads

```bash
mega snapshot OWNER/REPOSITORY --revision v1.0 --local-dir ./release
mega download OWNER/REPOSITORY config.json --revision v1.0 --local-dir ./release
```

The client can reuse local content that matches the requested immutable
revision. If you need a clean verification, download into a new directory and
validate the release again.

## Cache hygiene

Use `MEGA_HOME` to select a separate MEGA configuration and cache location for
an isolated environment. Do not put a shared cache in a repository, container
image, or artifact upload. Remove local cached data according to your own
retention policy when it contains private resources.

Changing a branch name does not change a cached commit. Pin a tag or commit to
avoid ambiguity when `main` advances.
