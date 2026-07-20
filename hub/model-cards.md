# Model Cards

A model card is the root `README.md` of a model repository. It gives readers
the context needed to decide whether a release is appropriate for their use.
MEGA renders the card on the repository page and parses supported YAML metadata
for discovery.

## Include the essentials

- Intended use and users.
- Input, output, and task description.
- Training or adaptation provenance.
- Evaluation method, metrics, and known limitations.
- License, citation, and links to supporting research.

Start with the [Repository Cards](/docs/hub/repository-cards) reference, then
add structured results through [Model Evaluations](/docs/hub/model-evaluations).
Keep claims in the card consistent with the exact revision readers download.

## Safe publishing

Do not include credentials, private endpoints, personal data, or unreviewed
claims. Use a [Gated Repository](/docs/hub/gated-repositories) or a private
repository when access terms need more control than a public card can provide.
