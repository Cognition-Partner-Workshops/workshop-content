# Evaluation: F2 — API Flow Test Expansion

## Task

Verify the participant wrote at least 3 new black-box API flow tests.

## Evaluation Prompt

```
You are evaluating a workshop participant's work on the OtterWorks API Flow
Test Expansion lab. Check their branch ({{branch_or_pr}}) in {{git_repo}}.

## Criteria

### Blocking (must pass for PASS)

1. **New Tests Written**: At least 3 new test functions added to files in
   `tests/api/` (either new test functions in existing files or a new
   test file).

2. **Pattern Compliance**: New tests follow the existing patterns: use
   `conftest.py` fixtures, send requests through the API gateway URL,
   assert specific HTTP status codes and response shapes.

3. **Meaningful Coverage**: Tests exercise flows that were previously
   untested or under-tested (not duplicates of existing test logic).
   Cross-reference with `docs/api-route-matrix.md` coverage annotations.

### Recommended (bonus, not blocking)

4. **Tests Pass**: `make test-api-flows` succeeds with the new tests
   (requires running local stack).

5. **Edge Cases Covered**: At least one test covers an edge case
   (cross-user access control, error path, concurrent operation).

6. **New Flow Covered**: A previously untested flow from the route matrix
   is now covered (e.g., templates, folders, audit trail verification).

## Output

Return `RESULT: PASS` if all blocking criteria are met.
Return `RESULT: FAIL` with per-criterion breakdown.
```
