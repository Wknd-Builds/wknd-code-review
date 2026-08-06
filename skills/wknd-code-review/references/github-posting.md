# GitHub Posting

Source snapshot: 2026-08-06.

Use this reference when WKND review output may become GitHub PR review content.

## Draft By Default

All GitHub-oriented review content is draft-only by default. Preparing, writing, formatting, or making comments "GitHub-ready" does not authorize posting.

Draft output must clearly say that comments have not been posted unless the user explicitly requested posting and posting succeeded.

## Explicit Posting Trigger

Post, submit, publish, approve/request changes, or otherwise send GitHub review content only when the user explicitly asks with language such as:

- "post the review"
- "submit these comments"
- "publish the findings"
- "leave this as PR feedback"
- "approve/request changes on GitHub"

Ambiguous phrasing stays draft-only. Examples that do not authorize posting include "prepare comments," "write a review," "make this GitHub-ready," "draft feedback," or "what should I comment?"

## Ambiguous Or Unsafe Drafts

Do not post ambiguous drafts. Before posting, the review content must have:

- A clear top-level review body or decision.
- Inline comments with file, line, severity, confidence, issue, impact, evidence, fix direction, and `draft_note`.
- No unresolved placeholders.
- No uncertainty about whether the user authorized posting.

If any of those are missing, provide the draft and ask for clarification instead of posting.

## Tooling Failures And Fallbacks

If GitHub tooling is unavailable, lacks permissions, cannot resolve the PR, cannot place an inline comment, or posting fails:

- Do not retry blindly.
- Do not claim anything was posted.
- Return the GitHub draft content.
- Report the exact unavailable tool, permission issue, API failure, unresolved file/line, or other failure reason.
- Include any partial posting state only when verified.

## Reporting Posted Content

After a successful explicit post, report exactly what was posted:

- Top-level review body or submitted decision.
- Inline comments by file and line.
- Any findings intentionally omitted from posting and why.
- Any residual risk or unavailable GitHub context that still matters.

Never imply a post, submit, publish, approval, or request-changes action happened without tool evidence.
