# Bucket Integrations

Buckets integrate with MEGA CLI, the Python client, and compatible
`huggingface_hub` workflows. They are suitable for mutable artifacts that do
not need repository commits.

```python
from megatensors._hub import MegaFileSystem

fs = MegaFileSystem(token="YOUR_MEGA_TOKEN")
with fs.open("buckets/OWNER/artifacts/metrics.json", "wb") as file:
    file.write(b'{}')
```

An integration should use the narrowest token scope, validate paths before a
copy or delete, and record the repository revision that produced an artifact.

## S3 tools and SDKs

The [Bucket S3 Gateway](/docs/hub/storage-buckets-s3) lets applications that
already use the AWS S3 API connect without rewriting their storage layer. Use a
dedicated Bucket credential, configure path-style addressing, and keep the
endpoint, access key, and secret in your application's normal secret store.

S3 clients can also provide a filesystem-style mount when that is the best fit
for an existing application. For MEGA's mount extension and platform-managed
Space volumes, see [Bucket Access Patterns](/docs/hub/storage-buckets-access).
