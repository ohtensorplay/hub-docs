# Continuous Integration

Use a scoped MEGA token in CI to validate, publish, or download a pinned
release. Keep the token in your CI secret store and grant only the scopes the
workflow needs.

## Publish from CI

```bash
export MEGA_TOKEN="$MEGA_TOKEN"
mega repos info OWNER/REPOSITORY --format json
mega upload OWNER/REPOSITORY ./dist --revision main --commit-message "CI release"
```

Use `repo:read` for a download-only workflow and `repo:write` only for a job
that creates or updates repository content. Add `repo:delete` only when a
separate, reviewed workflow must delete a resource.

## Protect credentials

- Store tokens as masked CI secrets, never in repository files or logs.
- Prefer a dedicated token or organization service account for each workflow.
- Pin input downloads to a tag or commit.
- Revoke and replace a token immediately if it appears in output or an issue.

See [Authentication](/docs/hub/authentication) and [Account Security](/docs/hub/security)
for token lifecycle guidance.
