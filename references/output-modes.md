# Output Modes

Source snapshot: 2026-08-06.

Use this reference to choose and format WKND review output.

## Mode Inference

- `report only`: default for ordinary WKND review requests.
- `draft GitHub comments`: use when the user asks for GitHub-ready review text without explicit post, submit, or publish language.
- `post GitHub review`: use only when the user explicitly asks to post, submit, or publish findings.

Ambiguous wording stays draft-only.

## Canonical Report Order

1. Findings.
2. Decision.
3. Summary.
4. Action Items.
5. Verification Evidence.
6. Residual Risk.

## GitHub Draft Output

Include a top-level review body and inline-ready comments with file, line, severity, issue, impact, fix direction, and a clear draft note.
