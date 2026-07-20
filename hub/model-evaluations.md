# Model Evaluations

Model evaluations attach structured test results to a model repository so
readers can inspect the task, metric, score, and source alongside the release.
They complement a [Repository Card](/docs/hub/repository-cards); they do not
replace an explanation of the data, method, limitations, or intended use.

## Add useful results

For each result, record a recognizable evaluation suite, task, metric, score,
and unit. Add a source URL or concise details when they help a reader reproduce
or interpret the result. Link the result to the release revision it describes
in the card or source material.

Do not compare scores unless the task, dataset version, split, preprocessing,
prompting, hardware assumptions, and metric definition are compatible. State
known limitations, confidence intervals, and failure cases in the model card.

## Read results on a model page

Published model pages show their available test results in the evaluation
panel. A result can include a source link and a verification indicator when it
has been reviewed through the applicable MEGA workflow. An absent indicator is
not evidence that a result is incorrect; it means readers should assess the
provided evidence themselves.

## Manage results through the API

Repository writers can manage evaluations with the public Hub API:

| Operation | Route |
| --- | --- |
| Read model links and evaluations | `GET /api/repos/{owner}/{name}/model-associations` |
| Add an evaluation | `POST /api/repos/{owner}/{name}/model-evaluations` |
| Update an evaluation | `PATCH /api/repos/{owner}/{name}/model-evaluations/{evaluationId}` |
| Delete an evaluation | `DELETE /api/repos/{owner}/{name}/model-evaluations/{evaluationId}` |

An evaluation request supplies `suite`, `metric`, and `score`; it may also
include `task`, `unit`, `source_url`, and `details`. Repository write permission
is required for changes. Use the live [OpenAPI Explorer](/spaces/mega/openapi)
for the complete schema and response contract.

## Release checklist

- Evaluate the exact revision you publish, not an untracked local checkout.
- Name the metric and unit unambiguously; a raw score alone is rarely useful.
- Link to enough method detail for a reader to understand the result.
- Keep evaluation claims consistent with the repository card and cited paper.
- Update or remove stale results when a release changes behavior.
