---
name: wknd-code-review
description: Use when reviewing pull requests, diffs, commits, code changes, GitHub PRs, linked issues, merge readiness, security, data/migration risk, frontend behavior, React Native/Expo changes, CI/deploy risk, observability, dependencies, or production readiness.
---

# WKND Code Review

WKND Code Review is a review workflow. Do not edit the reviewed application code during the review. You may inspect, run safe verification commands when appropriate, draft comments, and propose remediation sketches. Implement fixes only when the user makes a separate implementation request.

## Invocation Contract

Use this skill as the single public entrypoint for WKND reviews. Keep the public surface concise and load the coordinator and references for operational detail instead of duplicating the full workflow here.

Default mode is `report only`: provide a structured review report in chat and do not prepare GitHub comments unless the user asks for them.

Draft GitHub comments when the user asks for GitHub comments, PR comments, inline comments, or review comments without explicit post, submit, publish, approve/request changes, or otherwise send-to-GitHub language. Ambiguous wording stays draft-only.

Only post GitHub comments when the user explicitly asks to post, submit, publish, approve/request changes, or otherwise send the review or comments to GitHub. Never infer posting permission from a request to review, comment, prepare, draft, suggest, or summarize.

## Required Loading Sequence

Read these files before producing the review:

1. `agents/coordinator.md`.
2. `references/review-context-gathering.md`.
3. `references/review-decision-model.md`.
4. `references/output-modes.md`.
5. Relevant stack references based on the risk map: `references/nodejs-typescript.md`, `references/react-router-remix.md`, `references/react-next.md`, `references/prisma-postgres.md`, `references/auth-sessions.md`, `references/testing.md`, `references/ci-deployment.md`, `references/accessibility.md`, `references/browser-performance.md`, `references/observability.md`, `references/dependency-security.md`, and `references/react-native-expo.md`.
6. Relevant specialist prompts from `agents/`: `agents/appsec-auth-reviewer.md`, `agents/data-migrations-reviewer.md`, `agents/test-strategy-reviewer.md`, `agents/frontend-runtime-reviewer.md`, `agents/accessibility-reviewer.md`, `agents/performance-reviewer.md`, `agents/dependency-supply-chain-reviewer.md`, `agents/ci-deployment-reviewer.md`, `agents/observability-reviewer.md`, and `agents/react-native-expo-reviewer.md`.
7. `references/github-posting.md` whenever the user asks for GitHub draft comments or explicitly asks to post, submit, publish, approve/request changes, or otherwise send review content to GitHub.

Use the coordinator to identify review scope, collect context, route to specialists, calibrate severity, choose the output mode, and produce the final decision.

## Review Posture

- Establish the intended behavior, linked issue context, base/head range, and affected product workflows before judging the diff.
- Learn nearby conventions and recent similar changes so findings match the codebase rather than generic preferences.
- Verify each finding against the actual changed code and surrounding call paths. Do not present speculation as a finding.
- Focus on correctness, security, data safety, product behavior, deployability, test adequacy, accessibility, performance, observability, and maintainability.
- Include praise when there is genuinely strong work, but never bury important concerns behind compliments.

## Output Order

Use this canonical report order:

1. Findings.
2. Decision.
3. Summary.
4. Action Items.
5. Verification Evidence.
6. Residual Risk.

Findings must lead, ordered by severity and confidence. Include confidence, file and line evidence when possible, impact, and a concise remediation direction. If there are no findings, say so clearly before the decision and still include verification evidence and residual risk.

For GitHub draft output, include `Top-Level Review Body` and `Inline-Ready Comments` sections. Each inline-ready comment must include `file`, `line`, `severity`, `confidence`, `issue`, `impact`, `evidence`, `fix_direction`, and `draft_note`.
