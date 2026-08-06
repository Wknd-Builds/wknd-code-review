# React Router And Remix Review Reference

Source snapshot: 2026-08-06.

Primary sources:
- React Router Framework Mode: https://reactrouter.com/start/modes#framework
- React Router route modules: https://reactrouter.com/start/framework/route-module
- React Router data loading: https://reactrouter.com/start/framework/data-loading
- React Router actions: https://reactrouter.com/start/framework/actions
- React Router race conditions: https://reactrouter.com/explanation/race-conditions
- React Router testing: https://reactrouter.com/start/framework/testing
- Remix documentation: https://remix.run/docs/en/main

Use this reference when reviewing React Router Framework Mode, Remix, or Remix-style full-stack React routes with loaders, actions, forms, route modules, revalidation, SSR, or client data APIs.

## Review Checklist

- Treat route modules as the review unit for loaders, actions, revalidation, error boundaries, meta/headers, links, and route UI.
- Prefer generated `Route.*` types or local route types over broad casts around params, loader data, action data, and matches.
- Remember that server loaders/actions are public request handlers even when colocated with UI.
- Validate params, search params, form data, cookies, headers, and loader/action inputs on the server. Typed route params are still untrusted strings.
- Ensure loader returns are serializable, minimal, and free of secrets, privileged fields, non-serializable clients, class instances, and oversized payloads.
- Review `clientLoader` and `clientAction` for duplicated server logic, cache/hydration intent, offline assumptions, and divergence from server authorization.
- Check `clientLoader.hydrate`, `HydrateFallback`, SSR, streaming, and partial hydration behavior when route data changes.
- Use `<Form>`, `useSubmit`, `fetcher.Form`, and `fetcher.submit` according to navigation intent; fetchers are better for inline mutations that should not navigate.
- Return validation failures with appropriate non-2xx status codes so revalidation and pending UI remain intentional.
- Derive identity, permissions, tenant, ownership, and price/status authority from trusted session/server context, not hidden inputs or client state.
- Confirm mutation responses expose pending, success, validation, authorization, dependency-failure, and retry states to the UI.
- Verify duplicate-submit, retry, cancellation, idempotency, and race behavior. Canceled browser requests may still finish on the server.
- Review `shouldRevalidate`, optimistic UI, parallel fetchers, autosave, type-ahead, parent loader data, and rollback paths for stale or out-of-order state.
- Avoid copying server-derived route data into long-lived component state unless synchronization is explicit.
- Check CSRF assumptions for cookie-authenticated mutations.
- Prevent unsafe redirects from `returnTo`, `redirectTo`, or search-param targets.
- Keep secrets and privileged code inside server-only modules, loaders, actions, or `.server` files when that convention exists.
- Ensure route `ErrorBoundary` behavior distinguishes expected route responses from unexpected exceptions and preserves useful HTTP semantics.
- Test loader success and not-found/error paths, action success/validation/auth/duplicate-submit paths, revalidation, pending UI, params/search params, session/cookie behavior, and progressive enhancement.

## Common Review Smells

- `String(formData.get(...))` without schema validation, null handling, or empty-string policy.
- Hidden inputs provide authorization-sensitive values.
- Redirect targets pass directly from request input.
- Loader exposes more fields than the UI needs.
- Route components use untyped hooks where generated route props would preserve stronger typing.
- `clientLoader` duplicates server data fetching without cache, hydration, or offline reason.
- Fetcher mutations change server data but leave surrounding loader data stale.
- Tests silence Framework Mode type errors without an integration or route-level test.

## Primary-Source Refresh Triggers

- React Router or Remix major/minor upgrades, generated type changes, routing mode changes, or SSR/static rendering changes.
- New usage of `clientLoader`, `clientAction`, `defer`/streaming, revalidation overrides, route module exports, or framework-provided testing helpers.
- Security-sensitive loader/action changes involving sessions, cookies, redirects, CSRF, file uploads, or server-only modules.
- Race-condition fixes, optimistic UI changes, autosave/type-ahead changes, or mutation retries.
- Hydration, serialization, caching, or error-boundary behavior that depends on current framework semantics.
