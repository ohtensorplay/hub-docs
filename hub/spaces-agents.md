# Spaces as Agent Tools

A Space can be a useful human-facing demo for an agent workflow, but MEGA does
not automatically treat every Space as an agent tool. Use the
[MCP Server](/docs/hub/mcp) for MEGA's typed, permissioned AI actions.

When a Space calls an external model or tool, keep its credentials in Space
secrets, disclose the action boundary to users, and validate untrusted input.
