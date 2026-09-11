# Account Security

MEGA protects browser sessions, API tokens, Git credentials, organization access, and destructive operations as separate security boundaries. Use the controls together: a strong browser sign-in does not make an over-scoped automation token safe.

## Security control map

| Control | Where to manage it | Protects |
| --- | --- | --- |
| Password and connected sign-in | [Sign-in Security](/settings/authentication) | Browser account authentication. |
| Passkeys | [Sign-in Security](/settings/authentication) | Phishing-resistant browser authentication. |
| TOTP multi-factor authentication | [Sign-in Security](/settings/authentication) | Browser login, device approval, and sensitive account actions. |
| Browser sessions | [Sign-in Security](/settings/authentication) | Signed-in browsers and session revocation. |
| Fine-grained tokens | [Access Tokens](/settings/tokens) | CLI, API, MCP, and automation permissions. |
| SSH public keys | [SSH & GPG Keys](/settings/keys) | Git-over-SSH account authentication. |
| GPG public keys | [SSH & GPG Keys](/settings/keys) | Commit-signature verification. |
| GitHub Actions identities | [Sign-in Security](/settings/authentication) | Short-lived CI tokens exchanged from OIDC. |
| OAuth applications | [Connected Applications](/settings/connected-applications) | Third-party delegated account access. |

See [Passkeys](/docs/hub/passkeys) for registration, sign-in, removal, and recovery guidance. See [Authentication](/docs/hub/authentication) for exact CLI login, token scopes, SSH hostnames, and key commands.

## Enable TOTP MFA

Open **Settings → Sign-in Security**, add an authenticator, scan the enrollment secret, and confirm a current six-digit code. Once enabled, MEGA requires a fresh TOTP challenge during supported browser and CLI device flows and for high-risk account operations.

Enrollment and login challenges expire after ten minutes. Failed attempts are capped, and one time step cannot be replayed. Keep the authenticator seed out of screenshots, tickets, repository files, and synced notes.

## Use scoped access tokens

Create one token for one automation boundary and select only the scopes it needs. A common deployment reader needs `repo:read`; repository publication adds `repo:write`; compute adds `jobs:run` or `inference:run` according to the service.

Inference workloads should also follow the content, Provider-key, browser, and incident-reporting boundaries in [Inference Security and Privacy](/docs/inference-providers/security).

```bash
export MEGA_TOKEN="$(secret-tool lookup service mega-ci)"
mega auth whoami --format json
```

MEGA displays a new token secret only at creation. Store it immediately in the target secret manager. Token inventory retains the name, prefix, scopes, kind, creation time, and expiration rather than the recoverable plaintext secret.

Revoke a credential when its purpose ends, its storage boundary changes, or it may have appeared in logs. Rotating a secret means creating the replacement, updating consumers, verifying them, and then revoking the old token.

## Secure Git access

Use the dedicated `git.tensorplay.cn` data plane for HTTPS Git and
`ssh.tensorplay.cn` for SSH Git. Existing `mega.tensorplay.cn` Git remotes
remain compatible through a streamed fallback. Upload only the `.pub` half of
an SSH key:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_mega
mega auth keys add ~/.ssh/id_ed25519_mega.pub --name "Work laptop"
ssh -T git@ssh.tensorplay.cn
```

MEGA rejects private SSH and GPG key material before registration. Compare the server key with the fingerprint published in [Authentication](/docs/hub/authentication) before accepting a new SSH host identity.

GPG verification proves that a commit signature matches an active registered public key and the MEGA author email. It does not prove the safety or quality of the committed artifact. Apply [Signing and Trust](/docs/hub/trust) when the artifact itself also needs certificate-backed provenance.

## Prefer short-lived CI identity

For GitHub Actions, register the repository, optional branch, and optional workflow filename as a CI identity. A matching job exchanges GitHub's signed OIDC token for a one-hour MEGA token; no long-lived `MEGA_TOKEN` needs to be saved in GitHub.

The exchange validates GitHub's issuer and signature, the MEGA audience, immutable repository and owner IDs, and the configured branch or workflow. Each GitHub JWT can be exchanged once. The resulting token is intentionally limited and does not inherit arbitrary private-repository access.

## Review sessions and connected apps

The session list shows signed-in browsers and lets you revoke one session or every other session. Revoke unfamiliar sessions before changing credentials so an attacker cannot keep using an already authenticated browser.

Connected Applications lists OAuth grants separately from applications you operate. Revoking a grant removes that application's future access without deleting the OAuth client. Rotating a client secret invalidates the old client credential; update the application's secure backend before completing the rotation.

## Organization security

Team and Enterprise organizations can layer required MFA, SSO, SCIM, resource groups, trusted-network rules, repository visibility policy, service accounts, and audit logs on top of membership roles. These controls compose: satisfying SSO does not bypass a network rule, and resource-group access does not bypass required MFA.

See [Organizations](/docs/hub/organizations) for role and entitlement boundaries.

## If a credential is exposed

1. Revoke the token, session, key, OAuth grant, or CI identity immediately.
2. Remove the secret from the current repository state and rotate it at the upstream service.
3. Assume Git history, build logs, discussion attachments, and downloaded artifacts may retain copies.
4. Review account and organization audit events for unexpected reads, writes, or policy changes.
5. Replace downstream credentials that were reachable through the exposed secret.
6. Report abuse or a platform vulnerability through the appropriate MEGA security channel; do not post live secrets in a public discussion.
