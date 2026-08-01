# Organization Cards

An organization card is the public, long-form introduction on an organization
profile. It gives a publisher room to explain its work, link to important
resources, and guide visitors toward the right models, datasets, Spaces, and
MCP servers.

## How organization cards work

MEGA reads the card from the root `README.md` on the `main` branch of a public
Space named `<organization>/README`. The name is matched without regard to
letter case, but `README` is the conventional spelling shown throughout the
Hub.

- The Space must be owned by the organization and have repository type
  `space`.
- The Space must be public. A private `README` Space is never projected onto
  the public organization profile.
- The file must be a root `README.md`; a README in a subdirectory is not used.
- The public card Space is treated as profile infrastructure and is omitted
  from the organization's published-work count.
- Card edits remain versioned repository commits and use the Space's normal
  write permissions.

The card header links to the `README` Space's **Community** tab, so questions
and feedback stay attached to the versioned card repository.

## Create a card

Open [New Space](/new-space), select the organization as owner, enter `README`
as the Space name, keep the Space public, and choose the Static SDK. Publish a
root `README.md`; the organization profile detects it automatically after the
commit succeeds.

You can update an existing card with the MEGA CLI:

```bash
mega spaces upload research/README ./README.md README.md \
  --commit-message "Refresh organization card"
```

The **Edit card** action is shown only to members who can manage the `README`
Space. It opens the versioned repository tree rather than a separate profile
text field.

## Write a useful card

Start with a short description, then offer a small number of deliberate paths
for visitors. A compact card can be enough:

```markdown
# Research systems that ship

We publish open models, evaluation datasets, and runnable demonstrations.

## Explore

- [Models](/models?author=research)
- [Datasets](/datasets?author=research)
- [Spaces](/spaces?author=research)

## Contact

Open a thread from the Community action above for technical questions.
```

Organization cards use the same safe Markdown renderer as repository cards,
including headings, tables, fenced code, images, and relative links. See
[Repository Cards](/docs/hub/repository-cards) for the supported Markdown and
HTML boundary.

## Scope and safety

An organization card describes the publisher; it does not grant repository
access or replace a model or dataset card. Use release-specific cards for
artifact behavior, limitations, licenses, and citations. Use organization
settings for members, roles, billing, and security policy.

Everything in the card is public. Do not include access tokens, private
endpoints, personal contact details, or material that should be restricted to
organization members.
