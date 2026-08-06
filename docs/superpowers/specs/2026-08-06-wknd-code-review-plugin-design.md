# WKND Code Review Plugin Design

Date: 2026-08-06

## Goal

Redesign `wknd-code-review` from a compact review skill into a comprehensive Codex plugin bundle for high-stakes production code reviews.

The plugin should optimize for:

- Deeper review judgment first: context gathering, risk mapping, specialist review, severity calibration, and defensible merge decisions.
- Broader stack coverage second: modern TypeScript web apps, Node backends, React, React Router/Remix, Next.js, Prisma/Postgres, auth/sessions, testing, CI/deployment, accessibility, browser performance, observability, dependency/security review, and React Native/Expo.
- Better automation third: GitHub-ready review drafting and explicit posting when the user asks.

The plugin must keep one simple user-facing invocation model. A user should be able to say "run a WKND code review" or "run a WKND code review and post your findings" without choosing among plugin-provided skills.

## Non-Goals

- Do not add Python or Go review references in the first comprehensive version.
- Do not make review mode edit application code. Reviews may include remediation sketches and follow-up tasks, but fixes happen in a separate request.
- Do not require the user to know or invoke separate skills for report mode versus GitHub mode.
- Do not post GitHub comments or submit reviews unless the user explicitly asks.

## Architecture

Use a full plugin bundle with one public skill and internal specialist reviewers.

```text
wknd-code-review/
  .codex-plugin/plugin.json
  SKILL.md
  agents/
    coordinator.md
    appsec-auth-reviewer.md
    data-migrations-reviewer.md
    test-strategy-reviewer.md
    frontend-runtime-reviewer.md
    accessibility-reviewer.md
    performance-reviewer.md
    dependency-supply-chain-reviewer.md
    ci-deployment-reviewer.md
    observability-reviewer.md
    react-native-expo-reviewer.md
  references/
    review-decision-model.md
    review-context-gathering.md
    output-modes.md
    github-posting.md
    nodejs-typescript.md
    react-router-remix.md
    react-next.md
    prisma-postgres.md
    auth-sessions.md
    testing.md
    ci-deployment.md
    accessibility.md
    browser-performance.md
    observability.md
    dependency-security.md
    react-native-expo.md
  fixtures/
    auth-bypass-pr.md
    migration-risk-pr.md
    frontend-race-pr.md
    dependency-upgrade-pr.md
    react-native-permission-pr.md
    clean-pr.md
```

`SKILL.md` is the only public invocation surface. It describes the WKND review contract, loading rules, coordinator workflow, output modes, and safety constraints.

`agents/coordinator.md` is the operational center. It defines the review packet, risk-routing matrix, specialist dispatch rules, finding schema, severity calibration, final decision rules, and output mode selection.

Specialist reviewers are narrow, evidence-driven agents. They do not own the final decision. They return scoped findings with evidence, confidence, affected files or lines, impact, and remediation sketches.

The existing `SKILL.md` should become the seed for the new public skill and coordinator instructions. The existing Node and React Router references should be rewritten into the expanded reference library rather than kept as isolated one-off documents.

## Invocation And Output Modes

The plugin has one public user-facing behavior: WKND Code Review.

The coordinator infers output and action mode from the user request:

- `report only`: default for requests like "run a WKND code review."
- `draft GitHub comments`: used when the user asks for GitHub-ready findings, comments, or PR review text without asking to post.
- `post GitHub review`: used only when the user explicitly says "post," "submit," "publish," or equivalent.

Ambiguous phrases like "prepare findings" or "write review comments" must remain draft-only.

Default report output must use this canonical order:

- Findings ordered by severity.
- Decision.
- Summary.
- Action items.
- Verification evidence.
- Residual risk.

GitHub-ready output should include:

- Top-level PR review body.
- Inline-ready comments with file, line, severity, issue, impact, and fix direction.
- A clear note when comments are drafts.

Post mode should report exactly what was posted or submitted. If posting fails, return the draft review content and the failure reason.

## Workflow And Data Flow

1. Intent detection

   The coordinator classifies the run as `report only`, `draft GitHub comments`, or `post GitHub review`.

2. Context gathering

   Before judging, the coordinator builds a review packet that can include PR title/body, base/head commits, changed files, relevant docs/issues, tests touched, schema/migration changes, package and lockfile changes, CI status, nearby local conventions, and existing project patterns.

   If important context is unavailable, continue with local evidence and record the gap in residual risk.

3. Risk map

   The coordinator labels changed areas by risk category:

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

4. Reference loading and primary-source refresh

   Use local references by default. Require current primary docs, changelogs, or release notes when the change is version-sensitive, security-sensitive, depends on major framework behavior, upgrades important dependencies, or touches unfamiliar libraries.

5. Specialist passes

   Dispatch only relevant specialist reviewers. Each specialist receives a bounded packet:

   - Intent summary.
   - Relevant changed files or hunks.
   - Key surrounding context.
   - Local conventions.
   - Specific questions to answer.

   Specialist output must use a structured finding schema.

6. Coordinator reconciliation

   The coordinator verifies specialist claims against the code, deduplicates findings, adjusts severity using the decision model, and chooses the final decision.

7. Output

   The coordinator emits the default report, GitHub draft, or posted-review result based on the inferred mode.

## Specialist Reviewers

### AppSec And Auth Reviewer

Reviews authentication, authorization, tenant isolation, CSRF, redirects, secrets, PII, injection, webhooks, file/path/command risks, SSRF, rate limits, payload limits, session/cookie settings, and unsafe trust of client-supplied identity.

### Data And Migrations Reviewer

