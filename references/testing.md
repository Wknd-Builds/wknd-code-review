# Testing Review Reference

Source snapshot: 2026-08-06.

Primary sources:
- Testing Library guiding principles: https://testing-library.com/docs/guiding-principles
- Playwright test documentation: https://playwright.dev/docs/intro
- Vitest guide: https://vitest.dev/guide/
- Jest documentation: https://jestjs.io/docs/getting-started

Use this reference when reviewing test adequacy, regression coverage, fixtures, integration boundaries, route/mobile tests, migration tests, CI confidence, or changes that alter testing strategy.

## Review Checklist

- Scale test expectations to risk: auth, data loss, migrations, payments, tenant isolation, concurrency, and deploy behavior need stronger proof than copy or low-risk refactors.
- Prefer intent-first test names that describe condition, action, and expected product outcome.
- Test through meaningful integration boundaries: public API, route, component behavior, database transaction, server action, navigation flow, or mobile screen behavior.
- Cover success, validation failure, unauthorized/forbidden, no-result, dependency failure, conflict, retry, duplicate submit, and race-prone branches when those outcomes matter.
- Use realistic fixtures and factories with local cleanup or transactions. Avoid shared mutable global seeds unless the project has a safe pattern.
- Verify migrations with representative schema/data when the migration can fail from existing rows, locks, constraints, or deploy order.
- For route tests, cover params, search params, loader/action branches, pending UI, redirects, error boundaries, cookies/sessions, and revalidation.
- For frontend tests, prefer user-visible behavior over implementation state. Use accessible queries when possible.
- For mobile tests, consider iOS/Android divergence, navigation, app lifecycle, offline state, permissions, and native-module behavior.
- Avoid brittle mocks that bypass the behavior under review; mock external services at the edge and assert important request/response contracts.
- Ensure CI actually runs the test class needed for confidence and fails on relevant regressions.
- Check flakes, time-dependent assertions, network dependency, test isolation, and cleanup.
- Confirm tests fail for the bug or risk they claim to cover when feasible.

## Common Review Smells

- Tests assert snapshots or implementation details but not the user-visible or API-visible outcome.
- New auth, migration, or data-access behavior has only unit tests with mocked persistence.
- Mock setup duplicates the implementation, making the test pass for the same wrong reason.
- Race-prone code lacks duplicate-submit, retry, or out-of-order completion coverage.
- Fixture data is broad and mysterious, hiding the scenario under test.
- CI skips tests by path, package, tag, workspace, or runtime version.
- Test names say "works" or "handles error" without naming the condition.

## Primary-Source Refresh Triggers

- Testing framework, runner, browser automation, mobile testing, or assertion library upgrades.
- New framework test helpers, route/server action testing APIs, fake timer behavior, or module mocking behavior.
- Security, migration, concurrency, mobile native, or deployment changes where local test patterns may be insufficient.
- CI runtime changes, sharding, caching, retries, parallelization, or flake-management changes.
- A production incident or regression where existing tests failed to catch the issue.
