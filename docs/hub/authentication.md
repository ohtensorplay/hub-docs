# Authentication


MEGA uses the same authentication boundary across the CLI, Python SDK, and Web API. Interactive users normally use the browser device flow; automation should use a fine-grained bearer token with only the scopes it needs.

## Choose an authentication flow

| Context | Recommended flow | Result |
| --- | --- | --- |
| Developer workstation | `mega auth login` | Opens the browser device flow and stores the active token locally. |
| CI or headless automation | `MEGA_TOKEN` environment variable | Does not write a token to the workspace. |
| Direct Web API client | `Authorization: Bearer <token>` | Authenticates one HTTP request. |
| Multiple local accounts | `mega auth login`, then `mega auth switch` | Keeps named tokens and selects one active token. |

## Browser device login

```bash
mega auth login
```

The CLI requests a short-lived device code, opens or prints the verification URL, and waits until the browser flow is approved. Use `--force` to start a new login when a token is already active:

```bash
mega auth login --force
```

Confirm the selected account:

```bash
mega auth whoami
mega auth whoami --format json
```

## Token login and environment variables

Pass a token directly when an interactive device flow is not appropriate:

```bash
mega auth login --token "$MEGA_TOKEN"
```

The client resolves configuration in this order:

| Variable | Purpose |
| --- | --- |
| `MEGA_TOKEN` | Overrides the locally selected access token. |
| `MEGA_ENDPOINT` | Overrides the default `https://mega.tensorplay.cn` service endpoint. |
| `MEGA_HOME` | Changes the MEGA configuration and cache root. |
| `MEGA_DEBUG=1` | Prints full CLI tracebacks for diagnostics. |

For CI, prefer an injected secret and avoid persisting it:

```bash
export MEGA_TOKEN="${{ secrets.MEGA_TOKEN }}"
mega repos info mega/release --format json
```

## Token scopes

Fine-grained personal tokens use explicit scopes:

| Scope | Allows |
| --- | --- |
| `repo:read` | Read private repositories available to the token owner. |
| `repo:write` | Create repositories and write files, refs, and metadata. |
| `repo:delete` | Permanently delete repositories. |
| `community:write` | Create and update discussions, replies, reactions, and pull requests. |
| `jobs:run` | Create, inspect, list, cancel, and schedule Jobs. |
| `account:keys` | Manage account SSH and GPG public keys. |
| `webhooks:manage` | Manage account webhook routes and delivery receipts. |

Use separate tokens for unrelated systems. A deployment reader should not also receive `repo:delete` or `webhooks:manage`.

## Stored-token commands

```bash
mega auth list
mega auth switch --token-name workstation
mega auth token
mega auth logout --token-name workstation
```

`mega auth token` writes the active secret to stdout. Use it only in a controlled pipeline and never paste its output into logs.

## Public keys

Only public SSH and GPG keys are accepted. Private-key material is rejected before the CLI sends a request.

```bash
mega auth keys add ~/.ssh/id_ed25519.pub --name "Work laptop"
mega auth keys add signing-key.asc --type gpg --name release
mega auth keys list --format json
mega auth keys delete <key-id>
```

Account-key operations require `account:keys` when called with a fine-grained token.

## SSH Git authentication

Generate a dedicated Ed25519 key, upload only its public half, and verify the host greeting:

> **HTTPS and SSH use different published hostnames.** Use
> `git.tensorplay.cn` directly for HTTPS Git and `ssh.tensorplay.cn` for SSH.
> Existing Git remotes on `mega.tensorplay.cn` remain compatible through a
> streamed fallback; use the dedicated HTTPS host for new clones.

```bash
ssh-keygen -t ed25519 -C "$USER@$(hostname)" -f ~/.ssh/id_ed25519_mega
mega auth keys add ~/.ssh/id_ed25519_mega.pub --name "Work laptop"

cat >> ~/.ssh/config <<'EOF'
Host ssh.tensorplay.cn
  User git
  IdentityFile ~/.ssh/id_ed25519_mega
  IdentitiesOnly yes
EOF

ssh -T git@ssh.tensorplay.cn
git clone git@ssh.tensorplay.cn:OWNER/REPOSITORY
```

The SSH key authenticates the account; normal repository permissions still decide read or write access. Removing the key immediately prevents new SSH authorization.

Before accepting a new host key, compare the fingerprint displayed by your SSH
client with the published production fingerprint:

```text
ED25519  SHA256:z30WKULbmCe/Y0z/si4ETbXjzoQ8bOHeS2q0PCxI5WE
```

You can inspect the presented key with the following command. `ssh-keyscan`
does not authenticate a server by itself; trust the result only after comparing
it with the fingerprint above.

```bash
ssh-keyscan -t ed25519 ssh.tensorplay.cn 2>/dev/null | ssh-keygen -lf -
```

If SSH is temporarily unavailable, use HTTPS Git and retry later.

## GPG commit verification

The armored public key must contain the verified email address of the MEGA account. Keep the private key local:

```bash
gpg --armor --export ACCOUNT_EMAIL > mega-signing-key.asc
mega auth keys add mega-signing-key.asc --type gpg --name "Release signing"
gpg --list-secret-keys --keyid-format=long ACCOUNT_EMAIL

git config user.email ACCOUNT_EMAIL
git config user.signingkey GPG_KEY_ID
git config commit.gpgsign true
git commit -S -m "Signed release"
git push
mega repos history OWNER/REPOSITORY --format json
```

A commit is `verified` only when its signature validates with an active registered key and the Git author email matches the MEGA identity. Otherwise it is shown as `unverified` or `unsigned`; the CLI includes the verified signer fingerprint for audit.

## Web API authentication

```bash
curl https://mega.tensorplay.cn/api/v1/whoami \
  -H "Authorization: Bearer $MEGA_TOKEN"
```

JSON writes also require a content type:

```bash
curl -X POST https://mega.tensorplay.cn/api/v1/repos \
  -H "Authorization: Bearer $MEGA_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{"repo_id":"alice/demo","repo_type":"model","private":true}'
```

See the live [OpenAPI Explorer](/spaces/mega/openapi#tag/OAuth) for the exact OAuth methods and response contract.
Clients should also handle `429 Too Many Requests`; current buckets and retry headers are documented in [Rate limits](/docs/hub/rate-limits).

## Security checklist

- Keep `MEGA_TOKEN` out of command history, repository files, build artifacts, and Job environment output.
- Prefer `--secret NAME` over `-e NAME=value` for Job secrets.
- Use the narrowest token scope and a dedicated token per automation boundary.
- Revoke lost credentials from **Settings → Access Tokens** and remove obsolete public keys.
- Never submit a private SSH or GPG key to MEGA.
