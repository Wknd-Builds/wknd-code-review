# WKND Code Review Plugin Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Convert `wknd-code-review` into a full Codex plugin bundle with one public WKND review invocation, a coordinator workflow, narrow specialist reviewers, expanded references, GitHub-ready output modes, and fixture-based validation.

**Architecture:** Keep `SKILL.md` as the only public skill and move operational rigor into `agents/coordinator.md`. Add narrow specialist prompts under `agents/`, curated review references under `references/`, synthetic review fixtures under `fixtures/`, and `.codex-plugin/plugin.json` for plugin packaging. The review workflow remains non-mutating: it may inspect, draft findings, and propose remediation sketches, but it must not edit reviewed application code.

**Tech Stack:** Codex plugin manifest JSON, Markdown skills/prompts/references, YAML agent metadata, Git, shell validation with `rg`, `find`, and `git`.

---

## Prerequisites

- [ ] **Step 1: Confirm the workspace is a git repository**

Run: `git rev-parse --show-toplevel`

Expected: `/Users/wesleychang/projects/wknd-code-review`

If this fails, do not run the commit steps. Continue implementation with file edits and validation, then report that commits were skipped because the workspace is not a git repository.

- [ ] **Step 2: Confirm the working tree baseline**

Run: `git status --short`

Expected: clean or only known user changes. Do not revert unrelated user changes.

---

## File Structure

Create:

- `.codex-plugin/plugin.json`: plugin manifest and discoverability metadata.
- `agents/coordinator.md`: main review coordinator workflow, routing matrix, context packet, output mode inference, final decision rules.
- `agents/appsec-auth-reviewer.md`: security, auth, tenant, privacy, and trust-boundary specialist.
- `agents/data-migrations-reviewer.md`: database, Prisma/Postgres, migration, transaction, data-loss specialist.
- `agents/test-strategy-reviewer.md`: test adequacy and regression-risk specialist.
- `agents/frontend-runtime-reviewer.md`: React, React Router/Remix, Next.js runtime specialist.
- `agents/accessibility-reviewer.md`: a11y specialist.
- `agents/performance-reviewer.md`: backend/frontend/mobile performance specialist.
- `agents/dependency-supply-chain-reviewer.md`: dependency, lockfile, supply-chain specialist.
- `agents/ci-deployment-reviewer.md`: CI, Docker, deployment, release safety specialist.
- `agents/observability-reviewer.md`: logs, metrics, tracing, alertability specialist.
- `agents/react-native-expo-reviewer.md`: React Native/Expo mobile specialist.
- `references/review-decision-model.md`: severity and decision calibration.
- `references/review-context-gathering.md`: context packet checklist and local-convention gathering.
- `references/output-modes.md`: report, GitHub draft, and explicit-post output contracts.
- `references/github-posting.md`: GitHub draft/posting guardrails.
- `references/nodejs-typescript.md`: expanded Node.js/TypeScript review reference.
- `references/react-router-remix.md`: renamed/expanded React Router and Remix-style reference.
- `references/react-next.md`: React and Next.js review reference.
- `references/prisma-postgres.md`: Prisma/Postgres review reference.
- `references/auth-sessions.md`: auth and session review reference.
- `references/testing.md`: test strategy reference.
- `references/ci-deployment.md`: CI/deployment reference.
- `references/accessibility.md`: accessibility reference.
- `references/browser-performance.md`: browser/frontend performance reference.
- `references/observability.md`: observability reference.
- `references/dependency-security.md`: dependency/security reference.
- `references/react-native-expo.md`: React Native/Expo reference.
- `fixtures/auth-bypass-pr.md`: synthetic fixture for security blocking finding.
- `fixtures/migration-risk-pr.md`: synthetic fixture for migration/deploy finding.
- `fixtures/frontend-race-pr.md`: synthetic fixture for frontend runtime finding.
- `fixtures/dependency-upgrade-pr.md`: synthetic fixture for dependency/supply-chain finding.
- `fixtures/react-native-permission-pr.md`: synthetic fixture for Expo/mobile finding.
- `fixtures/clean-pr.md`: synthetic fixture for clean approval.
- `fixtures/README.md`: explains how to use fixtures for manual validation.
- `docs/validation/wknd-code-review-validation.md`: validation checklist and expected fixture outcomes.

