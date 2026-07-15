# Spaces


MEGA Spaces turn a `space` repository into a deployed web application. Source,
revisions, visibility, discussions, and organization ownership stay in the Hub;
the platform builds the selected revision and reports runtime state through the
Space API.

As on Hugging Face, compute and visibility are independent. Every authenticated
personal or organization owner may create and deploy a Space; a public
repository exposes both source and application, while a private repository
keeps both behind repository authorization.

The official [MEGA OpenAPI Space](/spaces/mega/openapi) is the reference deployment. It is owned by the `mega` organization and reads the live [OpenAPI document](https://mega.tensorplay.cn/.well-known/openapi.json).

## Choose an interface

| Need | Exact entry point |
| --- | --- |
| Browse the official deployment | [`/spaces/mega/openapi`](/spaces/mega/openapi) |
| Create and upload source from a shell | [CLI Space workflow](/docs/megatensors/guides/cli#spaces-workflows) |
| Inspect or control runtime from Python | [Python SDK Space methods](/docs/megatensors/package_reference/hub_client#space-methods) |
| Integrate another client | [OpenAPI Space operations](/spaces/mega/openapi#tag/Spaces) |
| Browse every deployed HTTP operation | [OpenAPI Explorer](/spaces/mega/openapi) |

## Repository configuration

Put Space metadata in the repository `README.md` frontmatter. A Docker Space declares its application port and includes a Dockerfile:

```yaml
---
title: My application
sdk: docker
app_port: 7860
---
```

The platform also supports `gradio`, `streamlit`, and `static` metadata. For
Python SDKs, set `app_file` when the entry point is not `app.py`.

## Publish with the CLI

```bash
mega repos create mega/my-space --type space --public --exist-ok
mega spaces upload mega/my-space ./my-space . --sync \
  --commit-message "Publish application"
mega spaces info mega/my-space --format json
```

`--sync` makes the remote tree match the local source directory. Review the target repository ID before using it because files absent locally are removed from the uploaded revision.

## Deploy and observe

After source upload, restart and observe the runtime from the CLI. The runtime moves through states such as `BUILDING`, `RUNNING`, `PAUSED`, `BUILD_ERROR`, or `RUNTIME_ERROR`.

```bash
mega spaces restart mega/my-space
mega spaces wait mega/my-space --timeout 5m
mega spaces runtime mega/my-space --format json
mega spaces logs --build --tail 100 mega/my-space
mega spaces logs --follow mega/my-space
```

```python
from megatensors._hub import MegaApi

api = MegaApi()
api.restart_space("mega/my-space")
print(api.get_space_runtime("mega/my-space"))
```

Build and runtime logs are server-sent event streams at:

```text
GET /api/spaces/:owner/:name/logs/build
GET /api/spaces/:owner/:name/logs/run
```

## Hardware and environment

Hardware availability can change. Use `mega spaces hardware` or
`GET /api/spaces/hardware` as the source of truth and submit the returned
identifier unchanged.

```bash
mega spaces hardware
mega spaces settings mega/my-space --hardware cpu-upgrade
mega spaces settings mega/my-space --sleep-time 15m
mega spaces pause mega/my-space
```

`cpu-basic` is included at no compute charge. Paid hardware is billed according
to the price returned by the service. Pause the Space or select `cpu-basic` to
stop paid runtime usage. Query the shared wallet with `mega jobs balance`; add
credit from [Settings → Billing](/settings/billing).

Variables are returned with values. Secrets return names and metadata but never
reveal stored secret values. Updated values are applied to the next runtime
generation.

```bash
mega spaces variables add mega/my-space -e MODE=production
mega spaces variables list mega/my-space

export API_TOKEN="..."
mega spaces secrets add mega/my-space -s API_TOKEN
mega spaces secrets list mega/my-space
```

Enabling Space Dev Mode requires PRO for a personal Space or Team/Enterprise for an organization Space. Disabling it remains available after a downgrade:

```bash
mega spaces dev-mode mega/my-space
mega spaces dev-mode mega/my-space --stop
```

## Organization ownership

Create the repository as `organization-handle/name` while authenticated as an organization member or service account with repository write permission. Public repository responses expose a structured `organization` owner, and the canonical publisher link is `/organizations/:handle`.

## Failure recovery

1. Read Space info and note the current runtime stage.
2. Follow build logs when the stage is `BUILD_ERROR`.
3. Follow run logs when the stage is `RUNTIME_ERROR`.
4. Fix the repository source and upload a new revision.
5. Restart the Space and wait for `RUNNING`.

If a paid restart or hardware request returns `402`, top up the owner wallet or
choose `cpu-basic`.
