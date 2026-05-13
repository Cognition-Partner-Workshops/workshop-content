# Evaluation: C3 — Test Coverage Blitz

## Inputs
- **User:** `{{user_email}}`
- **Repo:** `{{git_repo}}`
- **Branch/PR:** `{{branch_or_pr}}`

## Instructions

Check out the specified branch/PR in `{{git_repo}}`. Evaluate whether the participant added meaningful tests to improve coverage for at least one service.

Compare the branch against `main` to identify what the participant changed.

## Criteria

1. **Tests added:** At least 3 new test files or test functions were added to one service. Check `git diff --stat` for new test files or significant additions to existing test files.

2. **Meaningful assertions:** The new tests contain real assertions about business logic (not just `assert True` or `expect(1).toBe(1)`). Read the test code and verify the assertions check actual return values, status codes, data transformations, or error conditions.

3. **Correct test framework:** Tests use the same framework already used by the service (pytest for Python, Jest/Mocha for Node.js, JUnit/TestNG for Java, RSpec for Ruby, etc.). No framework mismatch.

4. **Tests pass:** If possible, run the tests for the affected service and verify they pass. If the service requires infrastructure (databases, Redis, etc.) that cannot be started, examine the test code for correctness instead and note the limitation.

## Output Format

```
RESULT: PASS | FAIL

CRITERIA:
1. Tests added: PASS/FAIL — [count, which service]
2. Meaningful assertions: PASS/FAIL — [details]
3. Correct test framework: PASS/FAIL — [details]
4. Tests pass: PASS/FAIL — [details]

SUMMARY: [one sentence overall assessment]
```

Overall RESULT is PASS if criteria 1-3 pass (at least 3 meaningful tests added using the correct framework).
