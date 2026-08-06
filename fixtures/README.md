# Validation Fixtures

These synthetic PR packets validate WKND code review routing and decision behavior. They are intentionally small, but each one includes enough changed-file and pseudo-diff context for the coordinator to select specialists, cite evidence, and make a calibrated merge decision.

Use them as manual review inputs before and after skill rewrites. The review workflow must inspect the packet, route only relevant specialists, and avoid editing any reviewed application code.

Fixture paths below are relative to this directory. When invoking from the repository root, use the `fixtures/...` path shown in docs validation, such as `fixtures/auth-bypass-pr.md`.

| Fixture | Required specialist routing | Expected decision |
| --- | --- | --- |
| `auth-bypass-pr.md` | appsec/auth, test strategy | Changes requested |
| `migration-risk-pr.md` | data/migrations, ci/deployment, observability | Changes requested |
| `frontend-race-pr.md` | frontend runtime, test strategy, performance if payload/render risk appears | Changes requested or Approved with comments depending on evidence |
| `dependency-upgrade-pr.md` | dependency/supply-chain, ci/deployment, test strategy | Needs clarification or Changes requested if changelog/security evidence is missing |
| `react-native-permission-pr.md` | React Native/Expo, appsec/auth if permissions affect privacy | Changes requested |
| `clean-pr.md` | only locally relevant specialists | Approved |
