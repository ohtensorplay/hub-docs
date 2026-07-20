# Service Accounts

Enterprise service accounts are organization-owned identities for automation
that must outlive an individual member. Use one service account and one
scoped token per automation boundary.

## Recommended workflow

1. Create the service account in organization settings.
2. Assign the smallest organization role that can perform the work.
3. Create a token with only the required scopes.
4. Store the token in the workload's secret manager.
5. Review and rotate the token on a regular schedule.

Disabling a service account stops its future access. Use [Audit Logs](/docs/hub/audit-logs)
and [Account Security](/docs/hub/security) to review changes.
