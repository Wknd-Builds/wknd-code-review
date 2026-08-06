# Auth And Sessions Review Reference

Source snapshot: 2026-08-06.

Primary sources:
- OWASP Authentication Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html
- OWASP Session Management Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html
- OWASP CSRF Prevention Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html
- OAuth 2.0 Security Best Current Practice: https://datatracker.ietf.org/doc/html/rfc9700

Use this reference when reviewing authentication, authorization, sessions, cookies, CSRF, OAuth callbacks, redirects, password/MFA flows, tenant isolation, PII, secrets, or ownership enforcement.

## Review Checklist

- Derive user identity, tenant, role, permissions, ownership, and account status from server-side session or verified tokens, never request body, hidden fields, query params, or client state.
- Keep authentication checks separate from authorization checks; logged-in is not the same as allowed.
- Enforce authorization close to each read/write operation and include tenant or ownership constraints in data access.
- Review session cookies for `HttpOnly`, `Secure`, `SameSite`, path/domain scope, expiration, rotation, revocation, and environment-specific behavior.
- Check CSRF protection for cookie-authenticated state-changing requests, including framework forms, fetchers, server actions, and route handlers.
- Validate redirect targets using same-origin allowlists or explicit destinations. Protect login, logout, OAuth callback, invitation, and return-to flows.
- Review OAuth/OIDC state, nonce, PKCE, callback validation, account linking, replay handling, and error branches.
- Treat password reset, email change, MFA enrollment, MFA recovery, and invitation acceptance as high-risk account takeover flows.
- Avoid user enumeration through response messages, status codes, timing, logs, and password/MFA recovery behavior.
- Protect secrets, tokens, refresh tokens, API keys, and session IDs from logs, analytics, client bundles, URLs, and error responses.
- Minimize PII collection and exposure; redact diagnostics and verify audit events avoid sensitive payloads.
- Check rate limits and abuse protections on login, signup, password reset, MFA, invitation, and token exchange endpoints.
- Confirm session fixation, privilege changes, logout, account deletion, and tenant switching rotate or invalidate relevant state.
- Test unauthorized, forbidden, cross-tenant, expired session, revoked token, duplicate callback, and replay scenarios.

## Common Review Smells

- Mutation accepts `userId`, `tenantId`, `role`, `isAdmin`, or ownership fields from form/body input.
- Tenant filters are applied in route code but omitted in deeper data access.
- Redirect URL comes directly from `returnTo` or OAuth state without validation.
- Password reset or MFA recovery creates a valid session before completing all checks.
- Session cookies are secure in production but unsafe defaults leak into preview or staging.
- Logs include authorization headers, cookies, reset tokens, OTPs, raw profile payloads, or sensitive PII.
- Tests cover happy-path login but not cross-tenant or forbidden access.

## Primary-Source Refresh Triggers

- Changes to auth libraries, OAuth/OIDC providers, session storage, cookie policy, MFA flows, password hashing, or token formats.
- New account linking, invitation, password reset, email change, tenant switching, or role/permission behavior.
- Security-sensitive redirects, CSRF protection, same-site cookie behavior, or browser platform changes.
- Incident response, audit/compliance requests, or changes involving PII, secrets, logs, analytics, or telemetry.
- Framework routing or server-action changes that alter where auth checks execute.
