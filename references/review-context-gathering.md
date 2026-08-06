# Review Context Gathering

Source snapshot: 2026-08-06.

Use this reference when starting any WKND code review.

## Context Packet

Collect the available:

- User request and inferred output mode.
- PR title/body, base/head commits, changed files, and changed hunks.
- Linked issues, design docs, acceptance criteria, and discussion.
- Nearby local conventions and recent similar changes.
- Tests touched, tests missing, and CI status when available.
- Schema, migration, dependency, runtime, deployment, and mobile configuration changes.
- Unavailable context that should be reported as residual risk.

## Risk Labels

Preserve these labels in the coordinator risk map: auth/security, data and migrations, public API behavior, UI behavior, routing/data loading/forms/mutations, concurrency and retries, performance, mobile runtime, dependencies, CI/deployment, observability, and test strategy.
