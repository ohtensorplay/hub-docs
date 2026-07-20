# Paper Pages

Paper Pages connect an arXiv publication to verified MEGA authors and public model, dataset, or Space releases. They provide a stable research-identity record without treating an unreviewed authorship claim as public fact.

## Open a paper page

A verified paper is available at:

```text
https://mega.tensorplay.cn/papers/2503.00948
```

The page shows the normalized arXiv identifier, reviewed title, link to the arXiv abstract, and verified MEGA authors. Only verified claims contribute public authors.

## Claim authorship

Open [Settings → Papers](/settings/papers), enter an arXiv identifier or arXiv abstract/PDF URL, and submit the claim. MEGA accepts current numeric identifiers such as `2503.00948` and legacy identifiers such as `hep-th/9901001`.

Version suffixes such as `v2` are normalized to the underlying paper record. A claim moves through these states:

| State | Visibility and available actions |
| --- | --- |
| `pending` | Private to the claimant and reviewers; the claimant can withdraw it. |
| `verified` | Eligible to appear on the public Paper Page and claimant profile. |
| `denied` | Retained privately with the review result; it cannot be shown publicly. |

Verification is a review step, not an automatic consequence of knowing an arXiv ID. Do not submit claims for collaborators or papers you did not author.

## Show a verified paper on a profile

After verification, use **Show on profile** in [Paper settings](/settings/papers). Turning it off removes the paper from the profile but does not revoke verified authorship or remove the public Paper Page.

Only verified claims can change profile visibility. A pending claim can be withdrawn; a verified or denied record remains part of the review ledger.

## Link repositories to a paper

Add an `arxiv:<paper-id>` tag to a public repository:

```bash
mega repos settings research/vision-evals \
  --tag multimodal \
  --tag evaluation \
  --tag arxiv:2503.00948
```

Use the normalized base identifier without a version suffix. Keep a human-readable citation and explanation in the [repository card](/docs/hub/repository-cards) as well; the tag supplies the machine-readable relationship.

A paper link does not imply that MEGA verified the technical claims, benchmark results, or licensing of the linked artifact. Repository publishers remain responsible for accurate provenance and documentation.

## Public API

Search verified paper records:

```bash
curl 'https://mega.tensorplay.cn/api/papers?q=2503.00948&limit=20'
```

Read one paper:

```bash
curl 'https://mega.tensorplay.cn/api/papers/2503.00948'
```

The collection returns an opaque `next_cursor` for pagination. Authorship claim creation and profile visibility are browser-session account operations under `/api/me/papers`; use the settings page rather than placing review actions in unattended scripts.

## Current boundary

- Paper identities are arXiv-only.
- A paper becomes discoverable after at least one authorship claim is verified.
- Titles and author links reflect reviewed MEGA records, not a live mirror of all arXiv metadata.
- Repository links require public repository metadata and an exact `arxiv:<paper-id>` tag.
- Verification establishes the MEGA account-to-paper relationship; it is not peer review or an endorsement of the paper.
