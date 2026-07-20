# Bucket S3 Gateway

Storage Buckets provide an S3-compatible, path-style gateway for applications
that already use S3 clients and SDKs. The gateway works only with Storage
Buckets; models, datasets, and Spaces are not exposed as S3 buckets.

## Create a Bucket credential

Create a credential for the specific Bucket from its settings or through the
Bucket API. Give it a descriptive name, select `read` or `read-write`, and set
an expiry when the integration is temporary:

```json
POST /api/buckets/OWNER/NAME/s3-credentials
{
  "name": "nightly-export",
  "permission": "read-write",
  "expiresAt": "2026-12-31T00:00:00Z"
}
```

The creation response includes an access key ID and secret access key. Save the
secret immediately in a secret manager: it is shown only when the credential is
created. List credentials with `GET /api/buckets/OWNER/NAME/s3-credentials`,
and revoke a no-longer-needed credential with:

```text
DELETE /api/buckets/OWNER/NAME/s3-credentials/CREDENTIAL_ID
```

## Configure a client

The endpoint is scoped to the Bucket owner or organization namespace:

```text
https://mega.tensorplay.cn/s3/OWNER
```

Use the bare Bucket name in the S3 client and set region `us-east-1` with
path-style addressing. For example, after exporting the credentials returned
at creation time:

```bash
aws configure set aws_access_key_id "$MEGA_S3_ACCESS_KEY" --profile mega
aws configure set aws_secret_access_key "$MEGA_S3_SECRET_KEY" --profile mega
aws configure set region us-east-1 --profile mega
aws configure set s3.addressing_style path --profile mega

aws --profile mega --endpoint-url https://mega.tensorplay.cn/s3/OWNER \
  s3 cp ./metrics.json s3://NAME/reports/metrics.json
```

With `boto3`, set the same endpoint and addressing style:

```python
import boto3
from botocore.config import Config

s3 = boto3.client(
    "s3",
    endpoint_url="https://mega.tensorplay.cn/s3/OWNER",
    region_name="us-east-1",
    aws_access_key_id="MEGA_S3_ACCESS_KEY",
    aws_secret_access_key="MEGA_S3_SECRET_KEY",
    config=Config(s3={"addressing_style": "path"}),
)
s3.put_object(Bucket="NAME", Key="reports/metrics.json", Body=b'{"loss": 0.12}')
```

## Supported operations

The gateway supports listing objects in one Bucket, `GET` and `HEAD` object
reads (including byte ranges), `PUT` uploads, `DELETE`, and AWS Signature
Version 4 presigned requests. Read credentials cannot write or delete.

Create, move, and delete Buckets through the MEGA web application, CLI, or Hub
API rather than S3 bucket-management calls. Multipart upload, object-copy, and
other AWS-specific administrative operations are not part of the gateway. For
large directory transfer, use [Bucket sync](/docs/hub/storage-buckets) instead.

Keep S3 secrets out of source repositories, mounted directories, and public
Spaces. Rotate or revoke a credential immediately if it may have been exposed.