Modify:

- `SKILL.md`: replace compact skill with public WKND review contract and loading rules.
- `agents/openai.yaml`: update default prompt and metadata to reflect one public invocation with report/draft/post modes.
- `references/nodejs-review.md`: either replace with a short compatibility redirect to `nodejs-typescript.md` or remove after all references are updated.
- `references/react-router-framework-review.md`: either replace with a short compatibility redirect to `react-router-remix.md` or remove after all references are updated.

Do not modify:

- `docs/superpowers/specs/2026-08-06-wknd-code-review-plugin-design.md` unless implementation reveals a real spec error.

---

### Task 1: Add Plugin Manifest And Preserve Natural Invocation

**Files:**
- Create: `.codex-plugin/plugin.json`
- Modify: `agents/openai.yaml`

- [ ] **Step 1: Write manifest skeleton**

Create `.codex-plugin/plugin.json` with metadata that identifies one public WKND review plugin. Use conservative fields that mirror common Codex plugin manifests:

```json
{
  "id": "wknd-code-review",
  "name": "WKND Code Review",
  "version": "0.1.0",
  "description": "High-signal production code review workflow with coordinator-led specialist review.",
  "skills": [
    {
      "path": "SKILL.md"
    }
  ],
  "agents": {
    "openai": "agents/openai.yaml"
  }
}
```

If local plugin manifest conventions differ, adjust only enough to match the installed plugin schema and keep one public `SKILL.md`.

- [ ] **Step 2: Update agent metadata**

Update `agents/openai.yaml` so `default_prompt` explains natural invocation and mode inference:

```yaml
interface:
  display_name: "WKND Code Review"
  short_description: "Coordinator-led production PR review"
  default_prompt: "Run a WKND code review. Infer whether to report, draft GitHub comments, or post findings from the user's explicit request. Use project context, specialist review, balanced severity calibration, and a merge decision."

policy:
  allow_implicit_invocation: true
```

- [ ] **Step 3: Validate manifest and metadata are present**

Run: `find .codex-plugin agents -maxdepth 2 -type f | sort`

Expected: includes `.codex-plugin/plugin.json` and `agents/openai.yaml`.

- [ ] **Step 4: Commit**

```bash
git add .codex-plugin/plugin.json agents/openai.yaml
git commit -m "feat: add wknd code review plugin manifest"
```

---

### Task 2: Create Validation Fixtures Before Rewriting The Skill

**Files:**
- Create: `fixtures/README.md`
- Create: `fixtures/auth-bypass-pr.md`
- Create: `fixtures/migration-risk-pr.md`
- Create: `fixtures/frontend-race-pr.md`
- Create: `fixtures/dependency-upgrade-pr.md`
- Create: `fixtures/react-native-permission-pr.md`
- Create: `fixtures/clean-pr.md`
- Create: `docs/validation/wknd-code-review-validation.md`

- [ ] **Step 1: Write fixture README**

Create `fixtures/README.md` explaining that these are synthetic PR packets used to validate routing and decision behavior. Include this expected outcome table:

```markdown
| Fixture | Required specialist routing | Expected decision |
| --- | --- | --- |
| `auth-bypass-pr.md` | appsec/auth, test strategy | Changes requested |
| `migration-risk-pr.md` | data/migrations, ci/deployment, observability | Changes requested |
| `frontend-race-pr.md` | frontend runtime, test strategy, performance if payload/render risk appears | Changes requested or Approved with comments depending on evidence |
| `dependency-upgrade-pr.md` | dependency/supply-chain, ci/deployment, test strategy | Needs clarification or Changes requested if changelog/security evidence is missing |
| `react-native-permission-pr.md` | React Native/Expo, appsec/auth if permissions affect privacy | Changes requested |
| `clean-pr.md` | only locally relevant specialists | Approved |
```

- [ ] **Step 2: Write security fixture**

