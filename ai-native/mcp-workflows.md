# MCP Workflows in Codex

The MEGA plugin lets Codex use MEGA MCP for focused, online Hub workflows.
MCP is a good fit for discovering resources, reading metadata, inspecting a
small known file, or making a carefully scoped change after confirmation.

## Discover before changing

Use a read-first workflow when you are unsure which resource is correct:

1. Ask Codex to search or inspect the candidate resource.
2. Verify the owner, repository type, visibility, and revision in the result.
3. State the exact change you want.
4. Review the result and open the resource in MEGA if needed.

This avoids changing a similarly named repository or the wrong revision.

## Available MCP tasks

MEGA MCP v1.1 has 32 focused tools. Typical workflows include:

- Identify the connected account or inspect a public profile.
- Find and inspect models, datasets, Spaces, papers, and repositories. Dataset inspection can retrieve supported structure and first-row previews.
- Read a bounded `mega://` text file; create a repository or make one explicit text-file commit after you confirm the destination.
- Inspect, duplicate, pause, restart, configure, open, or invoke a compatible Space. Space configuration covers the live hardware catalogue, sleep time, Dev Mode, storage, read-only volumes, and non-secret variables.
- Inspect, run, cancel, or schedule a bounded container Job; or inspect and operate a Sandbox session or warm pool after confirming cost and scope.
- Work with collections, blog content and comments, or repository discussions and pull requests when the requested scope permits it.
- Search and fetch current MEGA documentation.
- Search the MCP Marketplace, inspect a selected publisher tool schema, and
  invoke it through the official gateway with an explicit Mega Coin maximum.

MCP deliberately excludes arbitrary HTTP requests, bulk artifact transfer,
secret-value submission, account-key management, webhook management, and
repository deletion.

## When to switch to the CLI

Use the MEGA CLI path for local files and directories, binary artifacts, bulk
uploads or downloads, Git branches and history, scripts, long-running waits,
streaming output, and secret-bearing operations. These tasks need local context
or an interactive workflow that is better handled outside a bounded MCP call.

Marketplace MCPs may include a local companion. Install its versioned files
without executing publisher code:

```bash
mega mcp info mega/xpuoj
mega mcp install mega/xpuoj
```

Review its README before enabling it. Remote calls still use
`https://mega.tensorplay.cn/mcp`.

Read [Use the MEGA Codex Plugin](/docs/ai-native/using-the-plugin) and
[MEGA Agent Skills](/docs/ai-native/skills) for the workflow-selection guidance
Codex receives.
