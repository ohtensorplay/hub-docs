# Install the MEGA Codex Plugin

Install the MEGA plugin from its Codex marketplace, then restart Codex so it
can load the plugin and its bundled Skills.

```bash
codex plugin marketplace add ohtensorplay/mega-codex-plugin
codex plugin add mega@tensorplay
```

## Connect your MEGA account

The first MEGA action that needs your account opens the authorization flow.
Sign in to the intended MEGA account, review the requested permissions, and
complete authorization. See [Authorize MEGA in Codex](/docs/ai-native/authorization)
for safe permission choices.

## Confirm the installation

Ask Codex to perform a read-only MEGA task, such as finding a public model or
opening a repository card. If Codex asks you to authorize MEGA, complete that
step and retry the request.

For a task that affects a repository, use a specific request such as “inspect
the README for `owner/repository`” before asking Codex to make a change. This
lets you verify the destination and expected edit first.

## Update or remove the plugin

Use your Codex plugin management interface to update or remove the MEGA
plugin. Restart Codex after changing installed plugins. Removing the plugin
does not delete MEGA repositories or revoke an existing authorization; revoke
access from your MEGA security settings if you no longer want Codex to use it.
