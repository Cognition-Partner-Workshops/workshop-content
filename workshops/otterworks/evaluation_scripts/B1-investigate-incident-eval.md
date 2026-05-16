# Evaluation: B1 — Investigate Production Incident

## Inputs
- **User:** `{{user_email}}`
- **Repo:** `{{git_repo}}`
- **Branch/PR:** `{{branch_or_pr}}`

## Instructions

Check out the specified branch/PR in `{{git_repo}}`. Evaluate whether the participant investigated at least one chaos scenario and documented the root cause.

Compare the branch against `main` to identify what the participant changed.

## Criteria

1. **Root cause documented:** The participant has documented the root cause of at least one chaos scenario — either in a commit message, PR description, a markdown file, or code comments. Look for references to the chaos flags (`chaos:search-service:suggest_500`, `chaos:file-service:upload_failure`, `chaos:notification-service:processing_failure`, `chaos:document-service:slow_queries`) and an explanation of what code path fails and why.

2. **Fix committed (optional but check):** If the participant fixed the root cause, verify the fix addresses the actual bug path (e.g., adding a missing key check, handling an exception, fixing a timeout).

## Output Format

```
RESULT: PASS | FAIL

CRITERIA:
1. Root cause documented: PASS/FAIL — [details]
2. Fix committed: PASS/FAIL/SKIPPED — [details]

SUMMARY: [one sentence overall assessment]
```

Overall RESULT is PASS if criterion 1 is met (participant identified and documented a root cause).
