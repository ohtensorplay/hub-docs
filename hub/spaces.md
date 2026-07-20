# Spaces


MEGA Spaces turn a `space` repository into a deployed web application. Source,
revisions, visibility, discussions, and organization ownership stay in the Hub;
the selected revision is built and its runtime state is shown on the Space page.

As on Hugging Face, compute availability and visibility are independent. Every
authenticated personal or organization owner may create and deploy a Space; a
public repository exposes both source and application, while a private
repository keeps both behind repository authorization. Space creation and
deployment are subject to the published plan, security, and capacity limits.

The official [MEGA OpenAPI Space](/spaces/mega/openapi) is a reference Space
for browsing the live [OpenAPI document](https://mega.tensorplay.cn/.well-known/openapi.json).

## Choose an interface

| Need | Exact entry point |
| --- | --- |
| Browse the official reference Space | [`/spaces/mega/openapi`](/spaces/mega/openapi) |
| Create and upload source from a shell | [CLI Space workflow](/docs/megatensors/guides/cli#spaces-workflows) |
| Inspect or control runtime from Python | [Python SDK Space methods](/docs/hub/sdk#space-methods) |
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

Spaces also support `gradio`, `streamlit`, and `static` metadata. For Python
SDKs, set `app_file` when the entry point is not `app.py`.

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

Build and runtime logs are available as real-time streams at:

```text
GET /api/spaces/:owner/:name/logs/build
GET /api/spaces/:owner/:name/logs/run
```

## Hardware and environment

Hardware values are the currently available MEGA flavors. Query
`GET /api/spaces/hardware` and submit the returned identifier unchanged; do not
substitute Hugging Face hardware names or resource sizes.

```bash
mega spaces hardware
mega spaces settings mega/my-space --hardware cpu-upgrade
mega spaces settings mega/my-space --sleep-time 15m
mega spaces pause mega/my-space
```

`cpu-basic` is included at no compute charge. Paid hardware is charged per started minute while the runtime is starting or running. Admission charges the first minute, periodic maintenance meters additional minutes, `pause` or a downgrade to `cpu-basic` stops the meter, and an exhausted wallet is automatically paused. Query the shared wallet with `mega jobs balance`; add credit from [Settings → Billing](/settings/billing).

Variables are returned with values. Secrets return names and metadata but never
reveal stored secret values. Restart the Space after changing either set so the
next runtime receives the change.

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

Official platform Spaces use a dedicated organization service account. This keeps automated publishing separate from personal profiles while preserving normal repository authorization and audit events.

## Failure recovery

1. Read Space info and inspect the reported runtime error.
2. Follow build logs when the stage is `BUILD_ERROR`.
3. Follow run logs when the stage is `RUNTIME_ERROR`.
4. Fix the repository source and upload a new revision.
5. Restart the Space and wait for `RUNNING` before publishing its URL in documentation.

If a paid restart or hardware request returns `402`, top up the owner wallet or
choose `cpu-basic`. Check the billing page for the final charge associated with
a failed start.

For the OpenAPI reference, use the Space page and the live OpenAPI document.
