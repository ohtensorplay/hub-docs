# AI Native Troubleshooting

Start by identifying whether the problem is installation, authorization, the
selected MEGA resource, or the workflow surface.

## The plugin or Skills do not appear

Restart Codex after installing, updating, or removing a plugin or Skill. Check
the installation command for the intended marketplace, repository, Skill name,
and Codex target. If the problem persists, remove the incomplete installation
through Codex plugin management and install it again.

## Codex cannot access MEGA

Run a read-only task to trigger authorization, then complete sign-in for the
intended account. If access was previously granted but is no longer valid,
reconnect MEGA from Codex and review the authorization request again.

For organization resources, also confirm that your MEGA account is a member
and has the required role. See [Authorize MEGA in Codex](/docs/ai-native/authorization).

## A write action is refused or affects the wrong target

Ask Codex to inspect the resource first and verify the owner, repository type,
path, and revision. Then make one precise change. A write can be refused when
the granted permission, organization policy, or membership role does not allow
it.

## A local or long-running task does not work through MCP

Switch to the MEGA CLI workflow for directories, binary files, bulk transfer,
Git history, streaming logs, secrets, scripts, and sustained waits. The
[MEGA Skills Catalog](/docs/ai-native/skills-catalog) explains which Skill guides
each kind of task.

## Get help safely

When reporting an issue, include the non-sensitive task description, the
resource identifier, approximate time, and any error text. Never include
passwords, access tokens, private keys, or payment details.
