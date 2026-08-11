# WKND Code Review

WKND Code Review is a Codex skill for high-signal production code review. It uses one public invocation surface, a coordinator workflow, specialist reviewer prompts, stack/domain references, and fixture-based validation.

## Usage

Invoke it naturally:

```text
Run a WKND code review.
Run a WKND code review and draft GitHub comments.
Run a WKND code review and post your findings.
```

Default output is report-only. GitHub comments are draft-only unless the user explicitly asks to post, submit, publish, approve/request changes, or otherwise send the review to GitHub.

## Layout

- `SKILL.md`: public skill entrypoint and loading contract.
- `agents/coordinator.md`: review coordinator, routing, reconciliation, and output-mode handoff.
- `agents/*-reviewer.md`: focused specialist reviewer prompts.
- `references/`: severity, output, posting, frontend structure, stack, and domain review guidance.
- `fixtures/`: synthetic PR packets for validation.
- `docs/validation/wknd-code-review-validation.md`: validation checklist and recorded fixture dry-runs.

## Validation

Validate the skill root:

```bash
PYTHONPATH=/tmp/wknd-pyyaml python3 /Users/wesleychang/.codex/skills/.system/skill-creator/scripts/quick_validate.py /Users/wesleychang/projects/wknd-code-review
```

Then use `docs/validation/wknd-code-review-validation.md` to confirm routing, output shape, GitHub draft/post behavior, and fixture outcomes.
