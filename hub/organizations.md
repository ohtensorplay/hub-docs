# Organizations

An organization is a shared owner namespace for repositories, Jobs, Spaces, billing, and security policy. People always sign in with personal accounts; membership connects that identity to one or more organizations with an explicit role.

```text
personal account -- membership(role) --> organization -- owns --> resources
```

## Create an organization

Open [New Organization](/organizations/new), choose a globally unique handle, and add a display name and profile context. The creator becomes the first `admin`.

Personal and organization handles share the same namespace. An organization is not a second login account, and switching the settings context does not change the authenticated person.

## Official organizations

An **Official publisher** badge identifies an organization whose publisher
identity has been verified by MEGA. Organization administration always uses a
named person's account and current organization role; there is no shared
organization login.

An organization badge and an Inference Provider badge are separate
verifications. Attaching a Provider to an organization does not by itself make
the Provider verified.

## Roles

MEGA uses five organization roles:

| Role | Read private repositories | Create repositories | Write repositories | Manage organization |
| --- | --- | --- | --- | --- |
| `no_access` | No | No | No | No |
| `read` | Yes | No | No | No |
| `contributor` | Yes | Yes | Only repositories that member created | No |
| `write` | Yes | Yes | Every organization repository | No |
| `admin` | Yes | Yes | Every organization repository | Profile, members, roles, billing, and policy |

Permissions are evaluated on every request from the caller's current membership. They are not copied permanently into a personal token.

An organization must always keep at least one `admin`. The final administrator cannot leave, be removed, or be demoted until another member is promoted. A platform-managed organization also cannot be removed automatically when its sole administrator deletes a personal account; another administrator must be promoted first.

## Add and manage members

Administrators can add an existing MEGA handle from **Organization Settings → Members**, assign the smallest suitable role, and change it later. They can also configure:

- an invitation link that can be rotated;
- signed-in join requests;
- optional automatic approval;
- the default role for invitation and approved-request joins.

Role changes take effect immediately for browser, API, Git, and compute access. Removing a member also removes that person's resource-group assignments and SSO exemptions for the organization.

## Publish in an organization namespace

Choose the organization as owner in [New Model](/new), [New Dataset](/new-dataset), or [New Space](/new-space), or use its handle in the repository ID:

```bash
mega repos create research/qwen-demo --type model --private
mega datasets upload research/evals ./evals .
```

The namespace is ownership, not a display filter. Organization repository defaults can require public, private, or private-only creation. A contributor may modify only repositories that person created; writers and administrators may modify every organization repository.

## Organization compute

Jobs and paid Spaces can charge the organization wallet instead of a personal wallet. The caller still needs the required membership and token scope:

```bash
mega jobs balance --namespace research
mega jobs run --namespace research python:3.12-slim python task.py
```

Organization Jobs currently require administrator access. See [Jobs](/docs/hub/jobs) and [Billing](/docs/hub/billing) for reservation and settlement behavior.

## Team and Enterprise controls

Paid organization plans enable additional policy surfaces:

| Capability | Minimum plan |
| --- | --- |
| Organization token governance | Team |
| [Storage regions](/docs/hub/organization-storage-regions) | Team |
| Single Sign-On | Team |
| Audit log and export | Team |
| Resource groups | Team |
| Advanced security policy | Team |
| [Publisher analytics](/docs/hub/publisher-analytics) | Team |
| Service accounts | Enterprise |
| SCIM provisioning | Enterprise |
| [Network security](/docs/hub/organization-network-security) | Enterprise |

The live organization settings page is authoritative for current entitlements. A disabled control may indicate the current plan does not include it rather than that the signed-in administrator lacks permission.

## Resource groups

Resource groups add repository-specific access on top of base membership. Start a person with `no_access`, place selected private repositories and members in one group, and assign the group's read or write role. Empty groups grant nothing.

Use resource groups when an organization-wide `read` or `write` role is too broad. Base membership, MFA policy, SSO, trusted-network rules, repository visibility, and resource-group permission all remain enforceable; a group does not bypass another security layer.

## Automation identities

Personal fine-grained tokens continue to act as the person who created them and lose organization access when that membership changes. Enterprise service accounts are organization-owned principals for automation that must outlive an individual member.

Create a dedicated service account and token for each automation boundary. Restrict its role and scopes, store the token in a secret manager, and disable the account before deleting or rotating dependent workflows.

## Operational checklist

- Keep at least two administrators for production organizations.
- Grant `contributor` instead of `write` when members should own only their own repositories.
- Use resource groups for project-specific private access.
- Require MFA and configure SSO before adding sensitive repositories.
- Use organization service accounts rather than personal credentials for long-lived Enterprise automation.
- Review the audit log after membership, role, SSO, token, or network-policy changes.
