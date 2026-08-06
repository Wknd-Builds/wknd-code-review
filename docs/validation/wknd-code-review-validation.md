# WKND Code Review Validation

Run a WKND code review against each fixture. Confirm:

- Natural invocation works.
- Relevant references are loaded.
- Relevant specialists are selected.
- Specialist prompt paths resolve under `agents/`.
- Findings use evidence and confidence.
- Default report order is Findings, Decision, Summary, Action Items, Verification Evidence, Residual Risk.
- GitHub mode drafts by default.
- Posting requires explicit user language.
- GitHub draft output contains `Top-Level Review Body` plus `Inline-Ready Comments` with `file`, `line`, `severity`, `confidence`, `issue`, `impact`, `evidence`, `fix_direction`, and `draft_note`.
- Explicit post mode either reports exactly what was posted/submitted or, if posting fails, returns the draft content with the failure reason.
- No review path edits application code.

## Fixture Expectations

| Fixture | Routing expectation | Decision expectation |
| --- | --- | --- |
| `fixtures/auth-bypass-pr.md` | Routes appsec/auth and test strategy. | Changes requested with blocking file/line evidence for trusting request-supplied identity or role. |
| `fixtures/migration-risk-pr.md` | Routes data/migrations, ci/deployment, and observability. | Changes requested for destructive migration, missing backfill/constraint/index, or deploy-order risk. |
| `fixtures/frontend-race-pr.md` | Routes frontend runtime and test strategy; routes performance only if payload/render risk is evidenced. | Changes requested or Approved with comments depending on duplicate-submit, rollback, and revalidation evidence. |
| `fixtures/dependency-upgrade-pr.md` | Routes dependency/supply-chain, ci/deployment, and test strategy. | Needs clarification or Changes requested when primary changelog/security or engine compatibility evidence is missing. |
| `fixtures/react-native-permission-pr.md` | Routes React Native/Expo and appsec/auth for privacy-sensitive location data. | Changes requested for permission copy, platform divergence, and native capability/OTA risk. |
| `fixtures/clean-pr.md` | Routes only locally relevant specialists. | Approved without invented findings; residual risk is short. |

Canonical report order: Findings, Decision, Summary, Action Items, Verification Evidence, Residual Risk.

## Validation Results

Date: 2026-08-06

### Mechanical Checks

| Check | Result | Evidence |
| --- | --- | --- |
| File inventory | Passed | Sorted `find .codex-plugin agents references fixtures docs -maxdepth 3 -type f` output showed the plugin manifest, coordinator, 10 specialist reviewers, reference library, fixtures, spec, plan, and validation doc. |
| Public skill count | Passed | `find . -name 'SKILL.md' -print` returned only `./SKILL.md`. |
| Natural invocation metadata | Passed | `agents/openai.yaml` includes `allow_implicit_invocation: true` and a default prompt starting with "Run a WKND code review"; `SKILL.md` defines report, draft, and explicit-post modes. |
| Specialist schemas | Passed | Every `agents/*-reviewer.md` file includes `severity_recommendation`, `confidence`, `evidence`, `fix_direction`, and `residual_risk`. |
| Canonical report order | Passed | `SKILL.md`, `references/output-modes.md`, and this validation doc use Findings, Decision, Summary, Action Items, Verification Evidence, Residual Risk. |
| Risk labels | Passed | `agents/coordinator.md` and `references/review-context-gathering.md` preserve public API behavior and concurrency/retries/idempotency routing guidance. |

### Fixture Dry-Runs

| Fixture | Mode | Routed specialists | Expected outcome confirmed? | Notes |
| --- | --- | --- | --- | --- |
| `fixtures/auth-bypass-pr.md` | report only | AppSec/auth; test strategy | Yes | Pseudo-diff trusts request-supplied `userId`, `tenantId`, and `role` after only requiring a session, matching appsec/auth smells and supporting a blocking Changes requested result with file/line evidence. Test change proves the bypass instead of denied access, matching test strategy risk. |
| `fixtures/migration-risk-pr.md` | report only | Data/migrations; CI/deployment; observability | Yes | Pseudo-diff drops existing visibility columns, adds a new non-null text column without backfill, constraint, or index, and moves migration after deploy. This matches destructive migration, deploy-order, and failure-visibility routing and supports Changes requested. |
| `fixtures/frontend-race-pr.md` | report only | Frontend runtime; test strategy | Yes | Optimistic removal plus skipped revalidation and a double-click test route to frontend runtime and test strategy for duplicate submit, rollback, and revalidation coverage. Performance remains optional because no payload, bundle, or render-scale evidence is present. |
| `fixtures/dependency-upgrade-pr.md` | report only | Dependency/supply-chain; CI/deployment; test strategy | Yes | Major Next.js upgrade, Node engine bump, and a new package with an install script route to dependency, CI/deployment, and test strategy. Version-sensitive dependency and framework behavior requires primary-source refresh before final judgment, supporting Needs clarification or Changes requested when that evidence is unavailable. |
| `fixtures/react-native-permission-pr.md` | report only | React Native/Expo; AppSec/auth | Yes | Background location request, incomplete iOS copy, Android permission divergence, high-accuracy background updates, and EAS release-channel/native capability risk match React Native/Expo and privacy-sensitive appsec/auth routing. Expected Changes requested outcome is supported. |
| `fixtures/clean-pr.md` | report only | Frontend runtime; test strategy | Yes | Copy-only empty-state change with matching test update routes only to locally relevant UI/test review. No concrete failure mode is evidenced, so Approved with short residual risk is confirmed. |

## Manual Steps

1. Invoke the plugin naturally, for example: "Run a WKND code review on `fixtures/auth-bypass-pr.md`."
2. Repeat for every fixture in `fixtures/`.
3. Record whether loaded references match the routed specialists.
4. Confirm each finding cites concrete evidence and states confidence.
5. Confirm the merge decision matches the fixture expectation or explains a justified exception.
6. Confirm GitHub draft mode does not post unless the user explicitly asks to post.
7. Invoke an explicit post request, for example: "Run a WKND code review on `fixtures/auth-bypass-pr.md` and post your findings." Confirm it either reports exactly what was posted/submitted or returns the draft review content with the posting failure reason.
8. Confirm the review does not modify application code or fixture files.
