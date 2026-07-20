# Repository Settings

Repository settings control the name, description, tags, license, visibility,
and community features for one model, dataset, or Space repository. Open the
repository page and select **Settings**, or use the MEGA CLI for repeatable
changes.

## Visibility

Public repositories can be discovered and read without signing in. Private
repositories require an authorized account or token. Changing visibility affects
future access immediately, but does not remove copies that readers already
downloaded.

```bash
mega repos settings OWNER/NAME --private
mega repos settings OWNER/NAME --public
```

Use [Gated Repositories](/docs/hub/gated-repositories) when a repository may
remain discoverable but downloads require an application or terms acceptance.

## Metadata and ownership

Keep the description, tags, and license aligned with the root `README.md` card.
You can rename a repository or move it to an organization where you have the
required role. Update automation, Git remotes, and external links after a move.

```bash
mega repos settings OWNER/NAME --description "Release description" --tag demo
mega repos move OWNER/OLD-NAME OWNER/NEW-NAME
```

## Community controls

Repository managers can enable or disable discussions and pull requests where
the feature is available. Disabling a feature prevents new activity; it does
not rewrite repository history. See [Discussions and Pull Requests](/docs/hub/discussions)
for the collaboration workflow.

## Safe changes

- Verify the owner and repository name before a move or delete.
- Use a private repository for work that must not be discoverable.
- Pin automation to a commit or tag; do not rely on a mutable default branch.
- Review organization policy before changing visibility in a shared namespace.
