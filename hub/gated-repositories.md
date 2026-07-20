# Gated Repositories

Gated access lets an author share a public model, dataset, or Space repository
only after an individual user accepts the repository terms and, when selected,
receives approval. It is useful when access requires a stated intended use or
license acknowledgement. It is not a substitute for a private repository:
choose a private repository when the resource itself must not be discoverable.

## Enable gated access

Open a public repository that you can manage and select **Access**. Enable
gated access, choose an approval mode, write a clear application heading, and
save the policy.

| Approval mode | What happens after a complete application |
| --- | --- |
| Automatic | Access is granted immediately. |
| Manual | The request remains pending until a repository manager accepts or rejects it. |

You can require applicants to confirm the repository license. Set a license in
the [Repository Card](/docs/hub/repository-cards) before enabling that option.
Existing approvals remain visible to repository managers after gated access is
disabled, but new readers no longer need to apply.

## Request access

Open the gated repository, sign in, and choose **Agree and request access**.
The form asks for a name, optional affiliation, intended use, contact-sharing
consent, and—when required—license confirmation. A verified account email and
a completed profile are required.

Automatic approval unlocks downloads after the request is accepted. Manual
approval keeps the request pending until a repository manager decides it. Track
your requests in **Settings → Gated Repositories**. A rejected request cannot
be submitted again for the same repository.

## Review requests

Repository managers open **Access** on the repository page to filter requests
by pending, accepted, or rejected status. Review the stated intended use before
accepting access. A rejection can include an optional reason visible to the
applicant.

Managers can export the access ledger as CSV or JSON. Treat it as sensitive:
it can contain the applicant's contact information, affiliation, intended use,
and recorded consent. Store it only where the repository's access policy
permits, and delete local copies when no longer needed.

## Public API

The [Hub API](/docs/hub/api) exposes the same workflow:

| Operation | Public route |
| --- | --- |
| Read or update the repository policy | `GET` or `PUT /api/repos/{owner}/{name}/gating` |
| Submit an application | `POST /api/repos/{owner}/{name}/access-requests` |
| List requests | `GET /api/repos/{owner}/{name}/access-requests` |
| Review a request | `PATCH /api/repos/{owner}/{name}/access-requests/{requestId}` |
| Export the ledger | `GET /api/repos/{owner}/{name}/access-requests/export?format=csv` |

Applications use the signed-in browser flow. Management operations require
repository write permission. Consult the live [OpenAPI Explorer](/spaces/mega/openapi)
for request schemas and current response fields.

## Publish responsibly

- Explain what access enables and which uses are appropriate.
- Keep the repository card, license, and requested terms consistent.
- Use manual review when the decision requires context beyond a checkbox.
- Do not request information that is unnecessary for the access decision.
- Revisit approvals and exports when the repository license or access policy
  changes.
