# Jobs


MEGA Jobs runs a public Docker image and command in a MEGA-managed environment.
The interface is intentionally Docker-like: dispatch with `run`, discover
active work with `ps`, stream output with `logs`, and inspect retained metadata
with `inspect`.

Open the [Jobs settings page](/settings/jobs) for guided examples. Use [Web console](/settings/jobs?view=console) for browser dispatch and history, or [CLI reference](/settings/jobs?view=cli) for a compact in-product command matrix.

## Choose an interface

| Interface | Use it for | Exact reference |
| --- | --- | --- |
| Web console | Guided dispatch, retained history, logs, and schedules in the browser. | [Open the Jobs console](/settings/jobs?view=console) |
| MEGA CLI | Interactive work, shell scripts, and CI exit codes. | [Jobs CLI workflows](/docs/megatensors/guides/cli#jobs-workflows) |
| Python SDK | Typed orchestration from Python applications. | [Job methods](/docs/hub/sdk#job-methods) |
| Web API | Custom clients, other languages, and direct SSE log handling. | [Jobs operations](/spaces/mega/openapi#tag/Jobs) |

All four surfaces use the same owner, state, timeout, and capability contract described on this page.

## Current runtime boundary

| Capability | Current contract |
| --- | --- |
| Flavor | `cpu-nano` |
| Allocation | 0.25 vCPU, 256 MB RAM, and 256 MB swap |
| Runtime | 30 seconds through 1 hour; default 1 hour |
| Images | Public Docker image references |
| Ownership | Personal namespace or an organization administered by the caller |
| Output | Retained logs plus a live CPU, memory, and network metrics stream |
| Mounts | Read-only model, dataset, or Space mounts; Bucket mounts may be read-write |
| Private access | Opt-in SSH for a `RUNNING` Job through the shared Access-protected ingress |
| Not enabled | GPU, TPU, Space images, and exposed ports |

Use `mega jobs hardware` or `GET /api/jobs/hardware` as the runtime source of truth. Unsupported capabilities return `422` instead of being silently ignored.

## Install and log in

```bash
uv tool install megatensors
mega auth login
mega jobs hardware
```

Job routes require the `jobs:run` token scope.

## Pricing and prepaid credit

Jobs use prepaid compute credit and bill each started running minute. The current `cpu-nano` price is returned by `mega jobs hardware`; do not hard-code a price in automation. A Job reserves the requested timeout ceiling before dispatch, then refunds the unused portion exactly once when it reaches a terminal state. If the balance cannot cover the reservation, dispatch fails with `402` and no container is started.

Check the personal or organization wallet before dispatch:

```bash
mega jobs balance
mega jobs balance --namespace research --format json
mega jobs usage
mega jobs usage --namespace research --format json
```

Add credit from [Settings → Billing](/settings/billing). Jobs and paid Spaces
share the same owner wallet. Review the price returned for the selected
hardware before dispatching a Job.
Usage totals cover settled usage, while the detailed list is paginated. Follow
the returned totals and pagination fields instead of assuming a fixed history
length.

## Run a Job

```bash
mega jobs run python:3.12-slim \
  python -c 'print("Hello from the cloud!")'
```

Foreground mode waits for terminal state, prints retained output, and exits non-zero unless the Job reaches `COMPLETED`.

Return as soon as MEGA accepts the Job:

```bash
mega jobs run --detach python:3.12-slim python task.py
```

## Run a UV Job

The CLI also supports the same UV-oriented Job workflows as `hf jobs uv run`.
It creates an ordinary MEGA CPU Job using a public image with `uv`; it does not
claim a separate notebook, GPU, or endpoint product. A local script is copied
to the caller's Job-artifacts Bucket and mounted into that Job; an HTTPS URL or
command is passed to `uv run` directly.

```bash
mega jobs uv run https://example.test/task.py --with requests
mega jobs uv run --detach train.py --with datasets --python 3.12
```

`--flavor` accepts only the live `cpu-nano` flavor (or `cpu-basic` as the
compatibility alias), and `--ssh`, mounts, labels, secrets, timeout, namespace,
and detach have the same contract as `mega jobs run`.

## Private Job SSH

For interactive debugging, opt in when creating a long-running Job. SSH is
private: register an account key first, wait for the Job to reach `RUNNING`,
then connect through the same Cloudflare Access-protected ingress used by
Space Dev Mode. No Job container port is exposed.

```bash
mega auth keys add ~/.ssh/id_ed25519.pub --name laptop
mega jobs run --ssh --detach python:3.12-slim sleep 3600
mega jobs ssh JOB_ID
```

`mega jobs ssh JOB_ID COMMAND...` runs one command instead of an interactive
shell. It requires the key owner to own the Job and is unavailable after the
Job leaves `RUNNING`. The API returns `status.sshUrl` only for that state.

## Run options

| Option | Contract |
| --- | --- |
| `IMAGE` | Required public Docker image. URLs and path traversal are rejected. |
| `COMMAND...` | Required program and arguments passed to the container. |
| `-e`, `--env KEY=VALUE` | Repeatable non-secret environment value. |
| `--env-file PATH` | Read non-secret dotenv entries; explicit `--env` values override same-name file entries. |
| `--secret NAME` | Repeatable local environment-variable name; the CLI seals its value into the request. |
| `--secrets-file PATH` | Read secret dotenv entries; values are sealed and never returned by the API. |
| `-l`, `--label KEY=VALUE` | Repeatable metadata used for filtering. |
| `--flavor NAME` | `cpu-nano` only; `cpu-basic` is accepted as a compatibility alias. Query `mega jobs hardware` instead of assuming any GPU flavor. |
| `--timeout DURATION` | Integer seconds or a duration such as `90s`, `10m`, or `1h`. |
| `--namespace HANDLE` | Personal or organization owner handle. Organization Jobs require admin access. |
| `--ssh` | Opt in to private SSH while the Job is `RUNNING`; use `mega jobs ssh JOB_ID` after acceptance. |
| `-d`, `--detach` | Return after acceptance instead of waiting. |
| `-t`, `--token` | Override `MEGA_TOKEN` or the active login for one command. |

Environment and secret keys must be valid environment-variable names. The same key cannot appear in both maps.

## Environment, secrets, and labels

```bash
export MEGA_JOB_TOKEN="secret-value"

mega jobs run --detach \
  -e MODE=release \
  --env-file .env.release \
  --secret MEGA_JOB_TOKEN \
  --secrets-file .env.secrets \
  -l lane=release \
  -l project=qwen \
  --timeout 20m \
  python:3.12-slim python task.py
```

`--secret MEGA_JOB_TOKEN` reads the local variable. The CLI does not print its value, and API responses expose only `secretsConfigured`, never stored secret values.

## Organization Jobs

Pass an organization handle explicitly:

```bash
mega jobs run --namespace research \
  python:3.12-slim python -c 'print("organization run")'

mega jobs ps --namespace research
```

The equivalent Web console route is:

```text
/settings/jobs?view=console&namespace=research
```

The namespace changes ownership and visibility; it is not merely a display filter.

## Observe and control

List execution history:

```bash
mega jobs list
mega jobs ps --status RUNNING --label lane=release
mega jobs ls --limit 100 --format json
```

`--status` and `--label` are repeatable. Limit must be between 1 and 100.

Inspect, read logs, wait, or cancel:

```bash
mega jobs inspect <job-id>
mega jobs logs <job-id>
mega jobs logs --follow --tail 100 <job-id>
mega jobs stats <job-id>
mega jobs stats <job-id> --namespace research
mega jobs wait <job-id> --timeout 900
mega jobs cancel <job-id>
```

Log tail accepts 1 through 5000 retained lines. `wait` returns a failing shell status for `CANCELED` and `ERROR`, which makes it safe for CI gates.

`stats` forwards the Compute Pool SSE stream. It reports CPU, memory, and network usage for a running CPU Job; the GPU field is an empty object because no GPU Job flavor is enabled.

## Job states

| State | Meaning |
| --- | --- |
| `SCHEDULING` | Accepted and waiting for available capacity. |
| `RUNNING` | Container execution has started. |
| `COMPLETED` | Container finished successfully. |
| `CANCELED` | A caller canceled scheduling or execution. |
| `ERROR` | Validation passed, but dispatch or execution failed. |

Terminal history remains inspectable. Cancel is valid only while work is scheduling or running.

## Recurring Jobs

Create a schedule with a UTC five-field cron expression or an alias:

```bash
mega jobs scheduled run \
  "*/15 * * * *" \
  python:3.12-slim \
  python task.py

mega jobs scheduled run \
  --namespace research \
  --timeout 20m \
  @hourly \
  python:3.12-slim \
  python task.py
```

Schedule a UV script with the same runtime boundary:

```bash
mega jobs scheduled uv run @hourly https://example.test/task.py --with requests
mega jobs scheduled uv run --ssh --suspend "*/15 * * * *" task.py
```

Manage definitions:

```bash
mega jobs scheduled list
mega jobs scheduled inspect <schedule-id>
mega jobs scheduled suspend <schedule-id>
mega jobs scheduled resume <schedule-id>
mega jobs scheduled trigger <schedule-id>
mega jobs scheduled delete <schedule-id>
```

`--suspend` creates a paused definition. `--concurrency` allows overlapping runs; without it, the scheduler avoids overlap. Deleting a schedule does not delete prior execution history.

## Python SDK

```python
from megatensors.hub import MegaHubClient

client = MegaHubClient()
job = client.run_job(
    image="python:3.12-slim",
    command=["python", "-c", "print('hello')"],
    env={"MODE": "release"},
    secrets={"MEGA_JOB_TOKEN": secret_value},
    labels={"lane": "release"},
    timeout="20m",
    namespace="research",
)

for line in client.fetch_job_logs(job.id, follow=True, tail=100):
    print(line)

for metric in client.fetch_job_metrics(job.id, namespace="research"):
    print(metric)

final = client.wait_for_job(job.id, timeout=1_800)
print(final.status.stage)
```

For UV script preparation, use `MegaApi`:

```python
from megatensors import MegaApi

api = MegaApi()
job = api.run_uv_job(
    "https://example.test/task.py",
    dependencies=["requests"],
    flavor="cpu-nano",
)
```

Create a recurring Job:

```python
schedule = client.create_scheduled_job(
    image="python:3.12-slim",
    command=["python", "task.py"],
    schedule="@hourly",
    namespace="research",
    timeout="20m",
)
```

See [Python SDK: Job methods](/docs/hub/sdk#job-methods) for the complete method map.

## Web API: create a Job

```bash
curl -X POST https://mega.tensorplay.cn/api/jobs \
  -H "Authorization: Bearer $MEGA_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{
    "dockerImage": "python:3.12-slim",
    "command": ["python", "-c", "print(\"hello\")"],
    "arguments": [],
    "environment": {"MODE": "release"},
    "secrets": {},
    "flavor": "cpu-nano",
    "timeoutSeconds": 1200,
    "ssh": {"enabled": true},
    "labels": {"lane": "release"},
    "namespace": "research"
  }'
```

The create response is `201` with `{ "job": ... }`.

## Web API: list and logs

```bash
curl 'https://mega.tensorplay.cn/api/jobs?limit=30&status=RUNNING&label=lane%3Drelease&namespace=research' \
  -H "Authorization: Bearer $MEGA_TOKEN"

curl 'https://mega.tensorplay.cn/api/jobs/<job-id>/logs?follow=true&tail=100' \
  -H "Authorization: Bearer $MEGA_TOKEN" \
  -H 'Accept: text/event-stream'
```

Logs are server-sent events. Treat each `data:` record as one retained output line.

## Web API endpoint map

| Method | Path | Response |
| --- | --- | --- |
| `GET` | `/api/jobs/hardware` | HF-compatible hardware array `[...]` |
| `GET` | `/api/jobs/usage` | Per-minute Jobs usage and accrued cost |
| `GET` | `/api/billing/compute` | Prepaid balance, cumulative spend, and recent history |
| `GET` | `/api/jobs` | `{ "jobs": [...] }` |
| `POST` | `/api/jobs` | `201 { "job": ... }` |
| `GET` | `/api/jobs/:jobId` | `{ "job": ... }` |
| `DELETE` | `/api/jobs/:jobId` | `{ "job": ... }` after cancellation |
| `GET` | `/api/jobs/:jobId/logs` | SSE log stream |
| `GET` | `/api/jobs/:namespace/:jobId/metrics` | SSE CPU, memory, and network metrics for a running Job |
| `GET` | `/api/jobs/scheduled` | `{ "scheduledJobs": [...] }` |
| `POST` | `/api/jobs/scheduled` | `201 { "scheduledJob": ... }` |
| `GET` | `/api/jobs/scheduled/:scheduleId` | `{ "scheduledJob": ... }` |
| `DELETE` | `/api/jobs/scheduled/:scheduleId` | `204` |
| `POST` | `/api/jobs/scheduled/:scheduleId/suspend` | `{ "scheduledJob": ... }` |
| `POST` | `/api/jobs/scheduled/:scheduleId/resume` | `{ "scheduledJob": ... }` |
| `POST` | `/api/jobs/scheduled/:scheduleId/trigger` | `201 { "job": ... }` |

MEGA also supports the documented Hugging Face-compatible Job request shapes
where listed in the OpenAPI reference. Not every Hugging Face compute feature
is available; unsupported fields or capabilities return `422` rather than
being silently ignored.

Use the live [Jobs OpenAPI operations](/spaces/mega/openapi#tag/Jobs) for the deployed method, path, authentication, and error contract.

## Troubleshooting

| Symptom | Check |
| --- | --- |
| `403` | Token has `jobs:run`; organization Jobs also require organization admin access. |
| `402` | Add enough prepaid compute credit to cover the requested timeout, or request a shorter timeout. |
| `503` during a credit top-up | The payment service is temporarily unavailable. Retry later or use your usual MEGA support channel. |
| `422 only cpu-nano...` | Remove an unsupported flavor. |
| `422 ...not available` | Remove GPU, Space image, unsupported volume configuration, or exposed-port options. |
| Timeout validation error | Keep requested runtime between 30 seconds and 1 hour. |
| Empty logs | Inspect Job state; execution may still be scheduling or may have produced no output. |
| `wait` exits non-zero | Inspect final state and retained logs; the Job did not complete successfully. |
