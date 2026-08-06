# Review Context Gathering

Source snapshot: 2026-08-06.

Use this reference when starting any WKND code review.

## Context Packet

Collect available context before judging the diff. Record what is unavailable and continue with local evidence when enough remains to review responsibly.

Required packet fields:

- User request and inferred output mode.
- Base/head refs, compare range, PR number, author, branch, or other PR metadata when available.
- PR title/body, linked issues, design docs, acceptance criteria, release notes, tickets, and discussion.
- Changed files, file statuses, generated files, renames, deletions, and changed hunks.
- Nearby code around each risky hunk, callers/callees, route or API entry points, schema consumers, tests, fixtures, and local conventions.
- Recent similar changes and project patterns that clarify expected behavior.
- Tests touched, tests not touched, manual verification notes, and CI status when available.
- Schema, migration, seed, package, lockfile, runtime, environment, build, deployment, permission, and mobile configuration changes.
- External context that was unavailable but material to confidence.

## What To Inspect

- Read changed hunks first, then nearby code needed to understand execution paths.
- Trace changed data across request boundaries, database writes, cache invalidation, jobs, UI state, and external APIs when relevant.
- Compare tests against the user-facing behavior or failure mode introduced by the PR.
- Check migrations and deploy order together; a migration can be correct alone and unsafe in rollout.
- Treat package, lockfile, engine, runtime, framework, and CI changes as behavior changes until proven otherwise.
- Prefer local project conventions over generic advice unless the convention conflicts with correctness, security, or platform requirements.

## Risk Labels

Preserve the canonical risk-label set in the coordinator risk map:

- Auth/security.
- Data and migrations.
- Public API behavior.
- UI behavior.
- Routing, data loading, forms, and mutations.
- Concurrency and retries.
- Performance.
- Mobile runtime.
- Dependencies.
- CI/deployment.
- Observability.
- Test strategy.

Use the labels to route specialist review and explain coverage. A single hunk can carry multiple labels, such as public API behavior plus concurrency and retries for an idempotent mutation.

## Unavailable Context

If important context is unavailable:

- Say exactly what is missing.
- Explain why it matters to confidence.
- Continue with available local evidence when possible.
- Put the gap in `Residual Risk` unless it prevents fair judgment, in which case use `Needs clarification`.
- Do not claim CI, linked issue, changelog, production behavior, or GitHub state was checked unless it was actually available.
