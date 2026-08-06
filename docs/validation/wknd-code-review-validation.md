# WKND Code Review Validation

Run a WKND code review against each fixture. Confirm:

- Natural invocation works.
- Relevant references are loaded.
- Relevant specialists are selected.
- Specialist prompt paths resolve under `agents/`.
- Findings use evidence and confidence.
- Default report order is findings, decision, summary, action items, verification evidence, residual risk.
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

## Manual Steps

1. Invoke the plugin naturally, for example: "Run a WKND code review on `fixtures/auth-bypass-pr.md`."
2. Repeat for every fixture in `fixtures/`.
3. Record whether loaded references match the routed specialists.
4. Confirm each finding cites concrete evidence and states confidence.
5. Confirm the merge decision matches the fixture expectation or explains a justified exception.
6. Confirm GitHub draft mode does not post unless the user explicitly asks to post.
7. Invoke an explicit post request, for example: "Run a WKND code review on `fixtures/auth-bypass-pr.md` and post your findings." Confirm it either reports exactly what was posted/submitted or returns the draft review content with the posting failure reason.
8. Confirm the review does not modify application code or fixture files.
