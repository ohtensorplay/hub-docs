# Search and Discovery

MEGA offers global search, type-specific catalogs, social follows, and a personalized home feed. Discovery uses public repository metadata; private resources appear only when the current session or token can read them.

## Choose a discovery surface

| Surface | Best for |
| --- | --- |
| Header search | Quickly opening a model, dataset, or Space by name or description. |
| [Models](/models) | Filtering model releases by task and approximate parameter count. |
| [Datasets](/datasets) | Filtering datasets by text, image, audio, video, or multimodal signals. |
| [Spaces](/spaces) | Browsing deployable applications and their repository context. |
| Home feed | Following new releases, repository activity, papers, and people over time. |
| CLI or API | Reproducible search for scripts and other clients. |

Catalogs can sort by trending activity, downloads, likes, or last update. The `trending` order reuses the recommendation system's seven-day public engagement signal, but never personal interests, follows, or account history, so the same catalog URL is deterministic across users. Search and filters operate on repository ID, description, tags, license, and type-specific metadata signals.

## Make a repository discoverable

Set a specific description, license, and small set of useful tags:

```bash
mega repos settings research/vision-evals \
  --description "Multimodal evaluation prompts and reviewed labels" \
  --license cc-by-4.0 \
  --tag multimodal \
  --tag evaluation
```

The root `README.md` supplies detailed context after a visitor opens the result, but directory search does not treat a long card as a substitute for accurate structured metadata. See [Repository Cards](/docs/hub/repository-cards).

## Search from the CLI

```bash
mega repos list --search vision
mega repos list --type model --owner mega --search qwen
mega repos list --type dataset --search evaluation --limit 50
```

Use `--format json` when another program consumes the result. Typed commands are convenient when no cross-type filter is needed:

```bash
mega models list --limit 20
mega datasets list --limit 20
mega spaces list --limit 20
```

## Search through the API

The repository collection supports cursor pagination and filters:

```bash
curl 'https://mega.tensorplay.cn/api/repos?type=model&q=qwen&sort=downloads&limit=25'
```

Useful query parameters include:

| Parameter | Meaning |
| --- | --- |
| `type` | `model`, `dataset`, or `space`. |
| `q` | Search repository ID, description, and tags. |
| `owner` | Personal or organization handle. |
| `sort` | `trending`, `downloads`, `likes`, or `updated`. |
| `pipeline_tag` | Filter model task metadata and live inference mappings. |
| `inference_provider` | Require an active inference mapping; use `all` or one Provider slug. |
| `limit` | Page size from 1 through 500. |
| `cursor` | Opaque continuation value from `next_cursor`. |

Do not construct or modify cursors. Repeat the same filters with the returned `next_cursor` until it is `null`.

## Likes, follows, and notifications

Likes are a lightweight public signal on repositories. Following a repository or publisher subscribes the signed-in account to future activity. The [Notifications](/notifications) page collects repository changes, discussions, and social events relevant to those subscriptions.

Notification settings explain the source of each stream and provide links back to the watched repository or identity. Unfollowing stops future subscription events; it does not delete notifications already delivered.

## Personalized recommendations

The home feed can combine followed activity with repository and social recommendations. [Content Preferences](/settings/content-preferences) controls audience-labeled material, interest learning, sponsored personalization, and feed history behavior.

MEGA honors browser Global Privacy Control and Do Not Track signals for navigation analytics. Private repository identities and paths are not published into anonymous discovery analytics.

## Visibility rules

- Anonymous search contains only public repositories and public profiles.
- Authenticated search may include private repositories readable through personal, organization, or resource-group access.
- `no_access` organization membership does not make private organization content discoverable.
- A hidden or suppressed recommendation can remain reachable directly if normal authorization allows it.
- Search visibility never grants download or write permission; every target request is authorized again.
