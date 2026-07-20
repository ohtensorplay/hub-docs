# Space Custom Domains

Public Spaces support self-service custom domains through the Space API:

```text
POST /api/spaces/:owner/:name/domains
{ "hostname": "app.example.com" }
```

The response contains the CNAME target and DNS verification records. Publish a
CNAME from your hostname to `cnameTarget`, then publish every returned
verification record at your DNS provider. Refresh its status after DNS has
propagated:

```text
POST /api/spaces/:owner/:name/domains/app.example.com/verify
```

When the status becomes `active`, the hostname serves the public Space over
HTTPS. Use `DELETE /api/spaces/:owner/:name/domains/app.example.com` to remove
it. Domains cannot be attached to private Spaces.
