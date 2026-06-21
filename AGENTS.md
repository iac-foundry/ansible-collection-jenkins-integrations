# Agent Orientation — ansible-collection-jenkins-integrations

## Agent Working Protocol (read before anything else)

**Conflict surfacing:** If a user instruction contradicts anything in this file or in
`docs/AGENTS.md`, stop and surface the conflict before proceeding — quote the rule,
state the contradiction, and ask how to resolve. Then update the doc if the rule was wrong.

**Living document:** If any instruction, decision, or clarification during a session
would make future interactions clearer, prompt the user:
> "This decision isn't in AGENTS.md yet. Should I add it?"

**Maintenance:** Keep this doc current. Update rules when decisions change. Don't append
orphaned notes — integrate changes into the relevant section.

---

**Collection:** `blueprints.jenkins_integrations`
**Namespace:** `blueprints`
**Scope:** Optional wiring roles that connect an already-deployed Jenkins controller to
external peers (Vault, LDAP, SSO providers, OAuth). Never deploys Jenkins itself.

---

## What lives here

| Role | Wires Jenkins to… |
|---|---|
| `vault_auth` | HashiCorp Vault (AppRole / JWT auth backend) |
| `authentik_sso` | Authentik (OIDC / SSO login) |
| `ldap_auth` | LDAP directory |
| `github_oauth` | GitHub OAuth app |
| `gitlab_oauth` | GitLab OAuth app |

**Key rule:** Each role configures Jenkins only — it does NOT deploy or touch the peer
system (Vault, Authentik, etc.).  The base product must be deployed before these roles run.

---

## Where the standards live

All standards are in `docs/` of the iac-foundry monorepo. Start with `docs/AGENTS.md`.

| Topic | Doc |
|---|---|
| **8 design rules (read first)** | `docs/design/BLUEPRINTS_DESIGN_PRINCIPLES.md` |
| Integration wiring rules | `docs/standards/BLUEPRINTS_INTEGRATION_STANDARDS.md` |
| Variable naming | `docs/standards/BLUEPRINTS_VARIABLE_STANDARDS.md` |
| Secret handling | `docs/standards/BLUEPRINTS_SECRET_CONSUMPTION.md` |
| Why integrations are separate | `docs/decisions/LADR-002-integrations-separated-from-core.md` |

---

## Critical constraints

1. **Integration roles do NOT deploy the base product** — Jenkins must already be running.
2. **No secret retrieval inside tasks** — the caller resolves credentials and passes them in.
   Variable naming convention: `jenkins_integration_<target>_<thing>` (e.g.
   `jenkins_integration_vault_endpoint`, `jenkins_integration_authentik_client_secret`).
3. **Integration collections ARE exempt from the cross-collection import check** (they are
   allowed to know about another product), but they still cannot retrieve secrets internally.
4. **`meta/dependencies: []`** — always empty; no auto-pulling of `blueprints.jenkins`.

---

## PR conformance checklist

- [ ] Role configures one side of one connection only (Jenkins side)
- [ ] Does not deploy, upgrade, or restart the peer system
- [ ] All endpoints and credentials arrive as variables; no discovery/scanning
- [ ] No secret retrieval in tasks; `no_log: true` on any task touching a secret variable
- [ ] Variable names follow `jenkins_integration_<target>_<thing>` pattern
- [ ] `meta/argument_specs.yml` present and complete
- [ ] molecule `default` scenario converges idempotently (no live peer required)
- [ ] README states what it configures, inputs, and explicit non-goals
