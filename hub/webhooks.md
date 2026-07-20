# Webhooks


MEGA webhooks deliver signed JSON events to a public HTTPS receiver. Routes can cover repositories owned by the current account or one explicit repository.

## Supported events

| Event | Emitted when |
| --- | --- |
| `repo.created` | A repository is created. |
| `repo.updated` | Repository metadata or content changes. |
| `repo.deleted` | A repository is deleted. |
| `repo.ref.created` | A branch or tag is created. |
| `repo.ref.deleted` | A branch or tag is deleted. |
| `discussion.created` | A discussion or pull request is opened. |
| `discussion.updated` | Discussion state or metadata changes. |
| `discussion.reply.created` | A reply is added. |

## Create a route

```bash
mega webhooks create \
  --name release-verifier \
  --url https://ci.example/hooks/mega \
  --event repo.updated \
  --event repo.ref.created
```

Limit delivery to one repository with `--repo`:

```bash
mega webhooks create \
  --name community-index \
  --url https://index.example/hooks/mega \
  --repo mega/catalog \
  --event discussion.created \
  --event discussion.reply.created
```

When `--secret` is omitted, the create response contains a generated signing secret exactly once. Store it immediately.

## Verify a delivery

Each request includes:

| Header | Meaning |
| --- | --- |
| `X-Mega-Delivery` | Stable delivery UUID. Use it for idempotency. |
| `X-Mega-Event` | Event name. |
| `X-Mega-Signature-256` | `sha256=<hex>` HMAC of the exact request body. |

Compute HMAC-SHA256 over the raw body bytes and compare the result in constant time. Do not parse and reserialize JSON before verification.

## Test and observe

```bash
mega webhooks test <webhook-id>
mega webhooks deliveries <webhook-id> --limit 50
mega webhooks info <webhook-id> --format json
```

The test command creates a signed `webhook.test` delivery. Delivery receipts
show delivery state, attempt count, receiver status, timestamps, and the last
retained error.

## Pause, update, and delete

```bash
mega webhooks disable <webhook-id>
mega webhooks update <webhook-id> --url https://ci.example/new-hook
mega webhooks update <webhook-id> --secret "$NEW_SIGNING_SECRET"
mega webhooks enable <webhook-id>
mega webhooks delete <webhook-id>
```

Disabling pauses the route and cancels unstarted deliveries. Deleting removes the route and its retained delivery receipts.

## Python SDK

```python
from megatensors.hub import MegaHubClient

client = MegaHubClient()
created = client.create_webhook(
    name="release-verifier",
    url="https://ci.example/hooks/mega",
    events=["repo.updated", "repo.ref.created"],
)

print(created.webhook.webhook_id)
print(created.signing_secret)  # persist once, then redact
```

Use `list_webhooks`, `get_webhook`, `update_webhook`, `test_webhook`, `list_webhook_deliveries`, and `delete_webhook` for the complete lifecycle.

## Access and delivery contract

- Fine-grained tokens need `webhooks:manage`.
- Receiver URLs must be public HTTPS URLs.
- A repository-scoped route receives only matching repository events.
- Delivery processing is asynchronous; a successful test enqueue does not imply receiver acceptance.
- Retained receipts are the source of truth for retry and terminal state.
- Treat the signing secret like a password and rotate it if it is exposed.
