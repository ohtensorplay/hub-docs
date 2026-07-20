# Docker Spaces

Set `sdk: docker`, declare the public application port, and commit a Dockerfile
with the Space source.

```yaml
---
title: Docker demo
sdk: docker
app_port: 7860
---
```

The Dockerfile must start the application on the configured port. Keep build
inputs deterministic, avoid baking credentials into an image, and inspect build
logs after each revision. See [Spaces Dependencies](/docs/hub/spaces-dependencies)
and [Space Configuration Reference](/docs/hub/spaces-config-reference).
