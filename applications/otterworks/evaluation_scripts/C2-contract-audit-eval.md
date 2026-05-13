# Evaluation: C2 — API Contract Audit

## Inputs
- **User:** `{{user_email}}`
- **Repo:** `{{git_repo}}`
- **Branch/PR:** `{{branch_or_pr}}`

## Instructions

Check out the specified branch/PR in `{{git_repo}}`. Evaluate whether the participant identified and fixed the known API contract drift issues between schemas and service implementations.

Compare the branch against `main` to identify what the participant changed.

## Criteria

1. **Notification event drift fixed:** The camelCase/snake_case mismatch between `shared/events/schemas/notification-events.json` and the notification-service's actual event publishing code has been resolved. Either the schema was updated to match the service, or the service was updated to match the schema. Check both files for consistency.

2. **Audit event drift fixed:** The missing `timestamp` issue in audit batch events has been resolved. Either `shared/events/schemas/audit-events.json` makes `timestamp` optional for batch events, or the audit-service code now always includes `timestamp`.

3. **Direction documented:** The participant documented which direction they chose (update schema to match service, or update service to match schema) and why. Look in commit messages, PR description, or a README/doc file.

4. **OpenAPI spec validated:** At least one OpenAPI spec in `shared/openapi/` has been compared against the actual service implementation. Evidence: a contract test was run, or the spec was updated to fix discrepancies, or a comment/doc confirms the spec matches.

## Output Format

```
RESULT: PASS | FAIL

CRITERIA:
1. Notification event drift fixed: PASS/FAIL — [details]
2. Audit event drift fixed: PASS/FAIL — [details]
3. Direction documented: PASS/FAIL — [details]
4. OpenAPI spec validated: PASS/FAIL — [details]

SUMMARY: [one sentence overall assessment]
```

Overall RESULT is PASS if criteria 1-2 pass (both drift issues identified and fixed).
