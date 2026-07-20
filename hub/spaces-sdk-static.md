# Static Spaces

Set `sdk: static` for a Space that serves a static HTML, CSS, and JavaScript
application from its repository. Keep the published entry files in the same
revision as the card.

```yaml
---
title: Static demo
sdk: static
---
```

Static browser code is public to every visitor who can access the Space. Do not
embed MEGA tokens, private URLs, or secrets in client-side JavaScript. Use a
separate authenticated service for protected operations.
