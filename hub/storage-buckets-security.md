# Bucket Security

Use private Buckets for artifacts that are not ready for public distribution.
Visibility controls reads; ownership, organization role, resource group, and
token scope control writes and deletion.

## Protect data

- Use a dedicated, scoped token for automation.
- Do not place a token, private key, or unreviewed personal data in a Bucket.
- Review the target before recursive remove or `sync --delete`.
- Keep a versioned repository record for important release inputs.
- Remove local copies according to your own retention requirements.

An inaccessible private Bucket is presented as unavailable. See
[Account Security](/docs/hub/security) and [Organizations](/docs/hub/organizations)
for shared-access controls.
