# React And Next.js Review Reference

Source snapshot: 2026-08-06.

Primary sources:
- React documentation: https://react.dev/reference/react
- React rules of hooks: https://react.dev/reference/rules/rules-of-hooks
- Next.js App Router documentation: https://nextjs.org/docs/app
- Next.js caching guide: https://nextjs.org/docs/app/guides/caching
- Next.js mutating data guide: https://nextjs.org/docs/app/getting-started/mutating-data

Use this reference when reviewing React UI, hooks, component boundaries, Next.js App Router code, server/client component boundaries, route handlers, server actions, caching, hydration, metadata, redirects, or serialization behavior.

## Review Checklist

- Check component boundaries: server-only data access should stay in Server Components, route handlers, server actions, or server-only modules; browser-only APIs belong behind client boundaries.
- Use `"use client"` deliberately. Confirm it does not drag secrets, database clients, large server libraries, or unnecessary bundle weight into the client.
- Keep props crossing server/client boundaries serializable and minimal.
- Review hooks for rules-of-hooks violations, unstable dependencies, stale closures, accidental infinite effects, and effects that duplicate render-time derivation.
- Avoid storing server-derived props in local state unless there is a clear synchronization plan.
- Check forms and mutations for pending, success, validation, authorization, conflict, and retry states.
- In server actions and route handlers, derive identity, tenant, ownership, and permissions from server context, not submitted fields.
- Validate all route params, search params, headers, cookies, JSON bodies, and form data at the server boundary.
- Review Next.js caching: `fetch` options, `revalidate`, tags, `cache`, `unstable_cache`, dynamic rendering, `no-store`, and invalidation paths must match product freshness requirements.
- Check redirects and `notFound` behavior for unsafe destinations, lost state, and correct HTTP semantics.
- Review metadata generation for excessive fetching, private data exposure, and static/dynamic mismatch.
- Check hydration for nondeterministic render output: dates, random values, browser-only state, locale differences, auth-only branches, and feature flags.
- Ensure route handlers set status codes, cache headers, content types, and error responses deliberately.
- Keep client payloads small: avoid over-fetching, excessive JSON in Server Component payloads, and large client components in common layouts.
- Test important user flows at the boundary they run through: rendered UI, route handler, server action, cache invalidation, redirect, and hydration-sensitive behavior.

## Common Review Smells

- `"use client"` added to a layout or shared component to fix one hook error.
- `useEffect` copies props to state without a resync strategy.
- Hook dependency arrays are suppressed instead of making dependencies stable.
- Server actions trust hidden fields for user ID, tenant ID, role, price, or ownership.
- Cached data is mutated without tag/path invalidation or a freshness story.
- Dynamic per-user data is fetched in a static route segment.
- Non-serializable values cross the server/client boundary.
- Route handlers return `200` for validation, auth, conflict, or dependency failures.

## Primary-Source Refresh Triggers

- React or Next.js version upgrades, App Router changes, React Compiler adoption, or new server/client rendering behavior.
- Changes involving server actions, route handlers, caching/revalidation, streaming, redirects, metadata, or hydration.
- Security-sensitive mutations, auth-dependent rendering, cookie/session handling, or edge/runtime changes.
- Performance-sensitive layout changes, shared client components, or large dependency additions to client bundles.
- Tests or fixes that depend on current framework cache invalidation or serialization semantics.