Reviews schema changes, Prisma/Postgres behavior, migration safety, indexes, constraints, backfills, data loss risk, rollback plans, transaction boundaries, concurrent writes, idempotency, query shape, and pagination.

### Test Strategy Reviewer

Reviews whether tests prove product intent, cover important success/failure paths, match local style, avoid brittle mocks, exercise integration boundaries, and include realistic regression coverage for the risk introduced by the PR.

### Frontend Runtime Reviewer

Reviews React, React Router, Remix, and Next.js behavior, including server/client boundaries, loader/action semantics, hydration, revalidation, pending UI, mutation state, stale data, race conditions, error boundaries, serialization, and routing.

### Accessibility Reviewer

Reviews keyboard behavior, focus management, labels, semantic structure, forms, dialogs, dynamic content announcements, contrast-sensitive risks, disabled/loading states, and screen-reader-visible errors.

### Performance Reviewer

Reviews backend query/runtime performance and frontend bundle/render/network performance, including N+1 queries, unbounded scans, event-loop blocking, waterfall fetches, excessive payloads, render churn, cache behavior, and mobile performance where relevant.

### Dependency And Supply-Chain Reviewer

Reviews package changes, lockfile consistency, engine/runtime policy, lifecycle scripts, transitive risk, dependency size, vulnerability context, changelog review, and risky build tooling changes.

### CI And Deployment Reviewer

Reviews build/test pipelines, Docker, environment variables, secrets handling, deploy ordering, migration execution, rollback implications, release safety, and whether CI actually exercises the changed behavior.

### Observability Reviewer

Reviews logs, metrics, traces, error reporting, alertability, debugging visibility, background task failure visibility, privacy-safe diagnostics, and whether new failure modes will be visible in production.

### React Native And Expo Reviewer

Reviews Expo config, native capabilities, permissions, platform divergence, navigation, app lifecycle, OTA/update risk, mobile performance, storage, offline behavior, deep links, and iOS/Android differences.

## Finding Schema

Specialists return candidate findings in this shape:

```markdown
- severity_recommendation:
- confidence:
- file:
- line:
- issue:
- impact:
- evidence:
- fix_direction:
- verification:
- residual_risk:
```

The coordinator converts candidate findings into the final output format after verification and severity calibration.

## Decision Model

The default threshold is balanced.

The coordinator requests changes only for credible, evidence-backed issues that materially threaten correctness, security, data integrity, deployability, maintainability, or user-facing behavior.

Uncertain risks are not promoted into findings unless the reviewer can point to a concrete failure mode. Otherwise they go in `Residual Risk`.

Severity labels:

- `blocking`: high-confidence issue that should stop merge, including security bypass, data loss, broken migration/deploy path, user-visible correctness bug, merge-breaking test gap for a high-risk change, or major maintainability trap.
- `important`: likely issue or serious missing coverage that should be addressed, but may not independently block merge.
- `nit`: minor local consistency or readability issue.
- `suggestion`: optional improvement.
- `learning`: useful explanation that should not imply required action.
- `praise`: specific strong work worth preserving.

Decisions:

- `Approved`: no blocking or important findings.
- `Approved with comments`: only nits, suggestions, learning notes, or praise.
- `Changes requested`: at least one blocking issue, or important issues that materially threaten merge readiness.
- `Needs clarification`: intent or external constraints are too unclear to fairly approve or request changes.

If specialists disagree, the coordinator resolves by evidence and confidence, not by count.

## Error Handling

If GitHub metadata, linked issues, CI, or primary docs are unavailable, continue with local evidence and record the gap.

If a specialist cannot complete, note the missed pass and either continue or mark residual risk depending on how central that pass was.

If posting to GitHub fails, return the draft review content and the failure reason. Do not retry blindly or claim success.

If no findings are found, say so directly and still report what was reviewed, what verification was run, and what residual risk remains.

## Testing And Validation

Validation should be fixture-driven.

Add synthetic fixtures:

- `auth-bypass-pr.md`: verifies appsec/auth is triggered and produces a blocking finding.
- `migration-risk-pr.md`: verifies data/migration review catches destructive or unsafe deploy behavior.
- `frontend-race-pr.md`: verifies frontend runtime review catches stale data, revalidation, hydration, or mutation-state issues.
- `dependency-upgrade-pr.md`: verifies dependency/supply-chain review requires changelog/security review and lockfile consistency.
- `react-native-permission-pr.md`: verifies Expo/mobile review catches permission/config/platform divergence risk.
- `clean-pr.md`: verifies the plugin can approve clean changes without inventing findings.

Validation checklist:

- The public skill can be invoked naturally with "run a WKND code review" language.
- The coordinator infers report, draft, and post modes correctly.
- Posting requires explicit user intent.
- The coordinator loads only relevant references and specialists.
- Specialist outputs follow the structured finding schema.
- The final report follows the canonical order: findings, decision, summary, action items, verification evidence, and residual risk.
- GitHub mode drafts comments by default.
- No review path edits application code.
- Primary-source refresh triggers are present for version-sensitive or security-sensitive changes.

## Maintainer Guidance

Every reference file should include:

- Source snapshot date.
- Primary source links where applicable.
- Scope: when to load the reference.
- Review checklist.
- Common review smells.
- Primary-source refresh triggers.

Keep references concise enough to load selectively. If a reference grows too large, split it by reviewer responsibility rather than by generic technology category.

## Open Implementation Notes

- This workspace currently is not a git repository, so this design can be written locally but cannot be committed here unless the workspace is initialized or moved under a repository.
- The current `agents/openai.yaml` can seed plugin-facing metadata, but a full `.codex-plugin/plugin.json` should be introduced during implementation.
- The final plugin should preserve implicit invocation for natural requests involving WKND code review.
