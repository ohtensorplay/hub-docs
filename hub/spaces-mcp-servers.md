# Spaces and MCP

MEGA's supported hosted AI integration is the [MCP Server](/docs/hub/mcp).
Spaces are not automatically published as MCP servers, and a Space card does
not create an MCP tool catalog.

To connect an AI client to MEGA resources, use the documented MCP endpoint and
OAuth flow. To build an application in a Space, expose only the user-facing
HTTP behavior you intend to support and keep authorization decisions explicit.
