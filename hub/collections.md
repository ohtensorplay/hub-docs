# Collections

Collections are ordered, shareable lists of MEGA models, datasets, Spaces, papers, other collections, and Storage Buckets. Use collections to publish a reading list, group the resources for a project, or curate a release set without copying repository files.

## Browse and create collections

Open [Collections](/collections) to browse public collections. You can search by title or description and sort by trending activity, last modification, or upvotes.

After signing in, select **New Collection** from the avatar menu or the Collections directory. The browser opens `/collections/OWNER/new`, where you choose:

- A concise title and optional description.
- Public or private visibility.
- A theme color used by the directory and collection page.

A collection URL has the stable form `/collections/OWNER/SLUG`. MEGA generates the initial slug from the title; later title changes do not silently rename the URL. The `new` slug is reserved for the creation page. Use the returned canonical URL rather than constructing one from the title.

Personal collections require an account with write access. Creating or editing an organization collection requires the organization **Write** or **Admin** role. A **Contributor** can create repositories they own, but cannot create or edit an organization-wide collection; **Read** and **No access** roles can only view collections allowed by visibility policy.

## Manage collections from the CLI

List the collections visible to the active identity:

```bash
mega collections ls
mega collections ls --owner mega --sort trending
mega collections ls --search "evaluation" --limit 10
```

Create a collection in your own namespace or an organization where you have write access:

```bash
mega collections create "Release candidates" \
  --description "Models and evaluations for the next release"

mega collections create "Private review" \
  --namespace research \
  --private \
  --theme green
```

The command returns the canonical `OWNER/SLUG`. Use that value for later commands:

```bash
mega collections info OWNER/release-candidates
mega collections update OWNER/release-candidates \
  --description "Approved release inputs"
```

Collection writes made with a fine-grained token require `repo:write`.

## Add and order items

Pass the resource ID followed by its item type:

```bash
mega collections add-item OWNER/release-candidates mega/qwen-release model \
  --note "Primary candidate"
mega collections add-item OWNER/release-candidates research/evals dataset
mega collections add-item OWNER/release-candidates 2503.00948 paper
mega collections add-item OWNER/release-candidates research/release-artifacts bucket
```

Supported item types are:

| Type | Item ID |
| --- | --- |
| `model` | Repository ID such as `mega/qwen-release`. |
| `dataset` | Repository ID such as `research/evals`. |
| `space` | Repository ID such as `mega/demo`. |
| `paper` | Paper identifier such as `2503.00948`. |
| `collection` | Another collection's `OWNER/SLUG`. |
| `bucket` | Storage Bucket ID such as `research/release-artifacts`. |

Collection item types match Hugging Face exactly. To associate a Blog article or another external page, put its URL in the item's plain-text note.

`mega collections info` returns each entry's stable `item_object_id`. Use that identifier to change a note or ordered position, or to remove the entry:

```bash
mega collections update-item OWNER/release-candidates ITEM_OBJECT_ID \
  --position 0 \
  --note "Start here"
mega collections delete-item OWNER/release-candidates ITEM_OBJECT_ID
```

Adding or removing an item changes only the collection. It does not modify, duplicate, or delete the referenced repository or paper.

## Visibility and ownership

Public collections are discoverable without signing in. Private collections require access to their owner namespace. Referenced resources keep their own permissions; adding an item is not a way to grant repository access.

Organization collections follow organization membership and write policy. Use a personal collection for individual curation and an organization collection when the list is part of a shared release process.

The collection page exposes sharing, filtering, sorting, and a public change
history. History entries show the public actor identity and action category.

## API automation

The collection API is rooted at `/api/collections` and supports list, create, read, update, delete, upvote, and ordered-item operations. Use [Hub API](/docs/hub/api) for authentication conventions and the [OpenAPI Explorer](/spaces/mega/openapi) for current request and response schemas.

For scripts, prefer the typed `MegaHubClient` or `mega collections` commands so pagination, canonical slugs, and errors remain consistent with the Hub.
