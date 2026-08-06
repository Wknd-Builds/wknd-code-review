# React Router Framework Mode Review Reference

Source snapshot: 2026-07-31. Based on official React Router docs, latest shown as 8.3.0:

- Picking a Mode: https://reactrouter.com/start/modes#framework
- Route Module: https://reactrouter.com/start/framework/route-module
- Data Loading: https://reactrouter.com/start/framework/data-loading
- Actions: https://reactrouter.com/start/framework/actions
- Form Validation: https://reactrouter.com/how-to/form-validation
- Race Conditions: https://reactrouter.com/explanation/race-conditions
- Testing: https://reactrouter.com/start/framework/testing
- Security: https://reactrouter.com/how-to/security

Use this reference for React Router Framework Mode and Remix-style full-stack React apps.

## Framework Mode Baseline

- Framework Mode builds on Data Mode with route modules, type-safe route APIs, type-safe `href`, code splitting, and SPA/SSR/static rendering strategies.
- Route modules are the unit of review for data loading, mutations, revalidation, error boundaries, links/meta/headers, and route UI.
- Prefer generated `Route.*` types for route module args and props. Broad casts around route params, loader data, action data, or matches are review risks.

## Loaders And Data

- `loader` runs on the server for SSR and during pre-render builds; server loader code is removed from client bundles.
- `clientLoader` runs in the browser and can supplement or replace server data. If it sets `hydrate = true`, confirm there is an intentional `HydrateFallback`.
- Loader returns must be serializable across the route boundary. Watch for class instances, functions, non-serializable clients, accidental secret exposure, or oversized data.
- Validate route params and search params before use. A typed param is still untrusted input.
- Check revalidation behavior when mutations, caching, optimistic UI, parent loaders, or `shouldRevalidate` change.
- Avoid duplicate client/server fetching unless it is intentional and tested.

## Actions, Forms, And Mutations

- `action` runs on the server and is removed from client bundles. `clientAction` runs in the browser and takes priority when both exist.
- Mutations should derive identity, permissions, tenant, and ownership from trusted session/server context, not form fields.
- Use `<Form>`, `useSubmit`, `fetcher.Form`, and `fetcher.submit` according to navigation intent:
  - Forms navigate and add history entries.
  - Fetchers submit without navigation and are better for inline mutations.
- Return validation failures with an appropriate non-2xx status, commonly 400, so data revalidation semantics stay intentional.
- Ensure action responses make pending, success, validation, authorization, and dependency-failure states visible to the UI.
- Confirm idempotency or duplicate-submit handling when a mutation can be retried or clicked twice.

## UI State And Concurrency

- Prefer React Router pending UI and fetcher state over ad hoc local loading flags when route navigation or form submissions are involved.
- React Router cancels interrupted navigations and stale fetcher requests for common UI race conditions, but the server may still process canceled requests. Protect backend data integrity separately.
- Check type-ahead, autosave, optimistic UI, and parallel fetchers for stale commits, out-of-order UI, and rollback behavior.
- Avoid storing server-derived route data in long-lived component state unless there is a clear synchronization strategy.

## Security

- Treat loaders and actions as public request handlers.
- Validate form data, params, search params, cookies, and headers on the server.
- Check CSRF assumptions for cookie-authenticated mutations.
- Prevent unsafe redirects from `returnTo`, `redirectTo`, or search-param targets.
- Keep secrets and privileged data inside server-only modules, loaders, actions, or `.server` files when the project uses that convention.
- When Content Security Policy is changed, verify nonce propagation through Framework Mode server rendering components and stream rendering options.

## Error Boundaries And Responses

- Route-level `ErrorBoundary` should distinguish expected route responses from unexpected errors.
- Do not expose internal messages to users.
- Preserve HTTP semantics for not found, validation, unauthorized, forbidden, conflict, and server errors.
- Confirm parent and child boundaries produce usable UX for failed loaders/actions.

## Testing

- Use `createRoutesStub` for reusable components that depend on router context such as loader data, action data, matches, links, and forms.
- Be careful using `createRoutesStub` for Framework Mode route components typed with generated `Route.*` types; route-level behavior often belongs in integration or E2E tests against a running app.
- Tests for route changes should cover:
  - loader success and not-found/error paths
  - action success, validation, authorization, and duplicate-submit behavior
  - revalidation and pending UI
  - URL params/search params
  - session/cookie behavior
  - progressive enhancement where forms should work without custom client JavaScript

## Review Smells

- `String(formData.get(...))` used without schema validation or empty/null handling.
- Trusting hidden inputs for authorization-sensitive values.
- Redirect targets taken directly from request input.
- Loader exposes more fields than the UI needs.
- Route component uses untyped hooks where generated props would preserve stronger route typing.
- `clientLoader` duplicates server loader logic without a cache or hydration reason.
- Fetcher mutation changes server data but leaves surrounding loader data stale.
- Tests silence Framework Mode type mismatches without a higher-level integration test.
