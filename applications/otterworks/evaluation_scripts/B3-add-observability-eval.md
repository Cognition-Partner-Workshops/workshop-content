# Evaluation: B3 — Add Observability

## Inputs
- **User:** `{{user_email}}`
- **Repo:** `{{git_repo}}`
- **Branch/PR:** `{{branch_or_pr}}`

## Instructions

Check out the specified branch/PR in `{{git_repo}}`. Evaluate whether the participant added observability instrumentation (Prometheus metrics, structured logging) to at least one under-instrumented service.

Compare the branch against `main` to identify what the participant changed.

## Criteria

1. **Service identified:** The participant modified files in at least one service directory to add observability instrumentation.

2. **Prometheus metrics added:** New Prometheus metric definitions (counters, histograms, gauges) appear in the modified service. Look for `prometheus_client` (Python), `prom-client` (Node.js), `prometheus` crate (Rust), Micrometer (Java/Kotlin), or equivalent library usage.

3. **Structured logging:** The modified service uses structured logging (not bare `print()` / `puts` / `console.log`). Look for a logging framework (structlog, winston, slog, SLF4J, etc.).

4. **No breaking changes:** The service's existing endpoints and functionality are preserved. No routes removed, no response shapes changed.

## Output Format

```
RESULT: PASS | FAIL

CRITERIA:
1. Service identified: PASS/FAIL — [which service]
2. Prometheus metrics added: PASS/FAIL — [details]
3. Structured logging: PASS/FAIL — [details]
4. No breaking changes: PASS/FAIL — [details]

SUMMARY: [one sentence overall assessment]
```

Overall RESULT is PASS if criteria 1-2 pass (metrics added to at least one service).
