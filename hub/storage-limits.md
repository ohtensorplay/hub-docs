# Storage Limits

Repository and Bucket limits protect shared service capacity. The applicable
limit depends on the owner, plan, repository type, and requested operation.
The create form, CLI, and API return the current result for the specific
resource; do not hard-code an assumed quota in automation.

## Plan a large release

Keep source, configuration, cards, and release metadata in the repository.
Use the resumable upload command for large trees and a
[Storage Bucket](/docs/hub/storage-buckets) for mutable checkpoints, logs, or
intermediate data that do not need Git history.

```bash
mega upload-large-folder OWNER/REPOSITORY ./release --revision main
mega buckets sync ./checkpoints mega://buckets/OWNER/checkpoints
```

## Respond to a limit error

An upload can be rejected when it exceeds a current file, request, repository,
or entitlement limit. Check the error message, reduce the operation to the
intended files, and retry with the documented client. Do not split a release
across unrelated repositories merely to bypass a limit.

For a temporary `429`, follow [Rate limits](/docs/hub/rate-limits). For plan
availability, consult [Billing](/docs/hub/billing).
