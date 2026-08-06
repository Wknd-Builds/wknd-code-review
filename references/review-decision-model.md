# Review Decision Model

Source snapshot: 2026-08-06.

Use this reference to calibrate WKND review findings and final merge decisions.

## Default Threshold

Use a balanced default. Request changes only for credible, evidence-backed issues that materially threaten correctness, security, data integrity, deployability, maintainability, or user-facing behavior.

The review should be neither approval-biased nor adversarial. Do not invent findings to prove effort, and do not minimize a concrete failure mode because it is inconvenient. If a risk is plausible but not proven, keep it out of `Findings` and put it in `Residual Risk`.

## Evidence And Confidence

Every finding must include:

- A specific file, line, hunk, behavior, config, migration, dependency, CI step, or runtime path.
- A concrete failure mode, not only a preference.
- User, business, security, data, deploy, test, or maintenance impact.
- Confidence based on observed code and available context.

Use confidence to calibrate severity:

- `high`: directly supported by the diff, nearby code, tests, docs, logs, schema, package metadata, or current primary documentation.
- `medium`: supported by code evidence plus a reasonable inference about runtime behavior or project convention.
- `low`: possible concern with incomplete support. Low-confidence items normally belong in `Residual Risk`, not `Findings`.

When specialist reviewers disagree, resolve by evidence and confidence, not by vote count.

## Decisions

- `Approved`: no blocking or important findings.
- `Approved with comments`: only nits, suggestions, learning notes, or praise.
- `Changes requested`: at least one blocking issue, or important issues that materially threaten merge readiness.
- `Needs clarification`: intent or external constraints are too unclear to fairly approve or request changes.

Decision rules:

- Choose `Changes requested` for any credible `blocking` issue.
- Choose `Changes requested` when multiple `important` issues together create material merge risk.
- Choose `Approved with comments` when remaining comments are optional, local, educational, or low-risk.
- Choose `Needs clarification` only when missing intent or unavailable external constraints prevent fair judgment. Do not use it to avoid making an evidence-backed call.

## Severity Labels

- `blocking`: high-confidence issue that should stop merge, including security bypass, privilege or tenant isolation failure, data loss, broken migration/deploy path, user-visible correctness bug, high-risk untested behavior, or a maintainability trap likely to cause production failure.
- `important`: likely issue or serious missing coverage that should be addressed, but may not independently block merge.
- `nit`: minor local consistency or readability issue.
- `suggestion`: optional improvement.
- `learning`: useful explanation that should not imply required action.
- `praise`: specific strong work worth preserving.

## Residual Risk

Use `Residual Risk` for uncertainty that matters but is not a finding:

- Unavailable PR metadata, linked issues, CI results, logs, changelogs, migrations, runtime docs, production constraints, or deployment details.
- Plausible risks that lack enough evidence for a finding.
- Review passes that could not be completed, such as a missing specialist perspective or unavailable primary documentation.
- Assumptions made to proceed with local evidence.

Keep residual risk concise and scoped. Do not hide actionable defects there; promote evidence-backed defects into `Findings`.

## Praise, Nits, Suggestions, And Learning Notes

- Praise should be specific to an implementation choice worth preserving, such as a well-scoped test, safer migration ordering, or clear auth boundary.
- Nits are small and local. They should not affect the decision unless they expose a broader correctness or maintainability issue.
- Suggestions are optional improvements. Make the optionality explicit and avoid presenting them as required fixes.
- Learning notes explain relevant behavior or project convention without implying action.
- Do not post praise, nits, suggestions, or learning notes as inline comments unless they help the author act on the changed code.
