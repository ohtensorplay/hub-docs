# Model FAQ

## Should I publish a model as public, gated, or private?

Use public for unrestricted distribution, gated for a public listing with
individual access terms, and private when the repository itself must not be
discoverable. See [Gated Repositories](/docs/hub/gated-repositories).

## Why should I pin a revision?

Branches can change. Tags and commit IDs identify the exact files that were
evaluated and approved for an application.

## Where do evaluation results belong?

Put the interpretation and limitations in the model card, then publish
structured scores through [Model Evaluations](/docs/hub/model-evaluations).

## Can I use a Hugging Face client?

Core Hub workflows are supported by configuring `HF_ENDPOINT`. Read
[Hugging Face Compatibility](/docs/hub/hugging-face-compatibility) before
depending on a feature outside the documented boundary.
