# Streamlit Spaces

Set `sdk: streamlit` in the Space card and commit the Streamlit entry point and
dependencies with the application source.

```yaml
---
title: Streamlit demo
sdk: streamlit
app_file: app.py
---
```

Publish a new revision after a source change, then review build and runtime
logs. Keep secrets in Space settings rather than a repository file. See
[Spaces Dependencies](/docs/hub/spaces-dependencies) and
[Space Settings](/docs/hub/spaces-settings).
