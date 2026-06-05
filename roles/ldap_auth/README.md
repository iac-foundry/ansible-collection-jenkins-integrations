# blueprints.jenkins_integrations.ldap_auth

Wire Jenkins to an existing LDAP.

## Deployment model

`ldap_auth` (integration).

## Inputs

All configuration is supplied via caller variables — see `meta/argument_specs.yml`. Required inputs
have no default and fail fast when missing.

## Non-goals

- Does not retrieve secrets — the caller resolves them and passes them in
  (see [secret consumption standard](../../../../docs/standards/BLUEPRINTS_SECRET_CONSUMPTION.md)).
- Does not deploy or configure any other product. Cross-product wiring lives in
  `blueprints.jenkins_integrations` where applicable.
