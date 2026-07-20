# Model Libraries

MEGA repositories are library-neutral: a repository can store files consumed by
Megatensors, `transformers`, Diffusers, or another compatible client. The
repository card should identify the expected library, task, configuration, and
loading example.

## Choose a clear release contract

- Keep the files required by the chosen library in one revision.
- Name the library and version assumptions in the model card.
- Provide a minimal load example that pins a tag or commit.
- Do not infer compatibility from a filename alone.

For existing Hugging Face client workflows, follow
[Hugging Face Compatibility](/docs/hub/hugging-face-compatibility). For
MEGA-native artifacts, use the [Artifact Format](/docs/megatensors/package_reference/format)
and [Python SDK](/docs/hub/sdk).
