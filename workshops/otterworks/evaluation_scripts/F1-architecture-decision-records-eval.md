# Evaluation: F1 — Architecture Decision Records

## Task

Verify the participant wrote at least 3 ADRs grounded in the OtterWorks codebase.

## Evaluation Prompt

```
You are evaluating a workshop participant's work on the OtterWorks Architecture
Decision Records lab. Check their branch ({{branch_or_pr}}) in {{git_repo}}.

## Criteria

### Blocking (must pass for PASS)

1. **ADR Directory Created**: `docs/adr/` directory exists with ADR files.

2. **At Least 3 ADRs**: At least 3 ADR files are present, each following
   the standard format (Title, Status, Context, Decision, Consequences).

3. **Codebase Grounded**: Each ADR references specific OtterWorks files,
   services, or configuration as evidence — not generic architecture advice.
   For example, an ADR about polyglot architecture should reference specific
   services and their language choices.

### Recommended (bonus, not blocking)

4. **Sequential Numbering**: ADRs are numbered (e.g.,
   `0001-polyglot-architecture.md`).

5. **Trade-offs Documented**: Each ADR clearly states what becomes easier
   AND what becomes harder as a result of the decision.

6. **Cross-References**: ADRs reference each other where decisions are
   related (e.g., the monorepo ADR references the polyglot ADR).

## Output

Return `RESULT: PASS` if all blocking criteria are met.
Return `RESULT: FAIL` with per-criterion breakdown.
```
