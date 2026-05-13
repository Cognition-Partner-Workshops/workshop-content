# Evaluation: B1 — Incident Response (B1 Investigate + B2 Runbooks + B3 Observability)

## Inputs
- **User:** `{{user_email}}`
- **Repo:** `{{git_repo}}`
- **Branch/PR:** `{{branch_or_pr}}`

## Instructions

Check out the specified branch/PR in `{{git_repo}}`. This lab file covers three activities (B1, B2, B3). Evaluate each separately. A participant may have completed one, two, or all three.

Compare the branch against `main` to identify what the participant changed.

## Activity B1: Investigate Production Incident

### Criteria

1. **Root cause documented:** The participant has documented the root cause of at least one chaos scenario — either in a commit message, PR description, a markdown file, or code comments. Look for references to the chaos flags (`chaos:search-service:suggest_500`, `chaos:file-service:upload_failure`, `chaos:notification-service:processing_failure`, `chaos:document-service:slow_queries`) and an explanation of what code path fails and why.

2. **Fix committed (optional but check):** If the participant fixed the root cause, verify the fix addresses the actual bug path (e.g., adding a missing key check, handling an exception, fixing a timeout).

### B1 Result
PASS if criterion 1 is met (participant identified and documented a root cause). FAIL if no evidence of investigation exists on the branch.

---

## Activity B2: Complete the Runbooks

### Criteria

3. **Runbooks updated:** At least 2 runbook files in `docs/runbooks/` have been modified. The `<!-- TODO -->` placeholder comments should be replaced with actual content.

4. **Investigation Steps filled in:** Updated runbooks contain specific, actionable investigation steps — referencing actual commands, Grafana dashboard names, Prometheus queries, log locations, or specific code files. Not generic boilerplate.

5. **Resolution Steps filled in:** Updated runbooks contain resolution procedures that reference actual code paths, configuration files, or Redis keys specific to OtterWorks.

6. **Post-Incident sections:** At least one runbook has a post-incident section with realistic action items (monitoring improvements, code fixes, process changes).

### B2 Result
PASS if criteria 3-5 all pass (at least 2 runbooks with real investigation and resolution steps). FAIL if runbooks are unchanged or contain only generic content.

---

## Activity B3: Add Observability

### Criteria

7. **Service identified:** The participant modified files in at least one service directory to add observability instrumentation.

8. **Prometheus metrics added:** New Prometheus metric definitions (counters, histograms, gauges) appear in the modified service. Look for `prometheus_client` (Python), `prom-client` (Node.js), `prometheus` crate (Rust), Micrometer (Java/Kotlin), or equivalent library usage.

9. **Structured logging:** The modified service uses structured logging (not bare `print()` / `puts` / `console.log`). Look for a logging framework (structlog, winston, slog, SLF4J, etc.).

10. **No breaking changes:** The service's existing endpoints and functionality are preserved. No routes removed, no response shapes changed.

### B3 Result
PASS if criteria 7-8 pass (metrics added to at least one service). FAIL if no observability changes exist.

---

## Output Format

```
RESULT: PASS | FAIL

ACTIVITY B1 — Investigate Production Incident: PASS/FAIL
  1. Root cause documented: PASS/FAIL — [details]
  2. Fix committed: PASS/FAIL/SKIPPED — [details]

ACTIVITY B2 — Complete the Runbooks: PASS/FAIL
  3. Runbooks updated: PASS/FAIL — [which runbooks, how many]
  4. Investigation Steps: PASS/FAIL — [details]
  5. Resolution Steps: PASS/FAIL — [details]
  6. Post-Incident sections: PASS/FAIL — [details]

ACTIVITY B3 — Add Observability: PASS/FAIL
  7. Service identified: PASS/FAIL — [which service]
  8. Prometheus metrics added: PASS/FAIL — [details]
  9. Structured logging: PASS/FAIL — [details]
  10. No breaking changes: PASS/FAIL — [details]

ACTIVITIES COMPLETED: [list which of B1, B2, B3 passed]
SUMMARY: [one sentence overall assessment]
```

Overall RESULT is PASS if at least ONE activity (B1, B2, or B3) passes. The participant chooses how many activities to attempt within the time budget.
