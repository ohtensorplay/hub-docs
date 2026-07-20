# Schedule Jobs

Create a recurring Job when a command should run on a defined schedule. Verify
the first run manually before relying on a schedule for a production workflow.

```bash
mega jobs schedule create \
  --name nightly-report \
  --cron "0 2 * * *" \
  python:3.12-slim python report.py
```

Use the Jobs console or CLI to list, suspend, resume, trigger, and delete a
schedule. Scheduled runs use the owner, command, timeout, and billing rules
recorded for the schedule; review those settings whenever the script changes.

See [Jobs](/docs/hub/jobs) for the current public API and state model.
