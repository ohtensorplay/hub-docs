# Organization Single Sign-On

Team and Enterprise organizations can configure single sign-on (SSO) in
**Organization Settings → Security**. SSO centralizes sign-in requirements for
members while normal MEGA organization roles continue to control repository and
compute permissions.

## Configure safely

1. Keep at least two organization administrators with a working recovery path.
2. Add the identity-provider settings in the organization security page.
3. Test the connection before requiring SSO for members.
4. Communicate the transition and remove exemptions only after successful sign-in.

Use the public organization SSO API only from a secure administrative
application. Consult the [OpenAPI Explorer](/spaces/mega/openapi) for the
current configuration and test operations. Do not place identity-provider
secrets in a repository or client-side application.
