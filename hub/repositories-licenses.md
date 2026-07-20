# Repository Licenses

Declare a license before publishing a model, dataset, or Space whose contents
others may use. A license tells readers the terms for code, weights, data, and
documentation; it does not replace rights you must obtain from upstream
authors or data subjects.

## Set the effective license

Set a repository fallback with the CLI and state the terms in the root card:

```bash
mega repos settings OWNER/NAME --license apache-2.0
```

Card metadata takes precedence when it declares a valid `license` value. Keep
the setting and card consistent so search and readers show the same terms.

## Publish responsibly

- List upstream sources and their applicable licenses.
- Explain exceptions, redistributions, and known use restrictions in the card.
- Do not use a license label to claim rights you do not have.
- Use a private repository while distribution rights are being reviewed.
- Require acknowledgement through [Gated Repositories](/docs/hub/gated-repositories)
  when that is appropriate for a public release.
