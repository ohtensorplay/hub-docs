# Job Configuration

Configure a Job with a public image, command, timeout, non-secret environment
variables, secret names, labels, and optional organization namespace.

```bash
export API_TOKEN="secret-value"
mega jobs run --detach \
  -e MODE=release \
  --secret API_TOKEN \
  -l project=demo \
  --timeout 20m \
  python:3.12-slim python task.py
```

Use `-e` only for non-secret values. `--secret NAME` reads a local environment
variable without printing its value. Unsupported capabilities return `422`;
do not assume that a Docker option is available merely because another service
accepts it.

`--env-file` and `--secrets-file` accept dotenv files. Values passed with
`--env` override same-name non-secret file entries; secret file values are
sealed before the Job is stored and are never returned by the API.

For a temporary private shell, use `--ssh` with a long-running detached Job.
The account needs a registered SSH key and can connect only while its Job is
running: `mega jobs ssh JOB_ID`. This does not expose a container port.

For a UV script, use `mega jobs uv run SCRIPT --with PACKAGE`; the same
environment, secret, mount, timeout, namespace, and private SSH options apply.
