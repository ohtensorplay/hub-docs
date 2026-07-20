# Serving Models

Jobs are for bounded container execution and do not expose a long-lived public
service endpoint. Use [Spaces](/docs/hub/spaces) for an interactive application
or [Inference Providers](/docs/inference-providers/index) for routed model
inference.

If a Job prepares a model artifact, publish the result to a tagged repository
or a documented Bucket path, then deploy or consume that explicit output in the
next step.
