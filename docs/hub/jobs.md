# Jobs


MEGA Jobs runs a public Docker image and command on managed compute. The
interface is intentionally Docker-like: dispatch with `run`, discover active
work with `ps`, stream output with `logs`, and inspect retained metadata with
`inspect`.

Open the [Jobs settings page](/settings/jobs) for guided examples. Use [Web console](/settings/jobs?view=console) for browser dispatch and history, or [CLI reference](/settings/jobs?view=cli) for a compact in-product command matrix.

## Choose an interface

| Interface | Use it for | Exact reference |
| --- | --- | --- |
| Web console | Guided dispatch, retained history, logs, and schedules in the browser. | [Open the Jobs console](/settings/jobs?view=console) |
| MEGA CLI | Interactive work, shell scripts, and CI exit codes. | [Jobs CLI workflows](/docs/megatensors/guides/cli#jobs-workflows) |
| Python SDK | Typed orchestration from Python applications. | [Job methods](/docs/megatensors/package_reference/hub_client#job-methods) |
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
| Output | Retained logs with bounded tail and optional follow mode |
| Not enabled | GPU, TPU, volumes, Space images, SSH, and exposed ports |

Use `mega jobs hardware` or `GET /api/v1/jobs/hardware` as the runtime source of truth. Unsupported capabilities return `422` instead of being silently ignored.

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
share the same owner wallet. Usage responses provide cumulative totals and
recent Job details.

## Run a Job

```bash
mega jobs run python:3.12-slim \
  python -c 'print("Hello from the cloud!")'
```

Foreground mode waits for terminal state, prints retained output, and exits non-zero unless the Job reaches `COMPLETED`.

Return as soon as the runner accepts the Job:

```bash
mega jobs run --detach python:3.12-slim python task.py
```

## Run options

| Option | Contract |
| --- | --- |
| `IMAGE` | Required public Docker image. URLs and path traversal are rejected. |
| `COMMAND...` | Required program and arguments passed to the container. |
| `-e`, `--env KEY=VALUE` | Repeatable non-secret environment value. |
| `--secret NAME` | Repeatable local environment-variable name; the CLI seals its value into the request. |
| `-l`, `--label KEY=VALUE` | Repeatable metadata used for filtering. |
| `--timeout DURATION` | Integer seconds or a duration such as `90s`, `10m`, or `1h`. |
| `--namespace HANDLE` | Personal or organization owner handle. Organization Jobs require admin access. |
| `-d`, `--detach` | Return after acceptance instead of waiting. |
| `-t`, `--token` | Override `MEGA_TOKEN` or the active login for one command. |

Environment and secret keys must be valid environment-variable names. The same key cannot appear in both maps.

## Environment, secrets, and labels

```bash
export MEGA_JOB_TOKEN="secret-value"

mega jobs run --detach \
  -e MODE=release \
  --secret MEGA_JOB_TOKEN \
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
mega jobs wait <job-id> --timeout 900
mega jobs cancel <job-id>
```

Log tail accepts 1 through 5000 retained lines. `wait` returns a failing shell status for `CANCELED` and `ERROR`, which makes it safe for CI gates.

## Job states

| State | Meaning |
| --- | --- |
| `SCHEDULING` | Accepted and waiting for runner allocation. |
| `RUNNING` | Container execution has started. |
| `COMPLETED` | Container finished successfully. |
| `CANCELED` | A caller canceled scheduling or execution. |
| `ERROR` | Validation passed, but dispatch or execution failed. |

Terminal history remains inspectable. Cancel is valid only while work is scheduling or running.

## Recurring Jobs

Create a schedule with a UTC five-field cron expression or an alias:

```bash
mega jobs schedule create \
  python:3.12-slim \
  "*/15 * * * *" \
  python task.py

mega jobs schedule create \
  --namespace research \
  --timeout 20m \
  python:3.12-slim \
  @hourly \
  python task.py
```

Manage definitions:

```bash
mega jobs schedule list
mega jobs schedule inspect <schedule-id>
mega jobs schedule pause <schedule-id>
mega jobs schedule resume <schedule-id>
mega jobs schedule trigger <schedule-id>
mega jobs schedule delete <schedule-id>
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

final = client.wait_for_job(job.id, timeout=1_800)
print(final.status.stage)
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

See [Hub Python SDK: Job methods](/docs/megatensors/package_reference/hub_client#job-methods) for the complete method map.

## Web API: create a Job

```bash
curl -X POST https://mega.tensorplay.cn/api/v1/jobs \
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
    "labels": {"lane": "release"},
    "namespace": "research"
  }'
```

The create response is `201` with `{ "job": ... }`.

## Web API: list and logs

```bash
curl 'https://mega.tensorplay.cn/api/v1/jobs?limit=30&status=RUNNING&label=lane%3Drelease&namespace=research' \
  -H "Authorization: Bearer $MEGA_TOKEN"

curl 'https://mega.tensorplay.cn/api/v1/jobs/<job-id>/logs?follow=true&tail=100' \
  -H "Authorization: Bearer $MEGA_TOKEN" \
  -H 'Accept: text/event-stream'
```

Logs are server-sent events. Treat each `data:` record as one retained output line.

## Web API endpoint map

| Method | Path | Response |
| --- | --- | --- |
| `GET` | `/api/v1/jobs/hardware` | `{ "hardware": [...] }` |
| `GET` | `/api/v1/jobs/usage` | Per-minute Jobs usage and accrued cost |
| `GET` | `/api/v1/billing/compute` | Prepaid balance, cumulative spend, and recent ledger |
| `GET` | `/api/v1/jobs` | `{ "jobs": [...] }` |
| `POST` | `/api/v1/jobs` | `201 { "job": ... }` |
| `GET` | `/api/v1/jobs/:jobId` | `{ "job": ... }` |
| `DELETE` | `/api/v1/jobs/:jobId` | `{ "job": ... }` after cancellation |
| `GET` | `/api/v1/jobs/:jobId/logs` | SSE log stream |
| `GET` | `/api/v1/jobs/scheduled` | `{ "scheduledJobs": [...] }` |
| `POST` | `/api/v1/jobs/scheduled` | `201 { "scheduledJob": ... }` |
| `GET` | `/api/v1/jobs/scheduled/:scheduleId` | `{ "scheduledJob": ... }` |
| `DELETE` | `/api/v1/jobs/scheduled/:scheduleId` | `204` |
| `POST` | `/api/v1/jobs/scheduled/:scheduleId/suspend` | `{ "scheduledJob": ... }` |
| `POST` | `/api/v1/jobs/scheduled/:scheduleId/resume` | `{ "scheduledJob": ... }` |
| `POST` | `/api/v1/jobs/scheduled/:scheduleId/trigger` | `201 { "job": ... }` |

Use the live [Jobs OpenAPI operations](/spaces/mega/openapi#tag/Jobs) for the deployed method, path, authentication, and error contract.

## Troubleshooting

| Symptom | Check |
| --- | --- |
| `403` | Token has `jobs:run`; organization Jobs also require organization admin access. |
| `402` | Add enough prepaid compute credit to cover the requested timeout, or request a shorter timeout. |
| `422 only cpu-nano...` | Remove an unsupported flavor. |
| `422 ...not available` | Remove GPU, volumes, Space image, SSH, or exposed-port options. |
| Timeout validation error | Keep requested runtime between 30 seconds and 1 hour. |
| Empty logs | Inspect Job state; execution may still be scheduling or may have produced no output. |
| `wait` exits non-zero | Inspect final state and retained logs; the Job did not complete successfully. |
