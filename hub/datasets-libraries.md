# Dataset Libraries

MEGA dataset repositories are format-neutral. A dataset card should identify
the library or reader expected to load the files, the supported formats, and a
minimal example that pins a revision.

Libraries that use `huggingface_hub` can target MEGA through `HF_ENDPOINT`
within the documented compatibility boundary. For other readers, use repository
resolver URLs or a complete snapshot. See
[Hugging Face Compatibility](/docs/hub/hugging-face-compatibility).
