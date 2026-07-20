# Embed a Space

Public Spaces can opt in to iframe embedding. Add `embed: true` to the Space
card and deploy the revision:

```yaml
---
sdk: gradio
embed: true
---
```

After the Space is running, read `embedUrl` from `GET /api/spaces/:owner/:name`
and use it as the iframe source:

```html
<iframe src="https://your-space.example" title="My Space" allowfullscreen></iframe>
```

Only public Spaces can be embedded. Private Spaces keep their access boundary
and must be opened from the Hub. Test the embedded flow in the target site and
do not put credentials or other browser-held secrets in a public application.
