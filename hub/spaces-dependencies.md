# Spaces Dependencies

Commit every dependency declaration needed to build a Space. A Docker Space
uses its `Dockerfile`; a Python-oriented Space should keep its dependency files
next to the application entry point.

## Reproducible builds

- Pin important package versions.
- Keep dependency manifests and application source in the same commit.
- Do not download secrets or private credentials during build.
- Test the application locally when its framework supports it.

When a build fails, open the build logs, fix the repository source, publish a
new revision, and restart the Space. See [Spaces](/docs/hub/spaces).
