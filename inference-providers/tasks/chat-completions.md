# Chat Completions

Chat Completions sends an ordered message history to a live Provider and returns the OpenAI-compatible `choices` response. Use it for applications already built around `/v1/chat/completions`, including streaming, tool calling, and structured output where the selected route supports them.

## Find a compatible route

Use [Inference Models](/inference/models) or the authenticated `/v1/models`
endpoint to find a live `chat-completions` route. Check `supports_tools` and
`supports_structured_output` in the [Model Catalog](/docs/inference-providers/model-catalog).
A model name alone does not guarantee either capability across every Provider.

## Call with HTTP

```bash
curl https://inference.tensorplay.cn/v1/chat/completions \
  -H "Authorization: Bearer $MEGA_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{
    "model": "mega/gpt-5.4-mini",
    "messages": [
      {"role": "system", "content": "Be concise."},
      {"role": "user", "content": "What is routed inference?"}
    ],
    "temperature": 0.2,
    "max_tokens": 128
  }'
```

MEGA uses the selected Provider route for the public model ID. Compatible
request fields are handled according to that route's advertised capabilities.

## Stream output

Set `stream` to `true`:

```bash
curl -N https://inference.tensorplay.cn/v1/chat/completions \
  -H "Authorization: Bearer $MEGA_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{
    "model": "mega/gpt-5.4-mini:fastest",
    "messages": [{"role": "user", "content": "Count from one to five."}],
    "stream": true
  }'
```

The Provider's Server-Sent Events are preserved. Automatic failover is possible only before the first stream byte reaches the client. If the stream breaks later, decide at the application layer whether replay is safe.

## Use tools

Only choose a route whose catalog record has `supports_tools: true`. Send the standard OpenAI tool schema and inspect `tool_calls` in the assistant response:

```json
{
  "model": "mega/gpt-5.4-mini",
  "messages": [{"role": "user", "content": "What is the weather in Shanghai?"}],
  "tools": [{
    "type": "function",
    "function": {
      "name": "get_weather",
      "description": "Read current weather for a city",
      "parameters": {
        "type": "object",
        "properties": {"city": {"type": "string"}},
        "required": ["city"],
        "additionalProperties": false
      }
    }
  }]
}
```

MEGA routes and relays tool calls but does not execute them. Validate arguments, authorize the action, execute it in your application, then append the tool result to the next message history.

## Request structured output

For a route with `supports_structured_output: true`, send the Provider-compatible `response_format`. Prefer JSON Schema where the selected model advertises it:

```json
{
  "response_format": {
    "type": "json_schema",
    "json_schema": {
      "name": "answer",
      "strict": true,
      "schema": {
        "type": "object",
        "properties": {"summary": {"type": "string"}},
        "required": ["summary"],
        "additionalProperties": false
      }
    }
  }
}
```

Capability validation is route-specific. If an explicit Provider rejects a supported-looking field, refresh the live catalog and capture the `Inference-Id` for [Troubleshooting](/docs/inference-providers/troubleshooting).