Create `fixtures/auth-bypass-pr.md` as a concise PR packet with title, intent, changed files, and pseudo-diff. The pseudo-diff should include a server mutation that trusts `userId`, `tenantId`, or `role` from request body/form data.

Expected validation:

- AppSec/auth specialist is routed.
- Test strategy specialist is routed.
- Final output includes a blocking finding with file/line-style evidence.

- [ ] **Step 3: Write migration fixture**

Create `fixtures/migration-risk-pr.md` showing a destructive migration, missing backfill, missing index/constraint concern, or deploy-order risk.

Expected validation:

- Data/migrations specialist is routed.
- CI/deployment specialist is routed.
- Observability specialist is routed if failure visibility is part of the risk.

- [ ] **Step 4: Write frontend race fixture**

Create `fixtures/frontend-race-pr.md` showing React Router/Remix or Next.js mutation UI with stale revalidation, duplicate submit, optimistic update rollback, or hydration risk.

Expected validation:

- Frontend runtime specialist is routed.
- Test strategy specialist checks user-flow coverage.

- [ ] **Step 5: Write dependency fixture**

Create `fixtures/dependency-upgrade-pr.md` showing package and lockfile changes with missing changelog, lifecycle script, engine mismatch, or major-version framework upgrade.

Expected validation:

- Dependency/supply-chain specialist is routed.
- Primary-source refresh trigger is required.

- [ ] **Step 6: Write React Native/Expo fixture**

Create `fixtures/react-native-permission-pr.md` showing Expo config or permission changes with iOS/Android divergence, missing user-facing permission copy, OTA risk, or native capability implications.

Expected validation:

- React Native/Expo specialist is routed.
- AppSec/auth specialist is routed if privacy-sensitive permission data is involved.

- [ ] **Step 7: Write clean fixture**

Create `fixtures/clean-pr.md` showing a small low-risk refactor or well-tested UI copy/state change.

Expected validation:

- Plugin can approve without inventing findings.
- Residual risk stays honest and short.

- [ ] **Step 8: Write validation checklist**

Create `docs/validation/wknd-code-review-validation.md` with fixture expectations and manual validation steps:

```markdown
Run a WKND code review against each fixture. Confirm:

- Natural invocation works.
- Relevant references are loaded.
- Relevant specialists are selected.
- Findings use evidence and confidence.
- Default report order is findings, decision, summary, action items, verification evidence, residual risk.
- GitHub mode drafts by default.
- Posting requires explicit user language.
- No review path edits application code.
```

- [ ] **Step 9: Validate fixture paths**

Run: `find fixtures docs/validation -maxdepth 2 -type f | sort`

Expected: all six fixtures, `fixtures/README.md`, and `docs/validation/wknd-code-review-validation.md`.

- [ ] **Step 10: Commit**

```bash
git add fixtures docs/validation/wknd-code-review-validation.md
git commit -m "test: add wknd code review validation fixtures"
```

---

### Task 3: Rewrite Public Skill As Single Invocation Surface

**Files:**
- Modify: `SKILL.md`

- [ ] **Step 1: Replace the public skill contract**

Rewrite `SKILL.md` to keep the existing frontmatter name but broaden the description:

```markdown
---
name: wknd-code-review
description: Use when reviewing pull requests, diffs, commits, code changes, GitHub PRs, linked issues, merge readiness, security, data/migration risk, frontend behavior, React Native/Expo changes, CI/deploy risk, observability, dependencies, or production readiness.
---
```

- [ ] **Step 2: Add non-mutating review contract**

Add a top-level rule:

```markdown
WKND Code Review is a review workflow. Do not edit the reviewed application code during the review. You may inspect, run safe verification commands when appropriate, draft comments, and propose remediation sketches. Implement fixes only when the user makes a separate implementation request.
```

- [ ] **Step 3: Add mode inference rules**

Document:

- Default is `report only`.
- Draft GitHub comments when requested without post/submit/publish language.
- Post only when the user explicitly asks to post, submit, or publish.
- Ambiguous wording stays draft-only.

- [ ] **Step 4: Add required loading sequence**

