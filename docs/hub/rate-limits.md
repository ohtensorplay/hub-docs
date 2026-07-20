# Rate limits


MEGA applies rate limits to public traffic and authenticated automation. Limits protect shared Hub, Git, and compute capacity; authentication and authorization are still enforced independently on every protected operation.

## Public traffic buckets

| Bucket | Current limit | Typical traffic |
| --- | ---: | --- |
| API reads | 100 requests per 60 seconds | Repository metadata, account data, Jobs, and Space discovery |
| Resolvers | 600 requests per 60 seconds | Repository file downloads through `/resolve/` |
| Pages | 20 requests per 60 seconds | Anonymous HTML navigation |
| API writes | 240 requests per 60 seconds | JSON mutations and Space actions |

Limits may vary by authentication state, operation, and current service
conditions. Treat response headers as authoritative rather than hard-coding
retry timing.

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

Authentication, token scopes, organization roles, repository permissions, and
request validation remain in effect even when a rate bucket has capacity. The
live [OpenAPI Explorer](/spaces/mega/openapi) marks operations that can return
`429` responses.
