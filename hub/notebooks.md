# Notebooks

Notebooks can be stored, reviewed, and versioned as ordinary repository files.
MEGA renders `.ipynb` files as a safe static preview: Markdown, code, and plain
text outputs are shown, but notebook JavaScript and rich HTML outputs are never
executed in the repository viewer.

For an interactive managed workspace, create a private Space with `sdk:
jupyter`. It runs JupyterLab with its root directory at `/data`; that persistent
Space volume survives runner restarts and image rebuilds. The initial repository
files are copied into `/data` once, so later work stays private to the runtime
until you explicitly commit or export it.

Use a [Job](/docs/hub/jobs) for reproducible batch notebook execution. Include a
card that explains the environment, input revision, dependencies, and expected
outputs so another reader can reproduce the work.

Never commit tokens, private keys, or downloaded private data into a notebook.
