# Access Tokens

Access tokens authenticate CLI, SDK, and API automation. Create them in
**Settings → Access Tokens**, give each token a descriptive name, and select
only the scopes it needs.

| Need | Scope |
| --- | --- |
| Read private repositories | `repo:read` |
| Publish repository content | `repo:write` |
| Delete a repository | `repo:delete` |
| Run Jobs | `jobs:run` |
| Manage webhooks | `webhooks:manage` |

Store a token in a secret manager or CI secret, not in a card, notebook,
container image, or command history. Revoke a token that is no longer needed
or might have been exposed. See [Authentication](/docs/hub/authentication).