Tell the reviewer to read:

1. `agents/coordinator.md`.
2. `references/review-context-gathering.md`.
3. `references/review-decision-model.md`.
4. `references/output-modes.md`.
5. Relevant stack and specialist references based on the risk map.

- [ ] **Step 5: Add output order**

Canonical report order:

1. Findings.
2. Decision.
3. Summary.
4. Action Items.
5. Verification Evidence.
6. Residual Risk.

- [ ] **Step 6: Validate public skill has key phrases**

Run:

```bash
rg -n "Do not edit|report only|post GitHub|agents/coordinator.md|Findings" SKILL.md
```

Expected: each phrase is present.

- [ ] **Step 7: Commit**

```bash
git add SKILL.md
git commit -m "feat: define single wknd code review entrypoint"
```

---

### Task 4: Implement Coordinator Prompt

**Files:**
- Create: `agents/coordinator.md`

- [ ] **Step 1: Define coordinator responsibility**

Create `agents/coordinator.md` with these sections:

- Purpose.
- Non-mutating review boundary.
- Intent detection.
- Context packet.
- Risk map.
- Specialist routing matrix.
- Specialist packet format.
- Finding reconciliation.
- Decision model handoff.
- Output mode handoff.
- Failure handling.

- [ ] **Step 2: Add context packet schema**

Include this schema:

```markdown
## Review Packet

- user_request:
- mode:
- base_head_or_pr:
- title_body_intent:
- changed_files:
- linked_issues_docs:
- relevant_local_context:
- tests_and_ci:
- data_schema_migrations:
- dependency_runtime_changes:
- risk_map:
- unavailable_context:
```

- [ ] **Step 3: Add specialist packet schema**

Include:

```markdown
## Specialist Packet

- intent_summary:
- changed_files_or_hunks:
- surrounding_context:
- local_conventions:
- specific_questions:
- expected_output_schema:
```

- [ ] **Step 4: Add routing matrix**

Map changed areas to specialists:

- Auth/session/tenant/PII/webhook/file upload/redirect -> `appsec-auth-reviewer.md`.
- Schema/migration/query/Prisma/Postgres/backfill -> `data-migrations-reviewer.md`.
- Public API behavior/contract/serialization/backward compatibility -> coordinator loads `references/nodejs-typescript.md` or framework reference, then routes to `test-strategy-reviewer.md`, `appsec-auth-reviewer.md`, or `data-migrations-reviewer.md` when the API touches trust, auth, or persistence.
- Test files or high-risk untested behavior -> `test-strategy-reviewer.md`.
- React/React Router/Remix/Next.js/client-server boundary -> `frontend-runtime-reviewer.md`.
- Concurrency/retries/idempotency/duplicate submits/stale writes/background jobs -> `data-migrations-reviewer.md`, `frontend-runtime-reviewer.md`, `test-strategy-reviewer.md`, and `observability-reviewer.md` depending on where the risk occurs.
- UI forms/dialogs/focus/semantic changes -> `accessibility-reviewer.md`.
- Query/runtime/render/bundle/network/mobile performance -> `performance-reviewer.md`.
- Package/lockfile/engine/build scripts -> `dependency-supply-chain-reviewer.md`.
- CI/Docker/env/deploy/release migration order -> `ci-deployment-reviewer.md`.
- Logging/metrics/tracing/background failure visibility -> `observability-reviewer.md`.
- Expo/native config/permissions/mobile lifecycle/navigation/storage -> `react-native-expo-reviewer.md`.

- [ ] **Step 5: Add reconciliation rules**

Require coordinator to verify claims, deduplicate, resolve disagreements by evidence/confidence, and avoid promoting uncertain risks into findings.

- [ ] **Step 6: Validate coordinator contract**

Run:

```bash
rg -n "Review Packet|Specialist Packet|routing|verify|evidence|confidence|non-mutating" agents/coordinator.md
```

Expected: every concept appears.

- [ ] **Step 7: Commit**

```bash
git add agents/coordinator.md
git commit -m "feat: add review coordinator prompt"
```

