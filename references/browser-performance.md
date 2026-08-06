# Browser Performance Review Reference

Source snapshot: 2026-08-06.

Primary sources:
- web.dev Core Web Vitals: https://web.dev/articles/vitals
- Chrome DevTools performance documentation: https://developer.chrome.com/docs/devtools/performance
- MDN performance documentation: https://developer.mozilla.org/en-US/docs/Web/Performance
- Next.js optimizing documentation: https://nextjs.org/docs/app/building-your-application/optimizing

Use this reference when reviewing bundle size, network waterfalls, render churn, hydration cost, memoization, images/assets, Core Web Vitals risk, browser caching, or payload shape.

## Review Checklist

- Check whether new dependencies, client components, shared layouts, or imports increase critical bundle size.
- Look for accidental client-side inclusion of server libraries, icon packs, editors, charts, date libraries, SDKs, or admin-only code.
- Review waterfalls: sequential fetches, nested component data dependencies, blocking fonts/scripts/styles, and avoidable client-after-server refetches.
- Keep payloads shaped to UI needs; avoid sending whole records, relation trees, markdown/html blobs, or large JSON in initial render paths.
- Check hydration cost for large client trees, nondeterministic markup, overuse of context providers, and client components in high-traffic layouts.
- Review render churn: unstable object/function props, broad context updates, uncontrolled effect loops, unnecessary state synchronization, and list keys.
- Use memoization only when it reduces measured or obvious work; avoid `memo`, `useMemo`, and `useCallback` as noise that hides stale data or dependency bugs.
- Optimize images and media for size, dimensions, lazy/eager loading, priority, formats, and layout stability.
- Check Core Web Vitals concepts: LCP element priority, INP interaction cost, CLS from dynamic layout shifts, and TTFB where server latency matters.
- Confirm browser caching, CDN caching, route caching, and revalidation match freshness requirements.
- Review third-party scripts for loading strategy, consent/privacy needs, main-thread work, and failure isolation.
- Ensure tests, bundle analysis, traces, or performance budgets exist when risk is material.

## Common Review Smells

- One small UI feature imports a large shared dependency into the main bundle.
- Server data is fetched again immediately after hydration without a freshness reason.
- `useEffect` drives derived render state and causes extra renders or flicker.
- Memoization is added broadly while dependencies remain unstable.
- Images lack width/height, use oversized sources, or become the LCP bottleneck.
- Infinite scroll or table views fetch unbounded payloads.
- Third-party scripts load before critical UI or block user interaction.
- Performance claim has no measurement, budget, or before/after evidence for a risky change.

## Primary-Source Refresh Triggers

- React, Next.js, bundler, browser, image pipeline, or CDN/runtime version upgrades.
- Changes to Core Web Vitals definitions, browser performance APIs, hydration/streaming behavior, or framework caching.
- New large dependencies, third-party scripts, analytics SDKs, editors, maps, charts, media, or global providers.
- Performance-sensitive pages, mobile-heavy flows, dashboard/table/list rendering, or route-level data loading changes.
- Regressions found through lab traces, field metrics, real-user monitoring, or user reports.
