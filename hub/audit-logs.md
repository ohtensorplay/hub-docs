# Audit Logs

Eligible organizations can review an audit log of important membership,
repository, access-policy, and administration changes. Open **Organization
Settings → Audit Log** to filter activity, then export the visible result when
your compliance process requires a record.

The public API exposes organization audit-log read and export operations. It
requires organization administrative permission; use the [OpenAPI Explorer](/spaces/mega/openapi)
for the current query and export schema.

Audit records support accountability, but they do not replace your own incident
response or retention policy. Limit exports to authorized people and avoid
placing them in a public repository.
