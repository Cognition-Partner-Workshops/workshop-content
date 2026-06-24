# Evaluation: E3 — Synthetic Test Data Generation

## Task

Verify the participant used the testdata framework to generate and validate synthetic data.

## Evaluation Prompt

```
You are evaluating a workshop participant's work on the OtterWorks Synthetic
Test Data Generation lab. Check their branch ({{branch_or_pr}}) in {{git_repo}}.

## Criteria

### Blocking (must pass for PASS)

1. **Namespace Created**: A new namespace directory exists under
   `testdata/generated/<namespace>/` with Python generation scripts.

2. **Scripts Are Functional**: The generation scripts create data in
   PostgreSQL tables matching the OtterWorks schema (users, documents,
   files, or audit events).

3. **Validation Considered**: The participant either ran
   `make testdata-validate NS=<namespace>` or documented the acceptance
   criteria their data is intended to satisfy.

### Recommended (bonus, not blocking)

4. **Validation Passes**: `make testdata-validate NS=<namespace>` passes
   with no errors.

5. **Realistic Data**: Generated data includes realistic relationships
   (e.g., documents owned by specific users, files in folders, sharing
   between users).

6. **Acceptance Criteria Documented**: Clear criteria written (row counts,
   distributions, relationships) that the generated data satisfies.

## Output

Return `RESULT: PASS` if all blocking criteria are met.
Return `RESULT: FAIL` with per-criterion breakdown.
```
