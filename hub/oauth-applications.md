# OAuth Applications

Organization administrators can register OAuth applications for integrations
that need a user-approved MEGA authorization flow. Manage applications from
**Organization Settings → OAuth Applications**.

## Register securely

- Register exact redirect URIs you control.
- Use authorization-code flow with PKCE for public clients.
- Keep a confidential-client secret on the application's server, never in a
  browser bundle or repository.
- Request only the scopes the application needs.
- Rotate a client secret if it is exposed.

Users can review and revoke connected application grants separately from the
application registration. See [Account Security](/docs/hub/security) and the
live [OpenAPI Explorer](/spaces/mega/openapi) for current API details.
