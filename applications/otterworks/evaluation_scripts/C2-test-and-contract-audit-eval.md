# Evaluation: C2 — Test & Contract Audit (C2 Contract Audit + C3 Coverage Blitz)

## Inputs
- **User:** `{{user_email}}`
- **Repo:** `{{git_repo}}`
- **Branch/PR:** `{{branch_or_pr}}`

## Instructions

Check out the specified branch/PR in `{{git_repo}}`. This lab file covers two activities (C2 and C3). Evaluate each separately. A participant may have completed one or both.

Compare the branch against `main` to identify what the participant changed.

## Activity C2: API Contract Audit

### Criteria

1. **Notification event drift fixed:** The camelCase/snake_case mismatch between `shared/events/schemas/notification-events.json` and the notification-service's actual event publishing code has been resolved. Either the schema was updated to match the service, or the service was updated to match the schema. Check both files for consistency.

2. **Audit event drift fixed:** The missing `timestamp` issue in audit batch events has been resolved. Either `shared/events/schemas/audit-events.json` makes `timestamp` optional for batch events, or the audit-service code now always includes `timestamp`.

3. **Direction documented:** The participant documented which direction they chose (update schema to match service, or update service to match schema) and why. Look in commit messages, PR description, or a README/doc file.

4. **OpenAPI spec validated:** At least one OpenAPI spec in `shared/openapi/` has been compared against the actual service implementation. Evidence: a contract test was run, or the spec was updated to fix discrepancies, or a comment/doc confirms the spec matches.

### C2 Result
PASS if criteria 1-2 pass (both drift issues identified and fixed). FAIL if neither drift issue is addressed.

---

## Activity C3: Test Coverage Blitz

### Criteria

5. **Tests added:** At least 3 new test files or test functions were added to one service. Check `git diff --stat` for new test files or significant additions to existing test files.

6. **Meaningful assertions:** The new tests contain real assertions about business logic (not just `assert True` or `expect(1).toBe(1)`). Read the test code and verify the assertions check actual return values, status codes, data transformations, or error conditions.

7. **Correct test framework:** Tests use the same framework already used by the service (pytest for Python, Jest/Mocha for Node.js, JUnit/TestNG for Java, RSpec for Ruby, etc.). No framework mismatch.

8. **Tests pass:** If possible, run the tests for the affected service and verify they pass. If the service requires infrastructure (databases, Redis, etc.) that cannot be started, examine the test code for correctness instead and note the limitation.

### C3 Result
PASS if criteria 5-7 pass (at least 3 meaningful tests added using the correct framework). FAIL if no tests were added or tests are trivial.

---

## Output Format

```
RESULT: PASS | FAIL

ACTIVITY C2 — API Contract Audit: PASS/FAIL
  1. Notification event drift fixed: PASS/FAIL — [details]
  2. Audit event drift fixed: PASS/FAIL — [details]
  3. Direction documented: PASS/FAIL — [details]
  4. OpenAPI spec validated: PASS/FAIL — [details]

ACTIVITY C3 — Test Coverage Blitz: PASS/FAIL
  5. Tests added: PASS/FAIL — [count, which service]
  6. Meaningful assertions: PASS/FAIL — [details]
  7. Correct test framework: PASS/FAIL — [details]
  8. Tests pass: PASS/FAIL — [details]

ACTIVITIES COMPLETED: [list which of C2, C3 passed]
SUMMARY: [one sentence overall assessment]
```

Overall RESULT is PASS if at least ONE activity (C2 or C3) passes. The participant chooses how many activities to attempt within the time budget.
