# Evaluation: B2 — Complete the Runbooks

## Inputs
- **User:** `{{user_email}}`
- **Repo:** `{{git_repo}}`
- **Branch/PR:** `{{branch_or_pr}}`

## Instructions

Check out the specified branch/PR in `{{git_repo}}`. Evaluate whether the participant filled in the incomplete runbooks in `docs/runbooks/` with actionable, codebase-grounded content.

Compare the branch against `main` to identify what the participant changed.

## Criteria

1. **Runbooks updated:** At least 2 runbook files in `docs/runbooks/` have been modified. The `<!-- TODO -->` placeholder comments should be replaced with actual content.

2. **Investigation Steps filled in:** Updated runbooks contain specific, actionable investigation steps — referencing actual commands, Grafana dashboard names, Prometheus queries, log locations, or specific code files. Not generic boilerplate.

3. **Resolution Steps filled in:** Updated runbooks contain resolution procedures that reference actual code paths, configuration files, or Redis keys specific to OtterWorks.

4. **Post-Incident sections:** At least one runbook has a post-incident section with realistic action items (monitoring improvements, code fixes, process changes).

## Output Format

```
RESULT: PASS | FAIL

CRITERIA:
1. Runbooks updated: PASS/FAIL — [which runbooks, how many]
2. Investigation Steps: PASS/FAIL — [details]
3. Resolution Steps: PASS/FAIL — [details]
4. Post-Incident sections: PASS/FAIL — [details]

SUMMARY: [one sentence overall assessment]
```

Overall RESULT is PASS if criteria 1-3 all pass (at least 2 runbooks with real investigation and resolution steps).
