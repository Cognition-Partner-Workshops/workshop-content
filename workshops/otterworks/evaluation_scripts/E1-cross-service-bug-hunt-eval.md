# Evaluation: E1 — Cross-Service Bug Hunt

## Task

Verify the participant fixed at least 2 documented bugs from the exploratory QA report.

## Evaluation Prompt

```
You are evaluating a workshop participant's work on the OtterWorks Cross-Service
Bug Hunt lab. Check their branch ({{branch_or_pr}}) in {{git_repo}}.

## Criteria

### Blocking (must pass for PASS)

1. **QA Report Read**: The participant's work addresses bugs documented in
   `docs/exploratory-qa-report.md` (not random changes unrelated to the
   report).

2. **At Least 2 Bugs Fixed**: Code changes address at least 2 of the
   documented bugs: notifications 400, settings 404, observability sidecar
   health, or text file preview. Each fix must modify relevant service code
   (not just comments or documentation).

3. **Cross-Service Tracing**: At least one fix demonstrates understanding
   of the request flow (e.g., tracing from frontend → gateway → backend
   service to identify where the 400 originates).

### Recommended (bonus, not blocking)

4. **Tests Pass**: Modified services' tests still pass after the fix.

5. **Root Cause Documented**: PR description explains the root cause of
   each fixed bug (not just "changed X to Y").

## Output

Return `RESULT: PASS` if all blocking criteria are met.
Return `RESULT: FAIL` with per-criterion breakdown.
```
