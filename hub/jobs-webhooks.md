# Job Webhook Automation

Use [Webhooks](/docs/hub/webhooks) to notify an external HTTPS service about
repository events that should trigger a Job workflow. Verify the signed payload
before acting on it, and make the receiver idempotent with the delivery ID.

Do not put a MEGA token in a webhook URL or response body. Store it in the
receiver's secret manager, use only the necessary scope, and submit a Job after
validating the repository, revision, and event type.

For a recurring task that does not need an external event, use
[Schedule Jobs](/docs/hub/jobs-schedule) instead.
