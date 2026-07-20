# Organization Network Security

Organization Network Security is an Enterprise control for limiting access to
organization resources from trusted networks. Administrators can define
approved IPv4 or IPv6 addresses and ranges, then choose how the organization
responds to traffic outside that boundary.

## Plan the policy first

Open **Organization Settings → Network Security** as an administrator. Keep at
least two administrators with a tested recovery path before restricting access.
Record the office, VPN, and automation network ranges that must continue to
reach the organization.

## Configure trusted networks

Enter one IPv4 or IPv6 address or CIDR range per line. You can add the current
network from the settings page, then add the remaining approved ranges before
enabling restrictions.

Choose the access posture that matches the organization:

- **Restrict access to trusted networks** limits organization pages and
  repository reads to the approved ranges.
- **Require sign-in on trusted traffic** requires visitors from an approved
  range to identify themselves before they can access organization work.

Save the policy only after confirming that the administrators and required
automation originate from a trusted range.

## Use content rules carefully

The policy can contain allow and block rules for content crossing the
organization boundary. Make each rule narrowly scoped, keep a written reason
for significant changes, and test a representative repository workflow after
saving. When an allow and block rule both match, the settings page explains the
precedence used by the policy.

Network Security complements, rather than replaces, repository visibility,
organization roles, [Single Sign-On](/docs/hub/organization-sso), tokens, and
[Resource Groups](/docs/hub/resource-groups).

## Recover from an access problem

If a legitimate administrator is blocked, use another administrator connected
from an approved network to correct the ranges or access posture. Do not remove
the last working administrator or enable a restrictive policy before confirming
the trusted ranges.

For account-level controls, see [Account Security](/docs/hub/security) and
[Organizations](/docs/hub/organizations).
