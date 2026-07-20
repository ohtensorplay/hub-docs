# Spaces Configuration Reference

Configure a Space in the YAML frontmatter at the top of its root `README.md`.
The card is still rendered as documentation; the recognized Space fields select
how the application is started.

```yaml
---
title: My application
sdk: docker
app_port: 7860
---
```

## Common fields

| Field | Use |
| --- | --- |
| `title` | Human-readable application name. |
| `sdk` | `docker`, `gradio`, `streamlit`, `static`, or `jupyter`. |
| `app_file` | Application entry point when it is not the default. |
| `app_port` | Port exposed by a Docker Space. |
| `embed` | Set to `true` to allow a public Space to be embedded in an iframe. |
| `mega_oauth` | Set to `true` to enable the built-in identity sign-in integration. |

Keep configuration in the same revision as the application code. Invalid or
unsupported configuration appears in the user-visible build or runtime error.
See [Spaces](/docs/hub/spaces) for deployment and recovery.
