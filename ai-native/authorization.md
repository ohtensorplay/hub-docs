# Authorize MEGA in Codex

The MEGA Codex plugin asks you to sign in and approve access when it first
needs your MEGA account. Authorization connects Codex to the account you
choose; it is not the same as making a repository public.

## Grant the smallest useful permission set

Choose read access for discovery, inspection, and documentation tasks. Add only
the scope needed for a write, community, or compute action. Review the consent
screen before approval, especially when an organization is involved.

| Capability | Scope |
| --- | --- |
| Repository, profile, paper, Space, community, and documentation reads | `repo:read` |
| Repository files/lifecycle and Space configuration | `repo:write` |
| Collections, posts, comments, discussions, and pull-request mutations | `community:write` |
| Jobs, scheduled Jobs, Sandboxes, and warm pools | `jobs:run` |

The connected tool surface does not accept secret values or provider keys, and
does not expose account-key, webhook, or repository-deletion actions. Use MEGA
protected settings or the authenticated CLI workflow for those tasks.

Organization policy and your membership role still apply. A permission grant
does not let Codex access repositories that your MEGA account cannot access.

## Use the intended account and organization

Before a task that writes or runs paid work, verify:

- the signed-in MEGA account;
- the repository or organization owner; and
- the billing owner for work that uses organization compute.

State the intended owner in your request when it matters. Do not paste access
tokens, passwords, or private keys into a Codex prompt.

## Revoke access

Remove the MEGA connection from your MEGA security settings when you no longer
want Codex to access your account. Removing a plugin from Codex does not by
itself revoke an authorization that was already granted.

For account safety guidance, see [Account Security](/docs/hub/security) and
[Access Tokens](/docs/hub/access-tokens).
