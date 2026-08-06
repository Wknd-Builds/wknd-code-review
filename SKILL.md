---
name: wknd-code-review
description: Use when reviewing pull requests, diffs, commits, code changes, GitHub PRs, linked issues, Node.js backends, React apps, React Router Framework Mode, Remix-style route modules, tests, architecture, security, performance, maintainability, or merge readiness.
---

# WKND Code Review

## Overview

Run reviews in two passes: first understand the product, intent, architecture, and test strategy; then inspect the changed lines for logic, security, maintainability, and edge cases. The goal is a merge decision with structured feedback, not a list of isolated observations.

## Load References

- Read `references/nodejs-review.md` when the diff touches Node.js, server code, config, package/dependency changes, databases, queues, auth, API boundaries, workers, tests, Docker, or runtime behavior.
- Read `references/react-router-framework-review.md` when the diff touches React Router, Remix-style route modules, `routes.ts`, loaders, actions, fetchers, forms, sessions, cookies, pending UI, route tests, SSR, SPA, SSG, or hydration.
- Browse primary sources when the user asks for the latest guidance or when reviewing a version-specific framework change.

## Review Workflow

1. Establish scope before judging:
   - Identify base and head commits, changed files, PR title/body, linked issues, design docs, acceptance criteria, and discussion.
   - Read product docs, README, route maps, domain modules, schema/migrations, and tests relevant to the changed area.
   - Summarize the intended behavior in your own words. If intent is ambiguous, state the assumption and review against it.

2. Learn local conventions:
   - Inspect nearby files and recent similar changes.
   - Prefer current project patterns over generic style preferences.
   - Check whether the PR follows existing boundaries, naming, error handling, validation, state management, and test style.

3. High-level review:
   - Architecture: ownership boundaries, layering, coupling, data flow, deployment/runtime impact.
   - Product/spec fit: whether the implementation actually solves the linked issue without surprising adjacent workflows.
   - Performance: event-loop blocking, query shape, bundle impact, render churn, caching, waterfalls, concurrency, and payload size.
   - Security/privacy: authz, authn, sessions, CSRF, injection, unsafe redirects, secrets, PII leakage, dependency risk.
   - Test strategy: coverage for intent, regressions, edge cases, failure paths, integration boundaries, and realistic user flows.

4. Line-by-line pass:
   - Read every changed hunk and nearby context.
   - Trace values across caller/callee boundaries, async paths, serialization, request lifetimes, and retries.
   - Look for missing awaits, stale data, partial failure, race conditions, type lies, unvalidated input, leaked implementation details, and broken invariants.
   - Verify reported findings against the actual code. Do not speculate as a finding.

5. Decide:
   - `Approved`: no blocking or important issues.
   - `Approved with comments`: only nits, suggestions, learning notes, or praise.
   - `Changes requested`: any blocking issue, or important issues that materially threaten correctness, security, maintainability, or product behavior.
   - `Needs clarification`: intent or external constraints are too unclear to fairly approve or request changes.

## Severity Labels

| Label | Meaning |
| --- | --- |
| 🔴 blocking | Must be fixed before merge |
| 🟠 important | Should be fixed; may block depending on context |
| 🟡 nit | Minor style or preference issue |
| 🔵 suggestion | Optional improvement worth considering |
| 📚 learning | Educational note for the author |
| 🌟 praise | Explicitly highlight great work |

## Output Format

Lead with findings, ordered by severity. Use this shape:

```markdown
**Decision:** Changes requested

**Findings**
- 🔴 blocking [path/to/file.ts:42] The action trusts `userId` from form data, so a user can update another account. Use the authenticated session user instead and add a regression test.
- 🌟 praise [path/to/file.ts:88] Nice use of the existing route boundary; the data ownership stays local to the account domain.

**Summary**
One or two sentences about the review scope and overall quality.

**Action Items**
- Replace client-supplied identity with session-derived identity.
- Add an integration test for cross-user update rejection.

**Residual Risk**
Mention anything not verified, such as tests not run, missing environment, or unavailable linked issue.
```

Rules:
- Findings must include file and line when possible.
- Keep each finding actionable: issue, impact, fix direction.
- Do not bury blocking issues in prose.
- Include praise when there is genuinely strong work.
- If there are no findings, say so clearly and list residual risk or test gaps.
- Avoid style-only comments unless they affect consistency, readability, or future mistakes.

## Common Mistakes

| Mistake | Correction |
| --- | --- |
| Reviewing only the diff | Read surrounding code, callers, tests, and product context first. |
| Treating preferences as blockers | Use blocking only for correctness, security, data loss, severe UX, or merge-breaking maintainability. |
| Missing test strategy | Judge whether tests prove the intended behavior and important failure modes. |
| Ignoring linked issues | Confirm the PR solves the stated problem and does not widen scope silently. |
| Overlooking good work | Add 🌟 praise for clean boundaries, sharp tests, or thoughtful design. |
