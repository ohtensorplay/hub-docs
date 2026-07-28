# Spaces and MCP

MEGA has two separate MCP surfaces:

- The free [official MCP Server](/docs/hub/mcp) is hosted by MEGA at `/mcp`.
- The [MCP Marketplace](/docs/hub/mcp-marketplace) lets a publisher sell tool
  calls from a public MCP repository backed by a managed private Space.

A Space is never the marketplace page or public file repository. Marketplace
publishing accepts only a personal private Docker or Gradio Space on CPU
Basic, then binds it internally to a separate public MCP repository. The
private Space ID and page are not linked from the marketplace. Docker MCP
servers expose Streamable HTTP at `/mcp`; Gradio uses
`/gradio_api/mcp/`.

The public marketplace endpoint is owned by Hub. Callers authenticate to Hub,
and their MEGA credentials are not forwarded to the Space. Publishers cannot
substitute an external URL or a Worker binding.
