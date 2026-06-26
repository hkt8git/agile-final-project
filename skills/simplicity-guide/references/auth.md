# Auth

The ladder for how you verify identity. Start at framework sessions; climb only
when a stated requirement forces it.

## framework-sessions

The built-in session/cookie handling your framework already ships.

- **Use when:** you control the user database, support a single login method,
  and the framework's built-ins are sufficient.
- **Climb when:** you need standard auth flows (password reset, email
  verification, MFA) or multiple login methods, while still owning user data.
- **Cost:** none beyond your existing framework. No extra library, no external
  service.
- **Rationale template:** `"chose framework sessions — {own user table},
  {single login method}, {built-ins sufficient}"`

## auth-library

A dedicated auth library (Devise, Passport, Lucia, etc.) that you own and run.

- **Use when:** you need standard flows like password resets, email verification,
  or multi-factor authentication, but still want to manage user data yourself;
  no external IdP needed.
- **Climb when:** you need enterprise requirements (SSO, SAML), or compliance
  burden justifies offloading credential storage entirely.
- **Cost:** a library dependency and its configuration to own; standard flows
  work out of the box but you still run the user database.
- **Rationale template:** `"chose auth library — {standard flows needed},
  {own user data}, {no external IdP}"`

## third-party-identity

A hosted identity service (Auth0, Clerk, Cognito, etc.).

- **Use when:** you need enterprise features like SSO or SAML, or compliance
  requirements make offloading credential storage worth the cost.
- **Climb when:** (top of this ladder) — federation and multi-tenant identity
  are a separate, later decision driven by stated requirements.
- **Cost:** an external dependency and vendor lock-in to manage, plus
  integration and a recurring bill.
- **Rationale template:** `"chose third-party identity — {SSO/SAML required},
  {compliance burden}, {credential offload justified}"`
