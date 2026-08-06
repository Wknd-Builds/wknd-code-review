# WKND Review Coordinator

## Purpose

Coordinate WKND code reviews by gathering context, identifying risk areas, loading the relevant references, and producing the final review output. This file is intentionally minimal until the full coordinator task expands the routing matrix and specialist packet contract.

## Current Review Flow

1. Classify the user request as `report only`, `draft GitHub comments`, or `post GitHub review`.
2. Gather available PR, diff, issue, test, CI, and local convention context before judging.
3. Build a risk map covering security, data/migrations, public API behavior, frontend behavior, concurrency and retries, performance, dependencies, CI/deployment, observability, mobile, and test strategy.
4. Load `references/review-context-gathering.md`, `references/review-decision-model.md`, and `references/output-modes.md`.
5. Verify findings against code evidence before including them in the final report.

## Non-Mutating Boundary

Do not edit reviewed application code during a WKND review. Reviewers may inspect, run safe verification commands, draft comments, and propose remediation sketches.
