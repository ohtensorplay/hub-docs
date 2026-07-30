# Organization Billing

Organization billing lets an eligible workspace own inference routing preferences, shared custom keys, usage, compute credit, and a monthly hard limit. It is selected explicitly per request and never inferred from repository ownership.

## Eligibility and roles

Organization inference requires a Team or Enterprise plan.

| Action | Required organization access |
| --- | --- |
| Send a request billed to the organization | `write` or `admin` |
| View organization inference usage and preferences | `admin` |
| Change Provider order, disabled Providers, or spending limit | `admin` |
| Add, rotate, or remove an organization Provider key | `admin` plus token scope `account:keys` for API automation |

Role and plan eligibility apply to every new organization-billed request. After
you remove a member or change a plan, allow the settings change to take effect
before retrying a request.

## Select the billing owner

Use the organization handle, not its display name:

```http
X-Mega-Bill-To: research-lab
```

Python `InferenceClient` accepts `bill_to="research-lab"`. Without an explicit billing owner, MEGA uses the caller's personal account even when the model belongs to an organization.

## What changes for the request

Selecting an organization changes the owner of:

- Provider order and disabled-Provider policy;
- custom Provider keys used by `auto` or `byok`;
- Routed Inference compute-credit reservation and settlement;
- monthly inference hard-limit enforcement;
- usage, token, Provider, and model summaries.

The caller's PAT still authenticates the request and must include `inference:run`. An organization does not issue a different Router bearer token.

## Configure the organization

Open **Organization Settings → Inference Providers**. The overview shows organization-only usage; the settings tab controls the monthly limit, Provider order, availability, and shared keys.

For preferred routing:

```json
{"model": "mega/gpt-5.4-mini:preferred", "input": "Hello"}
```

Send that body to `/v1/responses` with
`X-Mega-Bill-To: research-lab`. The Router uses the organization's first
compatible healthy Provider. Disabled Providers remain unavailable even when
named explicitly.

## Combine billing modes

Organization ownership and billing mode are independent:

Require the organization's saved Provider key:

```http
X-Mega-Inference-Billing: byok
X-Mega-Bill-To: research-lab
```

Use `mega/gpt-5.4-mini:groq` as the request's `model` value.

Ignore organization keys and use its MEGA compute credit:

```http
X-Mega-Inference-Billing: routed
X-Mega-Bill-To: research-lab
```

BYOK requests record zero MEGA inference cost and are excluded from the organization's routed hard limit. The external Provider bills the organization account associated with the saved key.

## Automation guidance

- Give CI a PAT owned by a maintained service identity with only `inference:run`.
- Grant that identity the minimum organization role needed to run requests.
- Keep `X-Mega-Bill-To` explicit in every organization-billed workload.
- Set a monthly hard limit before enabling unattended routed traffic.
- Restrict `account:keys` to the separate workflow that rotates shared Provider credentials.
- Log `Inference-Id`, organization handle, model, and task, but never prompts or keys.

See [Pricing and Billing](/docs/inference-providers/pricing-and-billing) for reservation behavior and [Security and Privacy](/docs/inference-providers/security) for data boundaries.
