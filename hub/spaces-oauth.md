# Authentication in Spaces

Enable built-in identity sign-in by setting `mega_oauth: true` in the Space
card. The Space receives a public OAuth client configuration automatically; no
client secret belongs in the repository, browser, or Space variables.

```yaml
---
sdk: docker
mega_oauth: true
---
```

For a FastAPI application, install the optional dependency and attach the
helper once:

```python
from fastapi import FastAPI, Request
from megatensors._hub import attach_mega_oauth, parse_mega_oauth

app = FastAPI()
attach_mega_oauth(app)

@app.get("/me")
def me(request: Request):
    identity = parse_mega_oauth(request)
    return {"signed_in": identity is not None}
```

Use `/oauth/mega/login` for the sign-in action and `/oauth/mega/logout` to
clear the application session. The built-in flow requests identity scopes only;
use a separately registered OAuth application when the Space needs access to
other account resources. For iframe use, the helper automatically falls back
to a top-level sign-in when the browser blocks third-party cookies.
