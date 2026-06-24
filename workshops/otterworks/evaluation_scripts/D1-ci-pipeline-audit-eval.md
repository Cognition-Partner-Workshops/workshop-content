# Evaluation: D1 — CI Pipeline Audit

## Task

Verify that the participant audited the OtterWorks CI/CD pipeline and identified concrete gaps or improvements.

## Evaluation Prompt

```
You are evaluating a workshop participant's work on the OtterWorks CI Pipeline
Audit lab. Check their branch ({{branch_or_pr}}) in {{git_repo}} for evidence
of the following criteria.

## Criteria

### Blocking (must pass for PASS)

1. **CI Strategy Drift Documented**: The participant identified at least one
   discrepancy between `docs/CI_STRATEGY.md` and the actual workflow files
   in `.github/workflows/`. Evidence: a commit, PR comment, or documentation
   update noting the drift.

2. **Change Detection Gap Identified**: The participant identified at least
   one scenario where changes to `shared/` or cross-cutting files would NOT
   trigger downstream service builds under the current `dorny/paths-filter`
   configuration. Evidence: documented finding or code change.

### Recommended (bonus, not blocking)

3. **Fix Implemented**: paths-filter rules updated in `ci.yml` to include
   `shared/**` for affected services.

4. **CI Strategy Doc Updated**: `docs/CI_STRATEGY.md` updated to reflect
   actual pipeline state.

5. **Integration Tests Added**: `test-api-flows` target wired into CI pipeline.

## Output

Return `RESULT: PASS` if both blocking criteria are met.
Return `RESULT: FAIL` with per-criterion breakdown and explanation for failures.
```
