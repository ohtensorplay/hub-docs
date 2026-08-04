# Model Evaluations

Model repositories can publish structured benchmark results from
HF-compatible `.eval_results/*.yaml` files. The model page reads files merged
into `main` as repository results and also shows results proposed by open Pull
Requests with a **community** label.

## Submit a benchmark score

1. Open the model page and find **Evaluation results**.
2. Select **Submit results** and sign in.
3. Enter the dataset ID, task ID, numeric score, and any reproducibility fields.
4. Select **Open Pull Request**.
5. Review the generated Pull Request from the model repository's **Community** tab.

The browser creates a dedicated contribution branch, writes one YAML file below
`.eval_results/`, and opens a Pull Request against `main`. A contributor does
not need repository write permission for this scoped flow, but the repository
must be readable and its Community feature must be enabled. The contribution is
attributed to the signed-in account.

While the Pull Request is open, its new or changed evaluation files appear on
the model page with a **community** label and a link back to the review. Closing
the Pull Request removes those proposed results. Merging it publishes the YAML
on `main`, removes the community label, and makes the result part of repository
history.

## YAML format

Every `.eval_results/*.yaml` file contains a non-empty list. The required fields
match the Hugging Face
[Eval Results specification](https://huggingface.co/docs/hub/eval-results):

```yaml
- dataset:
    id: cais/hle
    task_id: text-generation
    revision: main
  value: 56
  date: 2026-08-03
  source:
    url: https://example.com/evals/alice-qwen-demo-hle
    name: Release evaluation report
    user: alice
  notes: Revision v1.0, no tools
```

| Field | Required | Meaning |
| --- | --- | --- |
| `dataset.id` | Yes | Benchmark dataset in `owner/name` form. |
| `dataset.task_id` | Yes | Task or sub-leaderboard identity. |
| `value` | Yes | Finite numeric result. |
| `dataset.revision` | No | Dataset branch, tag, or commit used for the run. |
| `date` | No | ISO-8601 evaluation date. |
| `source.url` | No | HTTP or HTTPS leaderboard, report, trace, or paper. |
| `source.name` | No | Human-readable evidence source. Requires `source.url`. |
| `source.user` | No | Evaluator or source account. Requires `source.url`. |
| `notes` | No | Concise protocol and reproducibility notes. |

MEGA never treats a foreign `verifyToken` as a MEGA verification. Verification
badges remain server-managed trust signals.

## Owner review and notifications

Open **Community → Pull requests**, select the result PR, inspect its commit and
file diff, and check the benchmark protocol and evidence. A repository writer
can comment, close or reopen the proposal, and merge it when the source remains
a fast-forward descendant of `main`.

When a contributor opens a PR, MEGA sends an Inbox review request to the
personal repository owner. For organization repositories, organization members
with `admin` or `write` access receive it. MEGA also queues the dedicated
**Pull Request review requested** email when that recipient has **Settings →
Notifications → Discussion activity → Email** enabled. The email names the
contributor, PR number, and repository, and its **Review Pull Request** action
opens the same browser review. The person opening the PR is not sent a
self-notification. Email delivery is asynchronous; the Inbox and PR remain the
authoritative review surfaces.

Reviewers should confirm:

- the score belongs to the exact model revision under review;
- dataset revision, task, split, prompting, tools, and preprocessing are clear;
- the numeric value uses the benchmark's published metric definition;
- source links are accessible and do not expose credentials or private traces;
- the result does not duplicate an unchanged YAML file already on `main`.

## Submit through the API

The model-page contribution endpoint accepts the same values as the form and
requires an authenticated account with `community:write`:

```http
POST /api/repos/{owner}/{name}/model-evaluation-submissions
Content-Type: application/json

{
  "dataset_id": "cais/hle",
  "task_id": "text-generation",
  "value": 56,
  "dataset_revision": "main",
  "date": "2026-08-03",
  "source_url": "https://example.com/evals/alice-qwen-demo-hle",
  "source_name": "Release evaluation report",
  "source_user": "alice",
  "notes": "Revision v1.0, no tools"
}
```

The response contains the Pull Request, result-file path, contribution branch,
and commit revision. The service deliberately controls the branch and path; it
cannot be used as a general repository-write endpoint.

Repository writers can still manage legacy database-backed records directly:

| Operation | Route |
| --- | --- |
| Read model links and all evaluations | `GET /api/repos/{owner}/{name}/model-associations` |
| Create a legacy record | `POST /api/repos/{owner}/{name}/model-evaluations` |
| Update a legacy record | `PATCH /api/repos/{owner}/{name}/model-evaluations/{evaluationId}` |
| Delete a legacy record | `DELETE /api/repos/{owner}/{name}/model-evaluations/{evaluationId}` |

Those management routes require repository write permission. New community
contributions should use `.eval_results` Pull Requests so the score, review,
and merge history stay together.

## Release checklist

- Evaluate a pinned model revision, not an untracked local checkout.
- Record the exact benchmark dataset and task identities.
- Include enough protocol detail for a reviewer to interpret the value.
- Keep claims consistent with the model card and cited source.
- Merge only after reviewing the generated YAML diff.
- Update or remove stale results when a release changes behavior.
