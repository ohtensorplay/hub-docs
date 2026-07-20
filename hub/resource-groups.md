# Resource Groups

Resource groups add repository-specific access controls for an organization.
Use them when an organization-wide role would grant more access than a project
needs.

Start a member with the smallest base role, add the person and selected private
repositories to a group, then assign the group read or write access. A resource
group does not override repository visibility, MFA, SSO, or another applicable
organization policy.

Review group membership after a project changes and audit administrative
updates through [Audit Logs](/docs/hub/audit-logs).
