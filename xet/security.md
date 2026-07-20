# Xet Security

Xet-backed artifacts use the same repository visibility, ownership,
organization policy, and token permissions as other MEGA repository files.
Large-file storage does not make a private repository public or grant access to
an account that lacks repository permission.

## Protect release data

- Use a dedicated, least-privilege token for automation.
- Keep tokens, private keys, customer data, and unreviewed secrets out of the
  release tree.
- Verify the repository owner, type, and revision before uploading or copying
  a large directory.
- Use private repositories until the release is ready for distribution.
- Pin production consumers to a commit or tag.

## Work with organizations

Organization membership, roles, resource groups, and policy controls still
apply. Confirm the destination organization before starting a large transfer;
an automated upload should use the intended organization-owned credential.

See [Account Security](/docs/hub/security),
[Organizations](/docs/hub/organizations), and
[Resource Groups](/docs/hub/resource-groups) for shared-access guidance.
