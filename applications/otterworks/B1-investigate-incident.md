# Lab: Investigate Production Incident

## Objective

Trigger a chaos scenario in the OtterWorks platform, investigate the resulting incident using Grafana dashboards and Jaeger traces, and identify the root cause with Devin's help.

## What's Wrong

OtterWorks has a chaos engineering framework that injects failures into production-like services. When chaos is active, services exhibit real failure modes: 500 errors on search suggestions, file upload rejections, notification processing failures, or document-service latency spikes. The admin dashboard has "Trigger Chaos" buttons for each scenario.

The available chaos scenarios are:
1. **Search Suggest 500** — MeiliSearch ranking score enrichment path triggers a KeyError
2. **File Upload Failure** — File-service rejects uploads with a 500
3. **Notification Processing Failure** — Strict JSON schema parser rejects messages with integer timestamps
4. **Document Service Slow Queries** — 3-5 second latency injected before every database query

## Where to Look

- `frontend/admin-dashboard/` — Admin UI with chaos trigger buttons
- `services/admin-service/app/controllers/api/v1/admin/chaos_controller.rb` — Chaos trigger/reset API
- `services/admin-service/app/services/chaos_probe_service.rb` — Synthetic traffic for metrics
- `observability/grafana/dashboards/chaos-scenarios.json` — Grafana dashboard for chaos metrics
- `observability/grafana/dashboards/incident-overview.json` — Cross-service incident overview
- `docs/runbooks/` — Skeleton runbooks for each scenario (investigation steps are incomplete)

## What Done Looks Like

- [ ] Triggered at least one chaos scenario via the admin dashboard
- [ ] Used Grafana dashboards to identify which service is affected and what the symptom is (error spike vs. latency spike)
- [ ] Used Jaeger traces (or asked Devin to read the service code) to identify the root cause
- [ ] Can explain the root cause: what code path fails, why, and how to fix it
- [ ] Optionally: fixed the root cause and verified the service recovers after chaos reset

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started
Start by triggering the "Search Suggest 500" chaos scenario from the admin dashboard. Then check the Grafana "Chaos Scenarios" dashboard to see the error rate spike.

### Hint 2 — Specific Direction
Ask Devin to read `services/search-service/app/api/search.py` and find the code path that runs when `chaos:search-service:suggest_500` is active. The bug is a missing key access on MeiliSearch results.

### Hint 3 — Approach
Ask Devin: "Read the chaos-related code in `services/search-service/app/api/search.py`, specifically the `suggest()` function. What happens when the chaos flag is active? Identify the bug and fix it."

## Time Budget
~45-60 minutes per chaos scenario investigation.
