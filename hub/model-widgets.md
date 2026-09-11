# Model Widgets

A model page shows an inline chat widget when the live Inference Provider
catalog contains a healthy `chat-completions` route for the same `owner/model`
ID. Model-card metadata alone does not make a widget available.

## Chat on the model page

Sign in, choose **Auto** or a specific live Provider, enter a message, and select
**Send**. Auto uses MEGA's managed route selection. **View code snippets** opens
the token-based API examples; **Compare providers** opens the live catalog.

The browser sends the signed-in session to the Hub. The Hub performs inference
server-side through the normal routed-inference control plane, so Provider
credentials are never returned to the page. Widget requests use the signed-in
account's MEGA Inference balance and can return `402` when its credit or spending
limit cannot cover the reservation. Review
[Pricing and Billing](/docs/inference-providers/pricing-and-billing) before
sustained use.

Anonymous model chat is disabled. A model with only Responses or Embeddings
routes shows its available tasks but does not present a fake chat composer.

## Conversations on this device

The model widget and Docs Assistant use the same reusable conversation runtime:

- multiple named conversations;
- new, select, delete, and clear-history actions;
- retry and in-flight cancellation behavior;
- account-scoped browser storage;
- a bounded recent-message history sent with the next request.

Transcripts stay in local browser storage on the current device. They are not
synced to the repository, the model publisher, another device, or MEGA's server
conversation history. Signing into a different account uses a different local
scope. Clear the widget history to remove saved transcripts from that browser.

Do not submit secrets, private datasets, or credentials in a prompt. Local
history is a convenience boundary, not a secret vault.

## Publish a richer demo

The inline widget intentionally covers a simple managed chat task. For custom
controls, multimodal inputs, generated artifacts, or an application-specific
workflow, create a [Space](/docs/hub/spaces) that pins the model revision and
documents its input, cost, and safety limits.
