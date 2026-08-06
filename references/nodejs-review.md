# Node.js Review Reference

Source snapshot: 2026-07-31. Based on the public 2026 edition of `goldbergyoni/nodebestpractices`: https://github.com/goldbergyoni/nodebestpractices

Use this reference to review Node.js, TypeScript server code, APIs, jobs, workers, config, runtime behavior, package changes, Docker, and test strategy.

## Architecture

- Favor business/domain components over technical folders that mix unrelated product areas.
- Keep web/API entry points thin. Domain logic should not depend on HTTP request/response objects, framework middleware details, or database client globals unless the local architecture already does.
- Reusable infrastructure belongs behind explicit package/module boundaries with stable exports.
- Validate configuration at startup, keep secrets outside committed code, and make environment defaults intentional.
- Check whether TypeScript usage improves domain guarantees or merely suppresses uncertainty with broad types, casts, `any`, or type assertions.

## Error Handling

- Prefer `async`/`await` or promises with clear control flow.
- Use domain-specific error types or structured errors where callers need to distinguish operational errors from programmer bugs.
- Centralize translation of internal errors to user-facing responses. Do not leak stack traces, SQL errors, secret names, or internal IDs to clients.
- Await promises before returning when stack trace quality or cleanup matters.
- Subscribe to `error` events on emitters and streams.
- Ensure background tasks, fire-and-forget promises, queues, and scheduled jobs have logging, retries, and failure visibility.

## Testing And Quality

- API/component tests should cover the public behavior, not internal mocks.
- Test names should describe condition, action, and expected result.
- Use Arrange, Act, Assert structure where it clarifies intent.
- Avoid global fixtures and shared mutable seeds. Create data per test unless the project has a safe transaction/cleanup pattern.
- Cover the five common outcomes: success, no result, invalid input, dependency failure, and forbidden/unauthorized access.
- Middleware, auth guards, serializers, migrations, and error paths deserve focused tests when changed.
- For dependency or runtime changes, check lockfiles, Node version policy, CI coverage, and migration/rollback impact.

## Security

- Validate and parse all external inputs: JSON bodies, form data, params, headers, cookies, environment variables, queue messages, and webhook payloads.
- Avoid dynamic SQL/query construction. Prefer ORM/query-builder parameterization and explicit field allowlists.
- Do not trust client-supplied identity, roles, tenant IDs, ownership fields, prices, or status transitions.
- Protect passwords and secrets with appropriate hashing/storage. Never log secrets, session tokens, raw credentials, or sensitive PII.
- Check rate limiting, payload limits, auth brute-force paths, unsafe redirects, SSRF, path traversal, command execution, regex denial of service, and `eval`-like behavior.
- Treat child processes, dynamic module loading, sandbox execution, file uploads, and dependency upgrades as high-risk.
- Ensure cookies and sessions use secure settings appropriate to the environment.

## Performance And Runtime

- Avoid blocking the event loop with CPU-heavy loops, sync filesystem/network operations on request paths, catastrophic regexes, or large JSON serialization.
- Check query count, N+1 patterns, unbounded scans, missing pagination, missing indexes, and excessive payloads.
- Prefer native language/runtime methods where they are clearer and avoid pulling large dependencies for small utilities.
- Keep services stateless unless state is deliberate and durable.
- Log to stdout/stderr in production unless the platform requires otherwise.
- Ensure graceful shutdown for servers, workers, queues, and database pools.

## Docker And Deployment

- Prefer multi-stage builds, pinned base images, `.dockerignore`, non-root users, and no build-time secret leakage.
- Use lockfile-driven installs such as `npm ci` in CI or production images.
- Make health/maintenance endpoints, observability, and rollback implications part of the review when deployment behavior changes.

## Review Smells

- Broad `catch` blocks that swallow errors or return success.
- `void` promises without explicit logging or lifecycle ownership.
- Validation only in UI code for server-trusted data.
- Authorization checks far away from the mutation they protect.
- New shared utilities that encode one feature's assumptions as global behavior.
- Snapshot-only tests for business logic.
- Dependency upgrades with no changelog review or lockfile consistency.
