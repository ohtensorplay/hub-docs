# Trusted Publishers

Use a short-lived workload identity where your CI platform and MEGA integration
support it, instead of putting a long-lived personal token in every workflow.
Trusted publishing reduces secret distribution and makes it easier to revoke a
single automation boundary.

Before enabling a workflow, confirm its repository, branch or tag policy,
organization identity, and requested publication scopes. Keep a fallback
recovery process that does not require sharing a personal token.

For a conventional token-based workflow, see
[Continuous Integration](/docs/hub/repositories-ci).
