# Output Modes

Source snapshot: 2026-08-06.

Use this reference to choose and format WKND review output.

## Mode Inference

- `report only`: default for ordinary WKND review requests.
- `draft GitHub comments`: use when the user asks for GitHub-ready review text without explicit post, submit, publish, approve/request changes, or otherwise send-to-GitHub language.
- `post GitHub review`: use only when the user explicitly asks to post, submit, publish, approve/request changes, or otherwise send review comments or findings to GitHub.

Ambiguous wording stays draft-only.

## Canonical Report Order

1. Findings.
2. Decision.
3. Summary.
4. Action Items.
5. Verification Evidence.
6. Residual Risk.

## Report-Only Shape

Use the canonical report order exactly:

### Findings

List findings ordered by severity, then confidence, then review importance. Each finding should include severity, confidence, file/line or location, issue, impact, evidence, and fix direction.

### Decision

State exactly one decision: `Approved`, `Approved with comments`, `Changes requested`, or `Needs clarification`.

### Summary

Briefly explain review scope and the highest-signal conclusion.

### Action Items

List required fixes first, then optional follow-ups. Say `None` when no action is needed.

### Verification Evidence

Report commands, code paths, references, docs, CI state, or fixtures checked. Be explicit about anything unavailable.

### Residual Risk

Record meaningful uncertainty that remains after the review. Say `None identified` only when context and verification justify it.

## GitHub Draft Output

GitHub-ready output is draft-by-default unless the user explicitly requested posting.

Use this exact contract when the user asks for GitHub-ready draft output:

```markdown
## GitHub Draft Output

### Top-Level Review Body

[Concise review body with decision, scope, high-level findings summary, verification evidence, and residual risk.]

### Inline-Ready Comments

- file:
- line:
- severity:
- confidence:
- issue:
- impact:
- evidence:
- fix_direction:
- draft_note:
```

The bracketed top-level body line is an illustrative contract placeholder, not text to leave in a real review draft.

The `draft_note` must clearly state that the comment has not been posted unless explicit posting was requested and posting succeeded with tool evidence.

## No-Findings Shape

When there are no findings:

### Findings

No findings.

### Decision

Use `Approved` if there are no required comments, or `Approved with comments` if only nits, suggestions, learning notes, or praise remain.

### Summary

State what was reviewed and why no actionable issues were identified.

### Action Items

None.

### Verification Evidence

List the exact evidence reviewed, including tests, CI, docs, changed files, or local context.

### Residual Risk

Call out unavailable context or remaining uncertainty. Keep it short.

## Posted Review Shape

When the user explicitly requested posting and posting succeeds, report:

- What top-level review body was posted.
- Which inline comments were posted, including file and line.
- The submitted review decision when the tool supports one.
- Any comments intentionally left as drafts or skipped.

If posting fails or GitHub tooling is unavailable, return the draft content and the failure reason.
