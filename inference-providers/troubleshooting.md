# Troubleshooting

Start with the response status and `Inference-Id`, then separate authentication, catalog, billing, routing, and Provider failures. Do not include prompt or credential contents in logs or support reports.

## Minimal diagnostic sequence

1. Confirm the token and scope against the Router model list.
2. Confirm the model has a live mapping for the endpoint task.
3. Retry with an explicit Provider to isolate route selection.
4. Make the billing mode explicit.
5. Check personal or organization Provider settings and credit.

```bash
curl -i https://inference.tensorplay.cn/v1/models \
  -H "Authorization: Bearer $MEGA_TOKEN"

mega inference models --search Qwen3-32B --format json

mega inference chat mega/gpt-5.4-mini "health check" \
  --provider mega \
  --billing routed
```

Use a non-sensitive test prompt for diagnostics.

## `401` authentication errors

Check that:

- the header uses `Bearer`, not a raw token;
- the credential is a MEGA PAT rather than a Provider key;
- the PAT includes `inference:run`;
- it is not expired or revoked;
- the CLI is reading the expected login or `MEGA_TOKEN`.

Token introspection can be cached for up to 60 seconds. Wait for that bound after changing or revoking a credential before concluding the change failed.

## `402` billing errors

For `--billing routed`, inspect the selected billing owner's compute credit, debt or restricted status, and monthly inference limit. Organization requests must use the intended `--bill-to` value.

To distinguish credit from custom-key behavior:

```bash
mega inference chat mega/gpt-5.4-mini "health check" --billing routed
mega inference chat mega/gpt-5.4-mini "health check" --provider groq --billing byok
```

Do not switch to BYOK merely to bypass an organization spending policy unless the external Provider account is authorized for that workload.

## `403` organization permission errors

Verify that the caller has `write` or `admin` access to a Team or Enterprise organization. An administrator is required to manage organization preferences and keys.

## `404` model or task errors

Refresh the live catalog:

```bash
mega inference models --task chat-completions --search Qwen
mega inference models --task responses --search Qwen
mega inference models --task embeddings --search bge
```

Check the exact case-sensitive `owner/model` ID and the endpoint task. A repository page, inference-related tag, or mapping on another task does not make the requested route live.

If an explicit Provider suffix fails while the unsuffixed model works, that Provider currently has no eligible mapping, validation, capacity, or billing path for the task.

A Provider disabled in the selected billing owner's settings remains ineligible even when the model ID names it explicitly. Re-enable it or choose a different route.

## `409` custom-key errors

`provider_key_missing` means the selected candidate cannot reserve the requested BYOK route. Confirm that:

- the Provider advertises custom-key support;
- a key is saved for the actual personal or organization billing owner;
- the key status is `unverified` or `valid`, not invalid or disabled;
- the request uses the Provider that owns the saved key.

If a Provider rejects a configured key with `401` or `403`, rotate it at the Provider and in MEGA. Use `--billing routed` to bypass the saved key for a deliberate test.

## `413` request-size errors

Reduce message history, tool schemas, embedded documents, or batch size. The default Router body limit is 8 MiB, but the deployed limit may differ. Token context limits are separate from byte-size limits and may produce an upstream validation error instead.

## `429`, `5xx`, and `502`

Before output starts, the Router may try another eligible candidate for `408`, `425`, `429`, network errors, or `5xx`. A final `502` means those pre-output attempts were exhausted or a required control-plane service was unavailable.

Use exponential backoff with jitter. Honor `Retry-After` when supplied. Compare an explicit Provider with `fastest` to determine whether the problem is route-specific, but avoid rapid cross-Provider retry loops.

## Interrupted streams

Once the first stream byte is exposed, MEGA will not fail over. A broken stream may already have consumed tokens and settled cost.

- Treat received content as partial.
- Give a retry a new application operation ID.
- Prevent duplicate tool execution and other side effects.
- Do not assume `previous_response_id` is portable across Providers.

## What to record

Record the following for an actionable incident:

```text
Inference-Id
UTC timestamp
HTTP status and machine-readable error code
Hub model ID and task
selection strategy or explicit Provider
billing mode and billing-owner type
whether streaming output began
X-Mega-Provider when present
```

Exclude Authorization, Provider keys, cookies, prompt or response content, tool arguments, and full bodies. See [Security and Privacy](/docs/inference-providers/security) for the complete data boundary.
