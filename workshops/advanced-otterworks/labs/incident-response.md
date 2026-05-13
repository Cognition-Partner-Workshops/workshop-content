# Lab: Incident Response

This lab covers three activities that can be done independently or as a progressive sequence.

---

## Activity B1: Investigate Production Incident

### Objective

Trigger a chaos scenario in the OtterWorks platform, investigate the resulting incident using Grafana dashboards and Jaeger traces, and identify the root cause with Devin's help.

### What's Wrong

OtterWorks has a chaos engineering framework that injects failures into production-like services. When chaos is active, services exhibit real failure modes: 500 errors on search suggestions, file upload rejections, notification processing failures, or document-service latency spikes. The admin dashboard has "Trigger Chaos" buttons for each scenario.

The available chaos scenarios are:
1. **Search Suggest 500** — MeiliSearch ranking score enrichment path triggers a KeyError
2. **File Upload Failure** — File-service rejects uploads with a 500
3. **Notification Processing Failure** — Strict JSON schema parser rejects messages with integer timestamps
4. **Document Service Slow Queries** — 3-5 second latency injected before every database query

### Where to Look

- `frontend/admin-dashboard/` — Admin UI with chaos trigger buttons
- `services/admin-service/app/controllers/api/v1/admin/chaos_controller.rb` — Chaos trigger/reset API
- `services/admin-service/app/services/chaos_probe_service.rb` — Synthetic traffic for metrics
- `observability/grafana/dashboards/chaos-scenarios.json` — Grafana dashboard for chaos metrics
- `observability/grafana/dashboards/incident-overview.json` — Cross-service incident overview
- `docs/runbooks/` — Skeleton runbooks for each scenario (investigation steps are incomplete)

### What Done Looks Like

- [ ] Triggered at least one chaos scenario via the admin dashboard
- [ ] Used Grafana dashboards to identify which service is affected and what the symptom is (error spike vs. latency spike)
- [ ] Used Jaeger traces (or asked Devin to read the service code) to identify the root cause
- [ ] Can explain the root cause: what code path fails, why, and how to fix it
- [ ] Optionally: fixed the root cause and verified the service recovers after chaos reset

### Hints

#### Hint 1 — Getting Started
Start by triggering the "Search Suggest 500" chaos scenario from the admin dashboard. Then check the Grafana "Chaos Scenarios" dashboard to see the error rate spike.

#### Hint 2 — Specific Direction
Ask Devin to read `services/search-service/app/api/search.py` and find the code path that runs when `chaos:search-service:suggest_500` is active. The bug is a missing key access on MeiliSearch results.

#### Hint 3 — Approach
Ask Devin: "Read the chaos-related code in `services/search-service/app/api/search.py`, specifically the `suggest()` function. What happens when the chaos flag is active? Identify the bug and fix it."

### Time Budget
~45-60 minutes per chaos scenario investigation.

---

## Activity B2: Complete the Runbooks

### Objective

Use Devin to fill in the incomplete incident runbooks in `docs/runbooks/` based on codebase knowledge.

### What's Wrong

The runbooks in `docs/runbooks/` are skeleton documents. Each has a title, severity, alert reference, and symptoms — but the Investigation Steps, Resolution Steps, and Post-Incident sections are marked with `<!-- TODO -->` placeholders. The engineering team started writing them but never finished.

### Where to Look

- `docs/runbooks/high-error-rate.md` — Generic high error rate runbook
- `docs/runbooks/service-down.md` — Service down/unreachable runbook
- `docs/runbooks/high-latency.md` — High latency runbook
- `docs/runbooks/search-suggest-500.md` — Search suggest 500 errors (maps to chaos scenario)
- `docs/runbooks/file-upload-failure.md` — File upload failures (maps to chaos scenario)
- `docs/runbooks/notification-processing-failure.md` — Notification processing errors (maps to chaos scenario)
- `docs/runbooks/document-service-slow.md` — Document service latency (maps to chaos scenario)
- Service source code — Devin needs to read the actual service implementations to write accurate investigation and resolution steps

### What Done Looks Like

- [ ] At least 2 runbooks have complete Investigation Steps (specific commands, queries, dashboards to check)
- [ ] Resolution Steps reference actual code paths, configuration, or Redis keys
- [ ] Post-Incident sections include realistic action items (monitoring gaps, code fixes, process improvements)
- [ ] Runbook content is grounded in the actual codebase (not generic boilerplate)

### Hints

#### Hint 1 — Getting Started
Start with `docs/runbooks/search-suggest-500.md` — it maps directly to the chaos scenario you may have already investigated in B1.

#### Hint 2 — Specific Direction
Ask Devin to read the runbook skeleton, the corresponding service code, the alert rules, and the Grafana dashboard configuration. Then ask it to fill in the TODO sections with specific, actionable steps.

#### Hint 3 — Approach
Ask Devin: "Read `docs/runbooks/search-suggest-500.md` and the search-service code at `services/search-service/app/api/search.py`. Fill in the Investigation Steps, Resolution Steps, and Post-Incident sections based on the actual code. Reference specific files, functions, Redis keys, and Prometheus metrics."

### Time Budget
~15-20 minutes per runbook. Try to complete at least 2-3 in 45-60 minutes.

---

## Activity B3: Add Observability to Under-Instrumented Services

### Objective

Identify services with weak observability (missing metrics, no structured logging, no trace propagation) and add instrumentation.

### What's Wrong

Not all services have equal observability coverage. Some services have Prometheus metrics, structured logging, and OpenTelemetry trace propagation. Others are missing one or more of these. The observability gaps make it harder to investigate cross-service incidents.

### Where to Look

- Each service's source code — look for Prometheus metric definitions, logging configuration, and OpenTelemetry setup
- `observability/` — Prometheus, Grafana, and Jaeger configuration
- `docker-compose.yml` — Service definitions with environment variables for tracing
- Compare well-instrumented services (e.g., search-service with its `prometheus_client` metrics) against under-instrumented ones

### What Done Looks Like

- [ ] Identified at least one service with observability gaps
- [ ] Added missing Prometheus metrics (request count, duration histogram, error count)
- [ ] Added or improved structured logging (not `print()` or bare `puts`)
- [ ] Verified new metrics appear in the Prometheus targets
- [ ] Optionally: added a Grafana dashboard panel for the newly instrumented service

### Hints

#### Hint 1 — Getting Started
Ask Devin to audit each service for Prometheus metrics, structured logging, and OpenTelemetry tracing. Which services are missing which?

#### Hint 2 — Specific Direction
The analytics-service (Scala) and audit-service (C#) tend to have less observability coverage than the Python and Node.js services. Check those first.

#### Hint 3 — Approach
Ask Devin: "Compare the observability instrumentation in `services/search-service/` (well-instrumented) with `services/analytics-service/` (potentially under-instrumented). Add equivalent Prometheus metrics and structured logging to the under-instrumented service."

### Time Budget
~45-60 minutes to instrument one service. Focus on metrics first (most impactful), then structured logging.
