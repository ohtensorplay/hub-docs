# Embeddings

The Embeddings endpoint converts one string or a batch of strings into OpenAI-compatible numeric vectors. MEGA routes only to mappings validated for the `embeddings` task; Embeddings requests do not stream.

## Find a compatible model

```bash
mega inference models --task embeddings --sort input-price
```

Compare context length, input price, throughput, Provider, and custom-key support. Output-token price is normally zero for this task.

## Call with the CLI

Embed one input:

```bash
mega inference embeddings BAAI/bge-m3 "MEGA routes inference"
```

Embed several inputs in one request:

```bash
mega inference embeddings BAAI/bge-m3 \
  "first document" \
  "second document" \
  --format json
```

Omit all input arguments to read one string from standard input. Use `--dimensions` only when the selected Provider model supports a reduced output size.

## Call with HTTP

```bash
curl https://inference.tensorplay.cn/v1/embeddings \
  -H "Authorization: Bearer $MEGA_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{
    "model": "BAAI/bge-m3:cheapest",
    "input": ["first document", "second document"],
    "encoding_format": "float"
  }'
```

The response uses the OpenAI-compatible `data` array with one indexed embedding per input and a usage object when supplied by the Provider.

## Use Python clients

With the OpenAI SDK:

```python
import os
from openai import OpenAI

client = OpenAI(
    base_url="https://inference.tensorplay.cn/v1",
    api_key=os.environ["MEGA_TOKEN"],
)

response = client.embeddings.create(
    model="BAAI/bge-m3",
    input=["first document", "second document"],
)
vectors = [item.embedding for item in response.data]
```

With MEGA `InferenceClient`:

```python
import os
from megatensors import InferenceClient

client = InferenceClient(provider="auto", api_key=os.environ["MEGA_TOKEN"])
vectors = client.feature_extraction(
    ["first document", "second document"],
    model="BAAI/bge-m3",
)
```

The MEGA client orders returned vectors by their response index.

## Preserve vector compatibility

For production indexes, pin the full model ID, Provider selection, dimensions, and normalization assumptions. Two Providers serving the same Hub model should expose the validated mapping, but Provider-specific revisions or defaults can still affect numeric output.

Store these fields with the index build metadata:

- Hub model ID and selected Provider policy or slug;
- vector dimensions and encoding format;
- application-side normalization or pooling;
- index creation time and source-data revision.

Do not silently mix vectors from a changed model or dimension in an existing similarity index. Re-embed and rebuild when the vector contract changes.
