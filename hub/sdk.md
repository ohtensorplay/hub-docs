# Python SDK

The Python SDK is the application entry point for automating MEGA Hub from
Python. It talks to the same Hub API used by the web application and CLI; it
does not proxy repository operations through Hugging Face.

Install the package and configure an access token:

```bash
pip install megatensors
export MEGA_TOKEN=YOUR_MEGA_TOKEN
```

Set `MEGA_ENDPOINT` only for a staging or self-hosted Hub. Production defaults
to `https://mega.tensorplay.cn`.

## Native Hub client

`MegaHubClient` is the typed client for the current MEGA public contract:

```python
from megatensors.hub import MegaHubClient

client = MegaHubClient()
client.create_repo("research/demo", repo_type="model", private=True, exist_ok=True)
client.upload_file("research/demo", "./config.json", path_in_repo="config.json")
```

Use it for repository files and revisions, discussions, webhooks, account
keys, Storage Buckets, and Jobs. The complete REST request and response shapes
are in the [Hub API](/docs/hub/api) and the live
[OpenAPI Explorer](/spaces/mega/openapi).

## Hub-compatible imports

`megatensors.mega_hub` provides the evaluated Hugging Face Hub import surface
while retaining MEGA transport semantics:

```python
from megatensors.mega_hub import HfApi, HfFileSystem, hf_hub_download

api = HfApi()
local_path = hf_hub_download("research/demo", "config.json")
```

`HfApi`, `HfFileSystem`, `HfFileMetadata`, `HfUri`, cache, OAuth, URL, and
TensorBoard names are aliases of the corresponding MEGA implementation. For
example, `hf_hub_download` and `hf_hub_url` resolve against MEGA's configured
artifact service. New integrations can use the canonical `Mega*` spellings.

This compatibility layer covers Hub repository workflows; it does not claim a
managed Inference Endpoint lifecycle product.

## Job methods

Use the live hardware catalogue before dispatching a Job. The catalogue is the
source of truth for CPU, RAM, accelerator, and price; an SDK never invents a
GPU flavor that is not currently available.

```python
from megatensors.hub import MegaHubClient

client = MegaHubClient()
hardware = client.list_jobs_hardware()
job = client.run_job(
    image="python:3.12-slim",
    command=["python", "-c", "print('hello')"],
    labels={"lane": "release"},
)
for metric in client.fetch_job_metrics(job.id):
    print(metric["cpu_usage_pct"])
final = client.wait_for_job(job.id, timeout=900)
```

Jobs support dispatch, logs, cancellation, labels, schedules, mounted
repositories or Buckets, and opt-in private SSH according to the current
service contract. `fetch_job_metrics` streams CPU, memory, and network samples
for a running Job. Create a long-running Job with `ssh=True`; while it is
`RUNNING`, `job.status.ssh_url` is populated and the CLI can connect with
`mega jobs ssh JOB_ID`. The connection requires a registered account SSH key,
write ownership of the Job, and the authenticated SSH URL returned for that
Job. MEGA does not provide Hugging Face's free-form Job `name` field.

See [Jobs](/docs/hub/jobs), [Job Configuration](/docs/hub/jobs-configuration),
and [Storage Buckets](/docs/hub/storage-buckets) for current limits and volume
semantics.

For a UV script, `MegaApi.run_uv_job` and `create_scheduled_uv_job` build an
ordinary Job or scheduled Job. They use the same live CPU flavor, Bucket
mounting, secret, and SSH rules as `MegaHubClient`; they do not add GPU or
public port support.

## Space methods

The compatibility client exposes Space metadata, runtime controls, variables,
and secrets:

```python
from megatensors import MegaApi

api = MegaApi()
space = api.space_info("research/demo-space")
runtime = api.get_space_runtime("research/demo-space")
```

Call `list_spaces_hardware()` before requesting hardware. It returns the live
MEGA catalogue; compatibility enum values are not availability guarantees.
See [Spaces](/docs/hub/spaces) and [Space Hardware](/docs/hub/spaces-hardware).

## Routed inference SDK

Inference stays in the SDK, separate from Hub repository operations and from
the CLI/MCP Hub surface:

```python
from megatensors import InferenceClient

client = InferenceClient(provider="auto")
response = client.chat.completions.create(
    model="mega/gpt-5.4-mini",
    messages=[{"role": "user", "content": "Hello"}],
)
```

`InferenceClient` uses MEGA's OpenAI-compatible inference router. See the
[Inference Provider guides](/docs/inference-providers/guides/first-api-call)
for routing and provider-specific behavior.
