# Jupyter Notebook Spaces

A Jupyter Notebook Space is a private managed JupyterLab workspace. Set the
repository card metadata to `sdk: jupyter` and enable persistent Space storage.
MEGA serves JupyterLab through the private Space proxy and starts it with
`/data` as its root directory.

```yaml
---
title: Research workspace
sdk: jupyter
app_file: notebook.ipynb
---
```

The first start copies the repository source into `/data` and writes an
initialization marker. Subsequent restarts and rebuilds retain `/data`; update
the repository only when you want to publish a reproducible snapshot of your
work. Access is managed by the Space's private authorization boundary; do not
copy access tokens or other credentials into a notebook.

Jupyter Spaces must be private. They do not support Dev Mode or public sharing.
Delete the persistent volume only after exporting the work that must survive.