---

### Task 5: Add Specialist Reviewer Prompts

**Files:**
- Create: `agents/appsec-auth-reviewer.md`
- Create: `agents/data-migrations-reviewer.md`
- Create: `agents/test-strategy-reviewer.md`
- Create: `agents/frontend-runtime-reviewer.md`
- Create: `agents/accessibility-reviewer.md`
- Create: `agents/performance-reviewer.md`
- Create: `agents/dependency-supply-chain-reviewer.md`
- Create: `agents/ci-deployment-reviewer.md`
- Create: `agents/observability-reviewer.md`
- Create: `agents/react-native-expo-reviewer.md`

- [ ] **Step 1: Create shared specialist template**

Each file should follow this shape:

```markdown
# [Specialist Name]

## Scope

[Narrow scope.]

## Inputs

Use only the coordinator-provided specialist packet plus explicitly referenced local context.

## Review Method

- Confirm what changed and why it matters.
- Trace the relevant risk across boundaries.
- Check local conventions before applying generic preferences.
- Return no finding when evidence does not support a concrete issue.

## Finding Output

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

## Review Smells

[Specialist-specific smells.]
```

- [ ] **Step 2: Write appsec/auth specialist**

Include scope from the spec and route to `references/auth-sessions.md`, `references/dependency-security.md`, and framework references when relevant.

- [ ] **Step 3: Write data/migrations specialist**

Include Prisma/Postgres, schema, migration, index, backfill, rollback, transaction, query, and deploy-order concerns. Route to `references/prisma-postgres.md`.

- [ ] **Step 4: Write test strategy specialist**

Include test intent, risk-proportional coverage, realistic integration boundaries, failure paths, and local style. Route to `references/testing.md`.

- [ ] **Step 5: Write frontend runtime specialist**

Include React, React Router/Remix, Next.js, hydration, server/client boundaries, stale data, forms, actions, fetchers, race conditions, and error boundaries.

- [ ] **Step 6: Write accessibility specialist**

Include keyboard, focus, labels, semantics, forms, dialogs, dynamic content, disabled/loading states, and screen-reader-visible errors.

- [ ] **Step 7: Write performance specialist**

Include backend query/runtime and frontend/mobile runtime concerns.

- [ ] **Step 8: Write dependency/supply-chain specialist**

Include package, lockfile, engine, script, transitive risk, vulnerability, changelog, and build tooling concerns.

- [ ] **Step 9: Write CI/deployment specialist**

Include CI coverage, Docker, env vars, secrets, deploy ordering, migrations, rollback, and release safety.

- [ ] **Step 10: Write observability specialist**

Include logs, metrics, traces, error reporting, alertability, background job visibility, and privacy-safe diagnostics.

- [ ] **Step 11: Write React Native/Expo specialist**

Include Expo config, native permissions, app lifecycle, OTA/update risk, platform divergence, navigation, storage, offline behavior, deep links, and mobile performance.

- [ ] **Step 12: Validate all specialist files share schema**

Run:

```bash
for f in agents/*-reviewer.md; do echo "$f"; rg -n "severity_recommendation|confidence|fix_direction|residual_risk" "$f"; done
```

Expected: every specialist file includes the finding schema fields.

- [ ] **Step 13: Commit**

```bash
git add agents/*-reviewer.md
git commit -m "feat: add specialist review prompts"
```

---

### Task 6: Add Core Workflow References

**Files:**
- Create: `references/review-decision-model.md`
- Create: `references/review-context-gathering.md`
- Create: `references/output-modes.md`
- Create: `references/github-posting.md`

- [ ] **Step 1: Write decision model reference**

Create `references/review-decision-model.md` with:

- Balanced default threshold.
- Severity labels.
- Decision definitions.
- Evidence and confidence rules.
- When to use residual risk.
- Praise/nit/suggestion guidance.

- [ ] **Step 2: Write context gathering reference**

Create `references/review-context-gathering.md` with:

- Base/head or PR metadata.
- PR title/body and linked issues.
- Changed files and changed hunks.
- Nearby code and local conventions.
- Tests and CI.
- Schema/migration/package/runtime changes.
- Unavailable context handling.

