# Service status and support

This page helps Hub users diagnose an unavailable page, API call, Git
operation, Space, or Job. It covers user-visible service status and support
only.

## Start with the affected product

Use the product page that matches the operation:

| Operation | First reference |
| --- | --- |
| Repository, file, Git, or revision | [Hub Repositories](/docs/hub/repositories) |
| Token, browser sign-in, or SSH key | [Authentication](/docs/hub/authentication) |
| Space build or runtime | [Spaces](/docs/hub/spaces) |
| Job submission, logs, or schedule | [Jobs](/docs/hub/jobs) |
| API response or schema | [Hub API](/docs/hub/api) |
| Request throttling | [Rate limits](/docs/hub/rate-limits) |

For the precise public API schema, use the live [OpenAPI Explorer](/spaces/mega/openapi).

## Recover safely

1. Confirm that you are signed in to the intended account or organization and
   that your token has the required scope.
2. Retry only idempotent reads after a short delay. For `429`, wait for the
   `Retry-After` value before retrying.
3. For an upload or download, use the CLI's resume or sync workflow instead of
   starting concurrent copies of the same destination.
4. For a failed Space or Job, inspect its user-visible logs, correct the
   repository or command, and create a new revision or run.
5. Do not share access tokens, private keys, secret values, or private artifact
   contents while seeking help.

## Report a reproducible problem

Include the public resource URL, the time of the failure with timezone, the
operation you attempted, the HTTP status or CLI error, and any safe request or
run identifier shown to you. Redact credentials and private data. This is
usually enough to distinguish a permissions problem from a transient service
failure without exposing sensitive information.

## Compatibility expectations

MEGA evolves public APIs additively where possible. Pin automation to an
explicit repository revision, preserve unknown response fields, and consult the
OpenAPI document before depending on a newly added field or operation.
