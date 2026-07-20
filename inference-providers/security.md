# Security and Privacy

Use a MEGA access token to authenticate inference requests. MEGA checks the
token, your account or organization permissions, and the selected billing owner
before a request can run. The selected inference Provider receives the content
needed to perform the requested task.

## Protect access tokens

Create a separate fine-grained Personal Access Token for each application and
grant only the `inference:run` permission it needs:

```http
Authorization: Bearer mega_...
```

Keep the token in a secret manager or environment variable. Never put it in a
browser bundle, URL, model ID, prompt, or session identifier. If a token may
have been exposed, revoke it and create a replacement before continuing.

Provider API keys are not used as MEGA access tokens. Manage them through
[Custom Provider Keys](/docs/inference-providers/custom-provider-keys) when
you deliberately choose the BYOK workflow.

## Understand content handling

MEGA does not retain prompts, responses, embedding inputs, or tool arguments
as Hub content. It records the limited account, usage, and reliability
information needed to provide the service, handle billing, and investigate
abuse. The selected Provider has its own data-processing and retention policy;
review that policy before sending sensitive content.

For BYOK, the external Provider also applies the account and billing terms for
the key you supplied.

## Stream safely

After streaming output begins, MEGA does not automatically replay the request
with another Provider. If your client retries an interrupted stream, it creates
a new inference request and can produce a second result or charge.

Design application-level retries so that tool calls and other side effects are
safe to repeat. Record the `Inference-Id` response header for troubleshooting.

## Keep session identifiers private

Use `X-Mega-Session-Id` with a random application conversation ID when you
need request affinity. Do not place identifying or sensitive information in a
session ID. Compatible conversation fields included in an OpenAI-style request
body are part of that request, so treat them as Provider-visible content.

## Browser applications

Browser applications can call the inference API, but a Personal Access Token
must not be exposed to untrusted users. Send requests through your own
authenticated application service and enforce the user-level limits you need.

## Report an incident safely

Include the `Inference-Id`, UTC time, public model ID, task, billing mode, and
client-side status when reporting a problem. Do not send a prompt, response,
Authorization header, saved key, or complete request body unless an approved
secure support channel explicitly asks for it. See
[Troubleshooting](/docs/inference-providers/troubleshooting) for status-specific
checks.