- [ ] **Step 3: Write output modes reference**

Create `references/output-modes.md` with canonical report order:

1. Findings.
2. Decision.
3. Summary.
4. Action Items.
5. Verification Evidence.
6. Residual Risk.

Also include GitHub draft shape, no-findings shape, and this exact inline-ready comment contract:

```markdown
## GitHub Draft Output

### Top-Level Review Body

[Concise review body with decision, scope, high-level findings summary, verification evidence, and residual risk.]

### Inline-Ready Comments

- file:
- line:
- severity:
- issue:
- impact:
- fix_direction:
- draft_note:
```

The `draft_note` must clearly state that the comment has not been posted unless the user explicitly requested posting.

- [ ] **Step 4: Write GitHub posting reference**

Create `references/github-posting.md` with:

- Draft-by-default rule.
- Explicit post/submit/publish trigger.
- Prohibition on posting ambiguous drafts.
- Fallback behavior when GitHub tooling is unavailable or posting fails.
- Requirement to report exactly what was posted.

- [ ] **Step 5: Validate core references**

Run:

```bash
rg -n "balanced|Residual Risk|Findings|explicit|unavailable" references/review-decision-model.md references/review-context-gathering.md references/output-modes.md references/github-posting.md
```

Expected: key rules appear in the relevant files.

- [ ] **Step 6: Commit**

```bash
git add references/review-decision-model.md references/review-context-gathering.md references/output-modes.md references/github-posting.md
git commit -m "feat: add core review workflow references"
```

---

### Task 7: Expand Stack And Domain References

**Files:**
- Create: `references/nodejs-typescript.md`
- Create: `references/react-router-remix.md`
- Create: `references/react-next.md`
- Create: `references/prisma-postgres.md`
- Create: `references/auth-sessions.md`
- Create: `references/testing.md`
- Create: `references/ci-deployment.md`
- Create: `references/accessibility.md`
- Create: `references/browser-performance.md`
- Create: `references/observability.md`
- Create: `references/dependency-security.md`
- Create: `references/react-native-expo.md`
- Modify: `references/nodejs-review.md`
- Modify: `references/react-router-framework-review.md`

- [ ] **Step 1: Establish reference file template**

Every new reference should include:

```markdown
# [Reference Name]

Source snapshot: 2026-08-06.

Primary sources:
- [Official/primary source link]

Use this reference when [...]

## Review Checklist

## Common Review Smells

## Primary-Source Refresh Triggers
```

Replace `[Official/primary source link]` and `Use this reference when [...]` with concrete links and concrete loading guidance in every reference. Do not leave template placeholders in committed reference files.

- [ ] **Step 2: Migrate Node.js content**

Create `references/nodejs-typescript.md` by expanding the existing `references/nodejs-review.md`. Preserve current architecture, error handling, security, performance, Docker/deployment, and testing guidance. Add TypeScript-specific review concerns for broad casts, `any`, assertion-heavy code, runtime validation gaps, and config typing.

- [ ] **Step 3: Migrate React Router content**

Create `references/react-router-remix.md` by expanding the existing `references/react-router-framework-review.md`. Preserve loader/action, form, revalidation, route module, race condition, testing, security, and error-boundary guidance.

- [ ] **Step 4: Add React/Next reference**

Create `references/react-next.md` covering React component boundaries, hooks, state synchronization, server/client component boundaries, Next.js route handlers/actions, caching/revalidation, hydration, serialization, redirects, metadata, and tests.

- [ ] **Step 5: Add Prisma/Postgres reference**

Create `references/prisma-postgres.md` covering migrations, schema drift, constraints, indexes, data loss, backfills, transactions, query shape, N+1, pagination, isolation, rollback, and deploy order.

- [ ] **Step 6: Add auth/session reference**

Create `references/auth-sessions.md` covering authn/authz, session cookies, CSRF, tenant isolation, OAuth/callbacks, redirects, password/MFA flows, secrets, PII, ownership, and server-derived identity.

- [ ] **Step 7: Add testing reference**

