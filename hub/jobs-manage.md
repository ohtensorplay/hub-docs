# Manage Jobs

Use the Job ID returned by `mega jobs run` to inspect, stream, cancel, or wait
for a Job.

```bash
mega jobs ps --namespace OWNER
mega jobs inspect JOB_ID
mega jobs logs JOB_ID --tail 100
mega jobs logs JOB_ID --follow
mega jobs stats JOB_ID
mega jobs cancel JOB_ID
mega jobs wait JOB_ID --timeout 10m
```

For a Job created with `--ssh`, `mega jobs ssh JOB_ID` opens a private shell
only while its state is `RUNNING`.

`wait` returns a non-zero exit code when the Job does not complete successfully.
Read the final state and retained logs before retrying. A new run is safer than
trying to alter a completed Job's inputs.
