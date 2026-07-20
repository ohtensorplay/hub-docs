# Job Examples

Run a small command first, then add explicit inputs, labels, and timeout:

```bash
mega jobs run --detach --timeout 10m \
  -e MODE=report \
  -l project=demo \
  python:3.12-slim python -c 'print("report")'
```

For a scheduled task, create the schedule only after this command has completed
successfully. Keep complex scripts in a versioned repository and pass a pinned
revision to the Job workflow.
