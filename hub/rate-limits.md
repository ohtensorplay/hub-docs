# Rate Limits


MEGA applies rate limits to public traffic and authenticated automation. Limits protect shared Hub, Git, and compute capacity; authentication and authorization are still enforced independently on every protected operation.

## Public traffic buckets

| Bucket | Current limit | Typical traffic |
| --- | ---: | --- |
| API reads | 100 requests per 60 seconds | Repository metadata, account data, Jobs, and Space discovery |
| Resolvers | 600 requests per 60 seconds | Repository file downloads through `/resolve/` |
| Pages | 20 requests per 60 seconds | Anonymous HTML navigation |
| API writes | 240 requests per 60 seconds | JSON mutations and Space actions |

Authenticated declarative API reads are keyed to the access token or account identity. Anonymous reads use the client IP. Protected writes can enforce both an IP bucket and an actor bucket, so rotating tokens does not bypass the network guardrail and users behind a shared network do not all share the authenticated identity bucket.

## Sensitive-operation buckets

| Operation class | Current limit |
| --- | ---: |
| General sign-in and authorization | 20 requests per 5 minutes |
| Password verification per account | 10 requests per 15 minutes |
| Password verification per IP | 60 requests per 15 minutes |
| OAuth device, token, and revocation | 60 requests per 5 minutes |
| Account deletion | 5 requests per hour |
| Account security changes | 30 requests per hour |
| Artifact uploads | 120 requests per minute |
| Authenticated Bucket S3 operations | 100,000 requests per minute per credential |
| Git writes | 120 requests per minute |
| Git authentication | 120 requests per 5 minutes |

Limits may be tightened temporarily when traffic is abusive. Treat the response headers as authoritative rather than hard-coding retry timing.

## Handle a 429 response

A limited request returns `429 Too Many Requests`, a JSON error, and these headers:

| Header | Meaning |
| --- | --- |
| `Retry-After` | Minimum number of seconds before retrying. |
| `RateLimit` | Exhausted bucket and estimated reset time. |
| `RateLimit-Policy` | Window and request quota for that bucket. |

Retry only after `Retry-After`, add random jitter, and cap exponential backoff. Do not immediately fan out the same request through additional tokens or IP addresses.

```python
import random
import time

response = client.get(url)
if response.status_code == 429:
    delay = int(response.headers.get("Retry-After", "1"))
    time.sleep(delay + random.random())
```

For large model or dataset downloads, use resolver URLs and resume with HTTP range requests. Cache immutable revisions locally instead of repeatedly resolving the same files.

## Related protections

Rate limits work alongside authentication, token scopes, organization roles,
repository permissions, request validation, and abuse prevention. Passing a
rate-limit check does not grant access to a protected operation. Limits may
change to protect the service; the response headers remain the authoritative
source for a request.

The live [OpenAPI Explorer](/spaces/mega/openapi) marks limited operations with `429` responses and `x-mega-rate-limit-buckets` metadata.
