# Review Decision Model

Source snapshot: 2026-08-06.

Use this reference to calibrate WKND review findings and final merge decisions.

## Default Threshold

Use a balanced default. Request changes only for credible, evidence-backed issues that materially threaten correctness, security, data integrity, deployability, maintainability, or user-facing behavior.

Uncertain risks belong in `Residual Risk` unless there is a concrete failure mode with evidence.

## Decisions

- `Approved`: no blocking or important findings.
- `Approved with comments`: only nits, suggestions, learning notes, or praise.
- `Changes requested`: at least one blocking issue, or important issues that materially threaten merge readiness.
- `Needs clarification`: intent or external constraints are too unclear to fairly approve or request changes.

## Severity Labels

- `blocking`: high-confidence issue that should stop merge.
- `important`: likely issue or serious missing coverage that should be addressed.
- `nit`: minor local consistency or readability issue.
- `suggestion`: optional improvement.
- `learning`: useful explanation that should not imply required action.
- `praise`: specific strong work worth preserving.
