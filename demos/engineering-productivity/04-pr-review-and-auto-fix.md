# PR Review and Safe Auto-Fix — Review Like a Senior Engineer, Fix Only What Is Approved

A teammate opens a PR that adds two admin endpoints to the Python
`document-service`. It looks harmless and the unit tests pass. Devin reviews it
against the rest of the module and the Semgrep result on the PR, posts specific
findings as review comments, and then — only for the findings a human approves —
applies the fixes with tests on the same branch and replies in each thread.

## Table of Contents

- [Quick Start](#quick-start)
- [Before](#before)
- [Part 1 — Open the PR](#part-1)
- [Part 2 — Review](#part-2)
- [Part 3 — Approve, Then Auto-Fix](#part-3)
- [Part 4 — Devin Review as the Standing Gate](#part-4)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

1. From `workshop-<attendee_id>`, open a PR into `workshop` titled
   `document-service: add owner stats and duplicate endpoints` (the diff is
   already on your branch — see [Part 1](#part-1)).
2. Paste the review prompt from [Part 2](#part-2) into a new Devin session.
3. Reply to the review threads you accept, then paste the fix prompt from
   [Part 3](#part-3) into the same session.

---

<a id="before"></a>
## Before

`workshop` includes a commit to `services/document-service/app/api/documents.py`
adding:

- `GET /api/v1/documents/owners/{owner_id}/stats` — document count, word total
  and last-updated for an owner, "for the admin dashboard".
- `POST /api/v1/documents/{document_id}/duplicate` — copy a document for the
  calling user.

Both endpoints have unit tests that pass. On the same branch:

- `.github/workflows/security-scan.yml` job `sast` (Semgrep,
  `p/owasp-top-ten` + `p/security-audit`) reports **1 blocking finding** in
  `documents.py`.
- The rest of the module shows the conventions the new code ignores:
  `_require_user_id()` / `_ensure_owner()` for identity and ownership,
  `DocumentService` methods that exclude templates and soft-deleted rows, ORM
  queries rather than raw SQL, and a `500`-on-failure posture.

Devin Review is enabled on the org and comments on every PR automatically; this
demo has Devin do the review interactively so the reasoning is visible, then
compares with the automated review in [Part 4](#part-4).

---

<a id="part-1"></a>
## Part 1 — Open the PR

The fixture diff is already on `workshop-<attendee_id>` because the branch is
cut from `workshop`. Open the PR from the branch into `workshop` (GitHub UI or
`gh pr create --base workshop --head workshop-<attendee_id> --title "document-service: add owner stats and duplicate endpoints" --body "Admin dashboard needs per-owner usage; also lets users duplicate a document."`).
Wait for `security-scan` to finish — the `sast` job should be red.

---

<a id="part-2"></a>
## Part 2 — Review

```
Review pull request <PR URL> in Cognition-Partner-Workshops/otterworks. Read the full diff, then read the rest of services/document-service/app/api/documents.py and services/document-service/app/services/document_service.py so you know the module's existing conventions for identity (_require_user_id, _ensure_owner), template and soft-delete filtering, ORM usage, and error handling. Also read the sast job output of the security-scan workflow run for this PR. Post a GitHub review with one inline comment per concrete finding on the exact line, each stating the impact, the evidence, and the minimal safe fix; cover security (authorization, injection, sensitive data in logs), correctness (wrong totals, silent failures, schema limits), and consistency with the module. Do not change any code yet. Finish with a summary comment that ranks findings by severity and says which ones you consider safe to auto-fix without product input.
```

What a complete review finds on this diff, in the order a reviewer would rank
them:

| Severity | Finding | Where |
|----------|---------|-------|
| Critical | `owner_id` is interpolated into a raw SQL string — injection | `owner_stats`, `where = f"owner_id = '{owner_id}'"` |
| High | No authentication or authorization; anyone can read any owner's usage | `owner_stats` has no `_require_user_id` / ownership check |
| High | Bearer token written to logs | `logger.info(..., authorization=request.headers.get("Authorization"))` |
| High | Any authenticated user can duplicate anyone's document | `duplicate_document` lacks `_ensure_owner(source, user_id)` |
| Medium | Database errors become `200` with zeroed totals | `except Exception` returns zeros |
| Medium | Template rows inflate counts, unlike list/search | missing `is_template = false` |
| Low | Title near the 500-char limit overflows when `" (copy)"` is appended | `title=source.title + " (copy)"` |

Each comment should quote the line, say what happens, and propose the fix in a
sentence. The summary should mark the injection, header-logging, ownership, and
error-handling items as safe to auto-fix, and flag the template-exclusion and
title-truncation items as needing a product answer (should stats include
templates? truncate or reject?).

---

<a id="part-3"></a>
## Part 3 — Approve, Then Auto-Fix

Reply in the threads you accept (for example, "approved" on the injection,
authorization, logging, ownership and error-handling findings; "exclude
templates, truncate titles" on the two judgment calls). Then, in the same
session:

```
On pull request <PR URL>, apply only the review findings that have an approving reply in their thread, on the existing branch workshop-<attendee_id>. For owner_stats: require the caller's identity with _require_user_id and only allow the caller to read their own stats, validate owner_id as a UUID, replace the raw SQL with a parameterized SQLAlchemy query that excludes soft-deleted and template documents, remove the Authorization header from the log line, and return a 500 without leaking SQL or exception text when the database fails. For duplicate_document: enforce _ensure_owner on the source and truncate the copied title so it fits the 500-character column. Add or update tests in services/document-service/tests/test_documents_api.py for each fix, including a cross-user 403 for both endpoints and an injection-shaped owner_id that is rejected. Run poetry run ruff check . and poetry run pytest --cov=app in services/document-service, push to the branch, and confirm the security-scan sast job and the CI Pipeline document-service job are green on the PR. Reply individually in each review thread you addressed with the commit that resolves it, and leave unapproved threads untouched.
```

Expected result: one or two commits on the branch, a reply in every approved
thread pointing at the commit, `sast` green (the blocking finding gone), the
`document-service` CI job green, and the unapproved threads still open for the
human conversation.

---

<a id="part-4"></a>
## Part 4 — Devin Review as the Standing Gate

Open the PR's **Conversation** tab. The automated Devin Review posted its own
inline comments when the PR was opened — compare them with the interactive
review from Part 2; on this fixture they should cover the same seven findings.
The difference is who was in the loop: Devin Review runs on every PR in the
org with no prompt, and the auto-fix in Part 3 is what "with developer approval"
looks like in practice — a reply in the thread is the approval.

---

<a id="key-takeaways"></a>
## Key Takeaways

- The review used the module's own conventions and the Semgrep result as the
  yardstick, so the findings were specific to this codebase, not generic lint.
- Fixes were applied only where a human said yes, in the thread, and each
  thread got its own reply with the resolving commit — the developer stays the
  decision-maker.
- Security findings were verified by the pipeline afterward (`sast` green),
  not asserted by the fix.
- Devin Review provides the same findings on every PR automatically; the
  interactive session is how a team extends that into approved remediation.
