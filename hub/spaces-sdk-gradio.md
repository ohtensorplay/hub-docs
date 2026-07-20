# Gradio Spaces

Set `sdk: gradio` in a Space card when the repository contains a Gradio
application. Keep the entry-point file and dependency declaration in the same
revision, then publish and observe the build.

```yaml
---
title: Gradio demo
sdk: gradio
app_file: app.py
---
```

Use [Spaces](/docs/hub/spaces) for upload, logs, restart, hardware, variables,
and secrets. Do not put a MEGA token in browser-delivered Gradio code; invoke
protected APIs from a protected application component with the narrowest
required scope.
