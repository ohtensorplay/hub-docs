# Model Release Checklist

Use this checklist before announcing a model release:

- [ ] The card states intended use, limitations, provenance, license, and citation.
- [ ] Configuration, tokenizer, manifest, and all referenced files are in one revision.
- [ ] The release loads from a clean download.
- [ ] Evaluation results name the suite, task, metric, unit, and source.
- [ ] The repository has appropriate visibility or gated-access terms.
- [ ] A tag identifies the exact public release.
- [ ] No credentials, private keys, or unintended personal data are present.

After release, monitor discussions, correct material card errors in a new
commit, and publish a new tag when a change affects behavior. See
[Model Evaluations](/docs/hub/model-evaluations) and [Signing and Trust](/docs/hub/trust).
