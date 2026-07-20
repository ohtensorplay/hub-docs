# Use the MEGA Codex Plugin

The MEGA Codex plugin combines MCP access for bounded online operations with
workflow Skills that guide Codex through MEGA tasks. Describe the outcome you
want and include the relevant repository, organization, or resource identifier.

## Start with an explicit task

Good requests identify the action and the target:

- “Find public image-classification models and compare their cards.”
- “Inspect `owner/dataset` and summarize its file layout.”
- “Create a draft model repository under my account; do not upload files.”
- “Show the latest logs for this Job before I decide whether to cancel it.”

For a change, ask Codex to inspect first when the target or effect is unclear.
Confirm the exact repository and path before a write, rename, or deletion.

## Choose the right surface

Use the plugin's connected workflow for identity, discovery, metadata, bounded
text files, small verified changes, Space configuration and invocation, Jobs,
Sandboxes, and supported community actions. Use the MEGA CLI workflow when the
work includes local directories, large or resumable transfers, Git history,
streaming logs, scripts, secrets, or sustained monitoring.

The connected workflow never submits secret values, calls arbitrary URLs, or
moves bulk artifacts. It asks the CLI or protected MEGA settings to handle
those cases.

Codex can help choose the surface, but you remain responsible for reviewing
commands and confirming changes in your own environment.

## Keep work reproducible

For work that changes a repository, keep the repository identifier and revision
in the request. For repeated tasks, save a command or script in your project
and use the CLI workflow so the operation can be reviewed and rerun.

See [MCP Workflows in Codex](/docs/ai-native/mcp-workflows) for common patterns and
[MEGA Agent Skills](/docs/ai-native/skills) for installing individual workflows.
