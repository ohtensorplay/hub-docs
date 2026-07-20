# Jobs Quickstart

Install the CLI, sign in, inspect available hardware, and run a small command:

```bash
uv tool install megatensors
mega auth login
mega jobs hardware
mega jobs run python:3.12-slim python -c 'print("hello")'
```

Use `--detach` to return after the Job is accepted, then inspect and stream its
output:

```bash
mega jobs ps
mega jobs logs JOB_ID --follow
mega jobs stats JOB_ID
mega jobs inspect JOB_ID
```

For a private interactive shell, add `--ssh` to a long-running detached Job
and use `mega jobs ssh JOB_ID` once it is running. Register the client key with
`mega auth keys add` first; no public Job port is opened.

Jobs require the `jobs:run` scope and enough prepaid compute credit when the
selected runtime is metered. See [Jobs](/docs/hub/jobs).
