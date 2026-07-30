# Pricing and Billing

Inference Provider prices are published per live model route in USD per one million input and output tokens. MEGA Routed Inference charges the selected Provider's standard price without an additional inference markup; custom-key requests are billed directly by the Provider.

## Monthly included credit

Every signed-in account receives monthly Routed Inference credit. It is applied automatically before purchased compute credit and resets on the first day of each UTC month.

| Billing account | Monthly included credit | Extra usage |
| --- | ---: | --- |
| Community user | $0.10 | Purchase compute credit |
| PRO user | $2.00 | Purchase compute credit |
| Team or Enterprise organization | $2.00 per billed seat | Shared by organization members, then purchased organization credit |

Included credit is promotional, inference-only credit. It is not cash, cannot be refunded or transferred, does not pay for Jobs or Spaces, and does not apply to custom Provider keys. Upgrading during a month increases the current month's entitlement; downgrades do not claw back credit already granted for that month.

## Compare current prices

Use [Inference Models](/inference/models) or the live catalog API:

```bash
curl https://mega.tensorplay.cn/api/inference/models
```

The catalog's `pricing.input` and `pricing.output` fields are USD per one million tokens. Prices belong to a model, Provider, and task mapping and may change independently.

For `:cheapest`, Chat Completions and Responses compare output price first. Embeddings compare input price because they do not produce billed output tokens.

## Choose a billing mode

Set the mode with `X-Mega-Inference-Billing`:

| Mode | Behavior |
| --- | --- |
| `auto` | Try an eligible saved Provider key, then use Routed Inference when no usable key is configured. This is the default. |
| `routed` | Ignore saved keys and require MEGA Routed Inference credit. |
| `byok` | Require a saved custom key and never fall back to MEGA billing. |

```bash
curl https://inference.tensorplay.cn/v1/responses \
  -H "Authorization: Bearer $MEGA_TOKEN" \
  -H "Content-Type: application/json" \
  -H "X-Mega-Inference-Billing: routed" \
  --data '{"model":"mega/gpt-5.4-mini","input":"Hello"}'

curl https://inference.tensorplay.cn/v1/responses \
  -H "Authorization: Bearer $MEGA_TOKEN" \
  -H "Content-Type: application/json" \
  -H "X-Mega-Inference-Billing: byok" \
  --data '{"model":"mega/gpt-5.4-mini:groq","input":"Hello"}'
```

The billing mode filters route candidates. A Provider that only supports custom keys cannot serve a forced `routed` request, and a Provider without a custom-key endpoint cannot serve a forced `byok` request.

## Preauthorization and settlement

Before dispatch, Routed Inference reserves a bounded maximum amount from the billing owner's monthly included credit first, then from purchased compute credit. The estimate considers the request size, route context windows, published prices, and explicit output limits such as `max_tokens`, `max_completion_tokens`, or `max_output_tokens`.

After completion, MEGA settles the Provider's reported token usage and cost, returns the unused reservation, and records one idempotent ledger settlement. A request that fails before output begins releases its reservation.

If output has already started, the request can still settle even when the client disconnects or the stream later fails. This matches upstream usage: do not automatically replay interrupted streams unless your application can tolerate duplicate work and cost.

## Credit and spending limits

Routed Inference uses the same compute-credit account described in [Hub Billing](/docs/hub/billing). A request can return `402` when:

- the account is restricted or carries unpaid compute debt;
- available compute credit is smaller than the reservation;
- the billing owner's monthly inference hard limit would be exceeded.

Configure the monthly limit under **Settings → Inference Providers → Settings**. The limit applies to Routed Inference reservations for that billing owner. BYOK requests have zero MEGA inference cost and do not consume the routed spending limit, although the external Provider may bill them.

## Usage reporting

The Inference Providers overview shows the last 30 days of:

- request counts and routed-versus-custom-key totals;
- input and output tokens;
- accrued MEGA inference cost;
- model and task breakdowns;
- Provider request and cost summaries;
- current monthly accrued cost and hard limit.

Personal usage appears in account settings. Organization usage appears in the organization's Inference Providers settings and is separate from the member's personal ledger.

## Delayed settlement safeguards

Some routed Providers report final cost asynchronously. MEGA retries billing reconciliation on a one-minute cadence and performs a final cost lookup before closing the 30-minute billing window. Reservations abandoned before output are refundable after 15 minutes; a completed request that still cannot be billed after approximately 30 minutes is marked `failed_to_bill`, refunded, and raised to operations.

The user remains uncharged after that close. MEGA continues querying late Provider cost for a bounded operations-only window so confirmed loss is measured from actual Provider usage rather than the released reservation estimate. A Provider whose recent `failed_to_bill` ratio crosses the configured minimum sample and failure thresholds is removed from new Routed reservations until billing health recovers; automatic routing may continue with another healthy Provider.

Keep the `Inference-Id` response header in your application logs so support can trace a reservation and settlement without prompt content.
