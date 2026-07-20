# Discussions and Pull Requests

Discussions and pull requests provide one collaboration surface for model, dataset, and Space repositories. They share numbering, Markdown messages, reactions, attachments, notifications, and moderation rules; a pull request additionally records source and target revisions.

## Open the community tab

Every repository has a **Community** tab. From there you can:

- list open and closed discussions and pull requests;
- open a discussion for questions, release notes, or support;
- propose a branch-backed pull request;
- reply with Markdown and math;
- upload or remove discussion attachments;
- report content that violates community policy.

The same surface is available through `mega discussions` and the Hub API.

## List and inspect threads

```bash
mega discussions list alice/qwen-demo
mega discussions list alice/qwen-demo --kind pull_request --status open
mega discussions info alice/qwen-demo 5 --format json
```

For datasets and Spaces, pass the repository type explicitly:

```bash
mega discussions list research/evals --type dataset
mega discussions list alice/demo-space --type space
```

Statuses are `open`, `closed`, or `merged`. A merged pull request is terminal; a closed, unmerged thread can be reopened.

## Create and reply to a discussion

```bash
mega discussions create alice/qwen-demo \
  --title "CUDA 13 verification results" \
  --body-file ./results.md

mega discussions comment alice/qwen-demo 5 \
  --body "Confirmed on the v1.0 tag."
```

Use `--body-file -` to read Markdown from standard input. This is safer than embedding a long or shell-sensitive message directly in a command.

Authors can edit their own messages. Repository writers and administrators can moderate messages within repositories they control:

```bash
mega discussions edit alice/qwen-demo 5 <message-id> --body-file ./corrected.md
mega discussions close alice/qwen-demo 5 --comment "Resolved in v1.0" --yes
mega discussions reopen alice/qwen-demo 5 --yes
mega discussions rename alice/qwen-demo 5 "Updated investigation title"
```

## Propose a pull request

MEGA pull requests use a branch in the source repository; they do not require a fork. Create a branch, publish changes to it, and open the proposal:

```bash
mega repos branch create alice/qwen-demo docs-card --revision main

mega upload alice/qwen-demo ./README.md README.md \
  --revision docs-card \
  --commit-message "Clarify model limitations"

mega discussions create alice/qwen-demo \
  --pull-request \
  --source-branch docs-card \
  --target-branch main \
  --title "Clarify model limitations" \
  --body-file ./proposal.md
```

The proposal records the source and target revisions at creation. The CLI identifies it as `refs/pr/<number>`; continue publishing review changes to the named source branch.

## Review and merge

Inspect the commits and file changes carried by a proposal:

```bash
mega discussions diff alice/qwen-demo 8
mega discussions diff alice/qwen-demo 8 --format json
```

Merge when the source remains a fast-forward descendant of the target:

```bash
mega discussions merge alice/qwen-demo 8 \
  --comment "Reviewed and ready to publish" \
  --yes
```

If the target advanced independently, update the source branch and review the new diff before trying again. MEGA never partially advances the target branch after a rejected merge.

## Reactions, deletion, and moderation

The CLI supports the MEGA fire reaction:

```bash
mega discussions react alice/qwen-demo 5 <message-id>
mega discussions react alice/qwen-demo 5 <message-id> --remove
```

Deleting a discussion or comment is permanent and follows authorship and repository-moderation permissions. Merged pull requests cannot be deleted as though their repository history never existed.

Use the report action in the browser for abusive or unsafe content. Reports enter the moderation workflow; they are not a replacement for removing exposed credentials from the repository and rotating them immediately.

## Permissions and automation

- Reading follows normal repository visibility.
- Mutations with a fine-grained token require `community:write`.
- Repository write permission is required to merge a pull request or moderate another person's content.
- Organization repository permissions are evaluated from the caller's current membership on every request.
- [Webhooks](/docs/hub/webhooks) can deliver discussion and reply events to external automation.
