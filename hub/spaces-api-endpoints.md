# Spaces API Endpoints

Use the Space API to inspect, control, and observe a Space from another
application. The [OpenAPI Explorer](/spaces/mega/openapi#tag/Spaces) is the
source of truth for current schemas and response fields.

Common public routes include:

| Operation | Route |
| --- | --- |
| Read runtime state | `GET /api/spaces/{owner}/{name}/runtime` |
| Restart or pause | `POST /api/spaces/{owner}/{name}/restart` or `/pause` |
| Read build logs | `GET /api/spaces/{owner}/{name}/logs/build` |
| Read runtime logs | `GET /api/spaces/{owner}/{name}/logs/run` |
| List hardware | `GET /api/spaces/hardware` |

Authenticate protected operations with the required token scope and repository
permission. Handle `402`, `403`, `422`, `429`, and `5xx` responses explicitly.