Create `references/testing.md` covering risk-proportional tests, intent-first naming, integration boundaries, failure modes, fixtures, race conditions, migration tests, route tests, mobile tests, CI confidence, and avoiding brittle mocks.

- [ ] **Step 8: Add CI/deployment reference**

Create `references/ci-deployment.md` covering CI jobs, cache behavior, Docker, env vars, secrets, migration ordering, release/rollback, build artifacts, runtime versions, and production safety.

- [ ] **Step 9: Add accessibility reference**

Create `references/accessibility.md` covering semantic HTML, labels, keyboard behavior, focus, forms, dialogs, dynamic content, errors, loading/disabled states, and mobile accessibility basics.

- [ ] **Step 10: Add browser performance reference**

Create `references/browser-performance.md` covering bundle size, waterfalls, render churn, memoization misuse, images/assets, Core Web Vitals concepts, hydration cost, caching, and payload shape.

- [ ] **Step 11: Add observability reference**

Create `references/observability.md` covering logs, metrics, traces, alertability, background tasks, privacy-safe diagnostics, error context, correlation IDs, and production debugging.

- [ ] **Step 12: Add dependency/security reference**

Create `references/dependency-security.md` covering package changes, lockfiles, engines, scripts, transitive risk, supply chain, vulnerabilities, changelogs, major versions, and risky build tooling.

- [ ] **Step 13: Add React Native/Expo reference**

Create `references/react-native-expo.md` covering Expo config, native permissions, app lifecycle, OTA/update safety, platform divergence, navigation, storage, offline behavior, deep links, native module risk, and mobile performance.

- [ ] **Step 14: Add compatibility redirects**

Replace `references/nodejs-review.md` with a short pointer:

```markdown
# Node.js Review Reference

This reference has moved to `references/nodejs-typescript.md`.
```

Replace `references/react-router-framework-review.md` with:

```markdown
# React Router Framework Mode Review Reference

This reference has moved to `references/react-router-remix.md`.
```

- [ ] **Step 15: Validate reference metadata**

Run:

```bash
for f in references/*.md; do echo "$f"; rg -n "Source snapshot|Use this reference|Primary-Source Refresh Triggers|moved to" "$f"; done
```

Expected: each active reference includes the template fields; compatibility files include `moved to`.

- [ ] **Step 16: Commit**

```bash
git add references
git commit -m "feat: expand wknd review reference library"
```

---

### Task 8: Wire Skill, Coordinator, References, And Fixtures Together

**Files:**
- Modify: `SKILL.md`
- Modify: `agents/coordinator.md`
- Modify: `fixtures/README.md`
- Modify: `docs/validation/wknd-code-review-validation.md`

- [ ] **Step 1: Cross-check all referenced files exist**

Run:

```bash
rg -o "([a-zA-Z0-9_./-]+\\.md)" SKILL.md agents/coordinator.md fixtures/README.md docs/validation/wknd-code-review-validation.md
```

Expected: every listed path exists or is intentionally an example.

- [ ] **Step 2: Add missing path references**

If `SKILL.md` or `agents/coordinator.md` omits a new reference or specialist path, add it.

- [ ] **Step 3: Confirm no stale old-reference loading remains**

Run:

```bash
rg -n "nodejs-review|react-router-framework-review" SKILL.md agents references fixtures docs
```

Expected: only compatibility redirect files mention old names, unless migration notes are intentionally kept.

- [ ] **Step 4: Validate posting guardrails**

Run:

```bash
rg -n "explicit|post|submit|publish|draft-only|ambiguous" SKILL.md agents/coordinator.md references/github-posting.md references/output-modes.md
```

Expected: posting requires explicit user intent in all relevant workflow files.

- [ ] **Step 5: Validate GitHub draft fields**

Run:

```bash
rg -n "Top-Level Review Body|Inline-Ready Comments|file:|line:|severity:|issue:|impact:|fix_direction:|draft_note:" references/output-modes.md references/github-posting.md SKILL.md agents/coordinator.md
```

