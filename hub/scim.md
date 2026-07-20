# SCIM Provisioning

Enterprise organizations can use SCIM to provision and deprovision members from
an identity provider. Configure SCIM only after SSO has a verified recovery
path and the organization understands its role mapping.

Create or rotate the SCIM token in **Organization Settings → Security** and
store it only in the identity provider's secret store. Disable or rotate it
immediately if it is exposed.

SCIM changes membership; repository, resource-group, and token policies still
apply. Review the organization audit log after an initial sync and after any
unexpected membership change.
