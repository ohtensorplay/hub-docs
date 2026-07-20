# Sandboxes

MEGA Sandboxes provide supported interactive execution sessions for workflows
that need to run commands, inspect state, or collect bounded metrics. They are
separate from long-lived [Spaces](/docs/hub/spaces) and batch
[Jobs](/docs/hub/jobs).

## Public API

The public API supports provider discovery, create, inspect, stop, metrics, and
command execution operations under `/api/sandboxes`. Use the live
[OpenAPI Explorer](/spaces/mega/openapi) for the exact schemas, supported
providers, limits, and error responses.

Treat a Sandbox as an execution boundary: pass only the data and secrets a
session needs, do not rely on it for durable storage, and remove a session when
the work is complete.