Expected: the GitHub draft contract includes top-level review body plus inline-ready comments with file, line, severity, issue, impact, fix direction, and draft note fields.

- [ ] **Step 6: Commit**

```bash
git add SKILL.md agents/coordinator.md fixtures/README.md docs/validation/wknd-code-review-validation.md
git commit -m "chore: wire wknd review workflow docs"
```

---

### Task 9: Run Full Validation Pass

**Files:**
- Read: all plugin files.
- Modify only if validation finds a real mismatch.

- [ ] **Step 1: Verify expected file tree**

Run:

```bash
find .codex-plugin agents references fixtures docs -maxdepth 3 -type f | sort
```

Expected: manifest, coordinator, 10 specialist reviewers, reference library, fixtures, spec, plan, and validation doc are present.

- [ ] **Step 2: Verify one public skill**

Run:

```bash
find . -name 'SKILL.md' -print
```

Expected: only `./SKILL.md`.

- [ ] **Step 3: Verify natural invocation metadata**

Run:

```bash
rg -n "allow_implicit_invocation|Run a WKND code review|mode|post" agents/openai.yaml SKILL.md
```

Expected: implicit invocation enabled and mode inference described.

- [ ] **Step 4: Verify specialist schemas**

Run:

```bash
for f in agents/*-reviewer.md; do echo "$f"; rg -n "severity_recommendation|confidence|evidence|fix_direction|residual_risk" "$f"; done
```

Expected: every specialist has all schema fields.

- [ ] **Step 5: Verify canonical report order**

Run:

```bash
rg -n "Findings.*Decision|canonical order|Verification Evidence|Residual Risk" SKILL.md references/output-modes.md docs/validation/wknd-code-review-validation.md
```

Expected: canonical order is consistent across files.

- [ ] **Step 6: Verify complete risk labels**

Run:

```bash
rg -n "Public API behavior|Concurrency and retries|public API|concurrency|retries|idempotency" agents/coordinator.md references/review-context-gathering.md
```

Expected: coordinator and context-gathering guidance preserve the spec risk labels for public API behavior and concurrency/retries.

- [ ] **Step 7: Manually dry-run each fixture**

For each fixture in `fixtures/*.md`, read the fixture and trace expected behavior against `SKILL.md`, `agents/coordinator.md`, and relevant specialist/reference files.

Record results in `docs/validation/wknd-code-review-validation.md` under a `Validation Results` section:

```markdown
## Validation Results

Date: 2026-08-06

| Fixture | Mode | Routed specialists | Expected outcome confirmed? | Notes |
| --- | --- | --- | --- | --- |
```

- [ ] **Step 8: Fix validation mismatches**

If a fixture expectation, routing rule, or output contract conflicts with the implemented docs, update the smallest set of files needed.

- [ ] **Step 9: Commit validation results**

```bash
git add docs/validation/wknd-code-review-validation.md SKILL.md agents references fixtures .codex-plugin
git commit -m "test: validate wknd code review plugin workflow"
```

If no files changed after validation, skip the commit and note that validation produced no changes.

---

### Task 10: Final Review And Handoff

**Files:**
- Read: `git status`, `git log`, key plugin files.
- Modify: none unless final review finds a defect.

- [ ] **Step 1: Check working tree**

Run: `git status --short`

Expected: clean, unless validation intentionally left uncommitted notes.

- [ ] **Step 2: Inspect commit history**

Run: `git log --oneline -8`

Expected: commits for manifest, fixtures, public skill, coordinator, specialists, references, wiring, and validation where applicable.

- [ ] **Step 3: Review final invocation path**

Read `SKILL.md`, `agents/coordinator.md`, and `agents/openai.yaml` together.

Confirm:

- One public invocation surface.
- Balanced decision model.
- Non-mutating review contract.
- Specialist routing.
- Draft-by-default GitHub behavior.
- Explicit-only posting.

- [ ] **Step 4: Final response**

Summarize:

- Files changed.
- Validation performed.
- Any residual risks, especially manifest schema uncertainty if not fully verifiable locally.
- Suggested next step, such as installing the plugin locally or running it against a real PR.
