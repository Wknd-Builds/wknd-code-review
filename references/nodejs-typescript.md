# Node.js And TypeScript Review Reference

Source snapshot: 2026-08-06.

Primary sources:
- Node.js documentation: https://nodejs.org/docs/latest/api/
- TypeScript handbook: https://www.typescriptlang.org/docs/handbook/intro.html
- Node.js best practices: https://github.com/goldbergyoni/nodebestpractices
- npm package-lock documentation: https://docs.npmjs.com/cli/v11/configuring-npm/package-lock-json/

Use this reference when reviewing Node.js services, API handlers, jobs, workers, package/runtime changes, Dockerized Node apps, or TypeScript server code.

## Review Checklist

- Keep web/API entry points thin; domain logic should not depend on HTTP request/response objects, middleware internals, or global database clients unless that is an established local boundary.
- Favor business/domain components over technical folders that mix unrelated product areas.
- Validate configuration at startup with typed, parsed values; check that secrets stay outside committed code and defaults are deliberate.
- Treat all external inputs as untrusted: JSON bodies, form data, params, headers, cookies, environment variables, queue messages, webhooks, files, and third-party API responses.
- Check TypeScript for broad casts, `any`, assertion-heavy code, `as unknown as`, disabled strictness, untyped config, and types that pretend runtime validation happened.
- Prefer schema parsing or explicit runtime checks at trust boundaries. A typed value from `req.body`, `FormData`, `process.env`, or a webhook is still untrusted.
- Use domain-specific or structured errors when callers need to distinguish validation, not-found, conflict, dependency, and authorization failures.
- Centralize internal-to-user error translation. Do not leak stack traces, SQL errors, secret names, tokens, raw credentials, or internal IDs.
- Await promises when stack quality, cleanup, transaction completion, or failure visibility matters.
- Ensure event emitters, streams, background tasks, fire-and-forget promises, queues, and scheduled jobs have error handlers, retries where appropriate, and observable failure paths.
- Avoid dynamic SQL/query construction; prefer parameterization and explicit allowlists for fields, sorts, filters, and includes.
- Do not trust client-supplied identity, roles, tenant IDs, ownership fields, prices, or status transitions.
- Check rate limits, payload limits, brute-force paths, unsafe redirects, SSRF, path traversal, command execution, regex denial of service, `eval`-like behavior, dynamic module loading, file uploads, and child processes.
- Avoid blocking the event loop with CPU-heavy loops, sync filesystem/network calls on request paths, catastrophic regexes, or huge JSON serialization.
- Review query count, N+1 patterns, unbounded scans, missing pagination, missing indexes, and excessive response payloads.
- Keep services stateless unless state is deliberate and durable.
- Confirm graceful shutdown for servers, workers, queues, streams, and database pools.
- Prefer multi-stage Docker builds, pinned base images, `.dockerignore`, non-root users, lockfile-driven installs such as `npm ci`, and no build-time secret leakage.
- Test public behavior through API/component boundaries, not only internals or mocks.
- Cover success, no result, invalid input, dependency failure, forbidden/unauthorized access, concurrency/retry behavior, and changed error paths.

## Common Review Smells

- `any`, non-null assertions, broad casts, or `// @ts-ignore` around request data, config, auth, database results, or dependency upgrades.
- Validation exists only in UI code while the server trusts the value.
- Broad `catch` blocks swallow errors, retry forever, or return success.
- `void` promises without explicit lifecycle ownership, logging, or rejection handling.
- Authorization checks are far from the mutation or are based on request body identity.
- New shared utilities encode one feature's assumptions as global behavior.
- Snapshot-only tests for business rules.
- Dependency upgrades lack lockfile consistency, changelog review, runtime-version checks, or CI coverage.

## Primary-Source Refresh Triggers

- Node.js, TypeScript, package manager, or runtime version changes.
- New or changed Node APIs, stream behavior, worker threads, test runner usage, permissions, loaders, ESM/CJS interop, or Docker base images.
- Security-sensitive changes involving auth, parsing, cryptography, dependency installation, file access, subprocesses, SSRF, uploads, or secrets.
- Type-system changes that relax strictness, introduce generated types, or depend on framework-specific TypeScript behavior.
- Production incident fixes where current runtime or package documentation may affect the correct remediation.
