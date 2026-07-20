# Structured Output

Structured Output asks a model to return data that conforms to a declared
shape, such as a JSON object for an extraction or classification workflow. It
is useful when your application needs machine-readable fields instead of prose.

Use it only with a route whose `supports_structured_output` capability is
enabled in the live [Model Catalog](/docs/inference-providers/model-catalog).

## Declare a JSON Schema

For Chat Completions, send a Provider-compatible `response_format` with a
small, explicit schema:

```json
{
  "response_format": {
    "type": "json_schema",
    "json_schema": {
      "name": "release_summary",
      "strict": true,
      "schema": {
        "type": "object",
        "properties": {
          "summary": {"type": "string"},
          "risk": {"type": "string", "enum": ["low", "medium", "high"]}
        },
        "required": ["summary", "risk"],
        "additionalProperties": false
      }
    }
  }
}
```

Keep schemas small and deterministic. Use required fields, explicit enums, and
`additionalProperties: false` when the receiving application cannot handle
unknown fields.

## Validate every result

Structured Output improves the response contract, but it does not replace
application validation. Parse the result, validate it against your schema, and
apply your normal authorization and business checks before using it to change
data or call an external service.

If validation fails, present a recoverable error or retry with a simpler prompt
and schema. Do not silently coerce a value that affects a security, billing, or
destructive decision.

## Use with Chat and Responses

For a complete request and streaming behavior, see
[Chat Completions](/docs/inference-providers/tasks/chat-completions). The
[Responses API](/docs/inference-providers/tasks/responses) also accepts
compatible output-format fields when the selected route advertises the
capability.

Route capability can differ by Provider. Refresh the catalog after a route
change and keep the model selection explicit when a workflow depends on a
particular optional feature.
