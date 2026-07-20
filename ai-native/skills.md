# MEGA Agent Skills

MEGA Agent Skills are portable workflow instructions for Codex and other
supported coding agents. Install one Skill when you want focused guidance, or
install the `mega` router Skill to let the agent select the relevant MEGA
workflow.

The router targets MEGA MCP v1.1's 29 bounded online tools and sends local,
bulk, streaming, long-running, and secret-bearing work to the installed `mega`
CLI. Like the Hugging Face CLI Skill, `mega-cli` checks the locally installed
command help before it proposes unfamiliar syntax, so commands follow the
version available in your environment.

## Install the router Skill for Codex

```bash
npx skills add ohtensorplay/mega-skills --skill mega -g -a codex -y
```

Restart Codex after installation. To see all available Skills before choosing
one, run:

```bash
npx skills add ohtensorplay/mega-skills --list
```

## Install a focused Skill

Replace `mega-repositories` with a Skill name from the
[MEGA Skills Catalog](/docs/ai-native/skills-catalog):

```bash
npx skills add ohtensorplay/mega-skills --skill mega-repositories -g -a codex -y
```

To install every MEGA Skill for Codex:

```bash
npx skills add ohtensorplay/mega-skills --skill '*' -g -a codex -y
```

## Plugin or standalone Skills?

The [MEGA Codex plugin](/docs/ai-native/install) is the simplest choice when you
also need the connected MEGA integration. Standalone Skills are useful when
you want the guidance separately, or when you use more than one supported
agent. They can coexist with the plugin.

Skills do not grant account access by themselves. Connected MEGA actions still
require the appropriate authorization.
