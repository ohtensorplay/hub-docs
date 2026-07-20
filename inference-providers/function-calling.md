# Function Calling

Function Calling lets a model request an action from your application. MEGA
returns the requested tool call; your application validates it, decides whether
it is allowed, performs the action, and sends the result back in the next model
request.

Use Function Calling only with a route whose
`supports_tools` capability is enabled in the live
[Model Catalog](/docs/inference-providers/model-catalog).

## Define a narrow tool

Describe the smallest action that the model needs. Require the fields you need,
disallow unexpected fields where possible, and avoid tools that accept a raw
shell command or unrestricted URL.

```json
{
  "type": "function",
  "function": {
    "name": "get_weather",
    "description": "Read the current weather for a city",
    "parameters": {
      "type": "object",
      "properties": {
        "city": {"type": "string"}
      },
      "required": ["city"],
      "additionalProperties": false
    }
  }
}
```

Include this object in the standard OpenAI-compatible `tools` array for
[Chat Completions](/docs/inference-providers/tasks/chat-completions) or use the
equivalent tool field for [Responses](/docs/inference-providers/tasks/responses).

## Handle a tool call safely

1. Read the tool name and arguments from the model response.
2. Validate the name, argument types, and business rules in your application.
3. Check that the signed-in user is authorized for the requested action.
4. Execute the action with scoped credentials.
5. Return a concise tool result in the next message or Responses input.

Treat model output as untrusted input. A valid schema does not prove that an
action is appropriate, safe, or authorized.

## Design for retries

Use idempotency keys or an application-side confirmation step for actions that
send messages, make purchases, modify data, or trigger Jobs. A user may retry
after a network failure, and an application must avoid performing the same
side effect twice.

For a complete Chat request, see [Chat Completions](/docs/inference-providers/tasks/chat-completions).
For agent-style input and events, see [Responses API](/docs/inference-providers/tasks/responses).
