# Responses API

The Responses API exposes the OpenAI-compatible `/v1/responses` contract for text generation, instructions, tools, structured output, and event-based streaming. Use it for new agent-style applications or when you need Responses-native input and output objects.

## Find a compatible route

```bash
mega inference models --task responses
```

Responses is a separate validated task. A model available for Chat Completions is not automatically available on this endpoint.

## Call with the CLI

```bash
mega inference responses mega/gpt-5.4-mini \
  "Explain Provider failover briefly" \
  --instructions "Answer for a software engineer." \
  --max-output-tokens 256
```

Add `--stream`, `--provider`, `--billing`, `--bill-to`, or `--session-id` as needed. Omit the input argument to read it from standard input.

## Call with HTTP

```bash
curl https://inference.tensorplay.cn/v1/responses \
  -H "Authorization: Bearer $MEGA_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{
    "model": "mega/gpt-5.4-mini",
    "instructions": "Answer in one paragraph.",
    "input": "How does MEGA choose a Provider?",
    "max_output_tokens": 256
  }'
```

The response keeps the upstream Responses shape. SDKs normally expose convenience accessors such as `output_text`; raw clients should inspect the `output` array.

## Use the OpenAI Python SDK

```python
import os
from openai import OpenAI

client = OpenAI(
    base_url="https://inference.tensorplay.cn/v1",
    api_key=os.environ["MEGA_TOKEN"],
)

response = client.responses.create(
    model="mega/gpt-5.4-mini:preferred",
    instructions="Be concise.",
    input="Explain MEGA inference routing.",
    max_output_tokens=256,
)
print(response.output_text)
```

See [Clients and SDKs](/docs/inference-providers/guides/clients) for organization and billing headers.

## Stream Responses events

Set `stream: true` or use the CLI's `--stream`. MEGA preserves Provider Server-Sent Events, including output-text delta events. The CLI prints `response.output_text.delta` and refusal deltas as text in human mode; JSON and agent modes emit complete event objects.

Once the first event is exposed, the Router will not switch Providers. Treat an interrupted event stream as a partial response.

## Conversation affinity

Pass `X-Mega-Session-Id` or `--session-id` when the same conversation should prefer a stable upstream account. The Router also recognizes `previous_response_id` as an affinity signal and forwards it to the Provider.

MEGA does not persist response content or create a cross-Provider response store. Whether `previous_response_id` can restore Provider-side state depends on the selected Provider and route. An explicit application-managed input history is more portable across failover.

## Tools and structured output

The Router forwards Responses-compatible tools and output-format fields. Use only catalog routes that advertise the corresponding capability, and still validate tool arguments and structured output in your application.

Provider selection can change the exact optional surface. Pin a Provider suffix when your application depends on a Provider-specific Responses extension; otherwise keep requests within the common validated contract.
