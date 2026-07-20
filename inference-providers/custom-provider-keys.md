# Custom Provider Keys

A custom Provider key lets MEGA authenticate and route a request while the selected Provider bills your external account directly. Keys are supported only for Providers that publish a dedicated custom-key endpoint in the live catalog.

## Add a personal key

Open **Settings → Inference Providers → Settings**, open a Provider's action menu, and choose **Add custom key**. The Provider must show custom-key support.

After saving, MEGA displays only a fingerprint and status. The secret is encrypted immediately and is never shown again. Rotating a key replaces the ciphertext and resets its status to `unverified`.

Future `auto` requests use the saved key when that Provider is selected. To prove that no MEGA-funded route can be used, force BYOK:

```bash
mega inference chat mega/gpt-5.4-mini "Hello" \
  --provider groq \
  --billing byok
```

The equivalent HTTP header is:

```http
X-Mega-Inference-Billing: byok
```

## Billing-mode behavior

| Situation | `auto` | `routed` | `byok` |
| --- | --- | --- | --- |
| Saved eligible key exists | Uses the key. | Ignores the key. | Uses the key. |
| No saved key exists | May use Routed Inference. | Uses Routed Inference. | Fails with `409`. |
| Provider has no custom-key endpoint | May use Routed Inference. | Uses Routed Inference when supported. | Route is ineligible. |

If a configured key is rejected by the Provider, rotate or remove it. `auto` does not replay an already dispatched failed request with MEGA credit. Use `routed` explicitly when you need to bypass a suspect saved key.

## Organization keys

Team and Enterprise organizations can store keys under **Organization Settings → Inference Providers**. Organization keys belong to the organization, not the administrator who entered them.

- An administrator manages shared keys and routing preferences.
- A member with `write` or `admin` access can select the organization with `X-Mega-Bill-To`.
- A request without that header uses the caller's personal keys and preferences.

See [Organization Billing](/docs/inference-providers/organization-billing) for the complete authorization boundary.

## Manage keys through the Hub API

Key-management automation requires a MEGA token with `account:keys`. Personal endpoints are:

```text
GET    /api/me/inference/provider-keys
PUT    /api/me/inference/provider-keys/{provider}
DELETE /api/me/inference/provider-keys/{provider}
```

The `PUT` body is:

```json
{ "api_key": "provider-secret" }
```

Organization endpoints use `/api/organizations/{handle}/inference/provider-keys`. The secret must contain 8 to 16,384 characters and cannot contain a newline.

List responses contain metadata such as Provider slug, fingerprint, status, validation time, and update time. They never contain plaintext or decryptable key material.

## Security model

Provider keys use an owner- and Provider-specific encryption context. The Router receives a decrypted key only for dispatch to the selected Provider's custom-key endpoint. Keys are excluded from application logs, analytics, error responses, and the public model catalog.

MEGA records route, task, token-count, latency, and zero-cost BYOK ledger metadata. The external Provider receives request content and applies its own retention, abuse, and billing policies. Review those policies before saving a key.

## Rotation checklist

- Create a replacement key in the Provider console.
- Save it in MEGA; verify the new fingerprint.
- Make a low-cost request with `--billing byok`.
- Revoke the old key at the Provider.
- Remove the MEGA key entirely if routed billing should be the only future mode.

If a key is exposed, revoke it at the Provider first; deleting the encrypted MEGA copy alone does not invalidate the original credential.
