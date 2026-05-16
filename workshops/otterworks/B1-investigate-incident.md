# Lab: Investigate Production Incident

## Objective

Trigger a chaos scenario in the OtterWorks platform and experience three levels of incident response automation — from manual investigation to fully autonomous Devin-driven remediation. Use the **Auto-Investigate toggle** to control which flow is active.

## What's Wrong

OtterWorks has a chaos engineering framework that injects failures into production-like services. When chaos is active, services exhibit real failure modes: 500 errors on search suggestions, file upload rejections, notification processing failures, or document-service latency spikes. The admin dashboard's Incidents page has "Demo Controls" with chaos trigger buttons for each scenario.

The available chaos scenarios are:
1. **Search Suggest 500** — MeiliSearch ranking score enrichment path triggers a KeyError
2. **File Upload Failure** — File-service rejects uploads with a 500
3. **Notification Processing Failure** — Strict JSON schema parser rejects messages with integer timestamps
4. **Document Service Slow Queries** — 3-5 second latency injected before every database query

## Three Investigation Flows

The Incidents page has an **Auto-Investigate** toggle in the Demo Controls panel. This controls whether Grafana alerts automatically create Devin sessions or just create incidents for manual investigation.

### Flow 1: Manual Investigation (Auto-Investigate OFF)

1. Ensure Auto-Investigate is **OFF**
2. Click a chaos trigger button (e.g., "Break Search Autocomplete")
3. Open Grafana dashboards to see the error rate spike (~30s-2m)
4. An incident is auto-created on the Incidents page from the Grafana alert — but NO Devin session is launched
5. Investigate manually: read Grafana dashboards, Jaeger traces, and service logs
6. Optionally open [app.devin.ai](https://app.devin.ai) and paste your own investigation prompt

### Flow 2: One-Click from Incidents Page (Auto-Investigate OFF)

1. Ensure Auto-Investigate is **OFF**
2. Trigger chaos — an incident appears on the Incidents page with "No Devin session"
3. Click **"Launch Devin"** on the incident card
4. A Devin session is created via the API with full incident context
5. Follow the session link to watch Devin investigate

### Flow 3: Fully Automatic (Auto-Investigate ON)

1. Turn Auto-Investigate **ON**
2. Trigger chaos
3. Grafana detects the metric anomaly and fires an alert (~30s-2m)
4. The alert webhook hits the admin-service, which auto-creates an incident AND automatically launches a Devin session
5. The incident appears on the Incidents page with a Devin session already attached
6. Click "View Session" to watch Devin investigate and open a fix PR — zero human intervention after the chaos trigger

## Where to Look

- `frontend/admin-dashboard/src/app/pages/incidents/` — Incidents page with chaos triggers, Auto-Investigate toggle, and Devin session tracking
- `services/admin-service/app/controllers/api/v1/admin/alerts_controller.rb` — Grafana alert ingestion → incident creation → conditional Devin session
- `services/admin-service/app/controllers/api/v1/admin/incidents_controller.rb` — Manual incident creation and "Launch Devin" button handler
- `services/admin-service/app/services/devin_session_service.rb` — Devin API integration (builds prompts, calls POST /v3/.../sessions)
- `services/admin-service/app/services/admin_settings_service.rb` — Redis-backed Auto-Investigate toggle state
- `services/admin-service/app/controllers/api/v1/admin/chaos_controller.rb` — Chaos trigger/reset API
- `services/admin-service/app/services/chaos_probe_service.rb` — Synthetic traffic for metrics
- `observability/grafana/provisioning/alerting/alert-rules.yml` — Grafana alert rules for each chaos scenario
- `observability/grafana/provisioning/alerting/contact-points.yml` — Webhook contact point → admin-service
- `observability/grafana/dashboards/chaos-scenarios.json` — Grafana dashboard for chaos metrics
- `docs/runbooks/` — Skeleton runbooks for each scenario

## What Done Looks Like

### Flow 1 (Manual)
- [ ] Auto-Investigate toggle is OFF
- [ ] Triggered at least one chaos scenario via the admin dashboard
- [ ] Observed an incident auto-created from the Grafana alert (no Devin session attached)
- [ ] Used Grafana dashboards to identify which service is affected and what the symptom is
- [ ] Used Jaeger traces (or asked Devin manually) to identify the root cause
- [ ] Can explain the root cause: what code path fails, why, and how to fix it

### Flow 2 (One-Click)
- [ ] Saw an incident with "No Devin session" and a "Launch Devin" button
- [ ] Clicked "Launch Devin" and observed a Devin session being created
- [ ] Followed the session link and watched Devin investigate

### Flow 3 (Fully Automatic)
- [ ] Auto-Investigate toggle is ON
- [ ] Triggered chaos and observed the full pipeline: chaos → Grafana alert → auto-incident → auto-Devin session
- [ ] Can explain the event-driven pipeline: chaos → Prometheus metrics → Grafana alert rule → webhook → AlertsController → Incident + DevinSessionService → Devin API session

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started
Start with **Auto-Investigate OFF** and trigger the "Search Suggest 500" chaos scenario. Watch the Incidents page — within 30s-2m, an incident should appear from the Grafana alert. Then check the Grafana "Chaos Scenarios" dashboard to see the error rate spike.

### Hint 2 — Specific Direction
Ask Devin to read `services/search-service/app/api/search.py` and find the code path that runs when `chaos:search-service:suggest_500` is active. The bug is a missing key access on MeiliSearch results.

### Hint 3 — Approach
Ask Devin: "Read the chaos-related code in `services/search-service/app/api/search.py`, specifically the `suggest()` function. What happens when the chaos flag is active? Identify the bug and fix it."

### Hint 4 — Event-Driven Pipeline
Turn Auto-Investigate ON, trigger a different chaos scenario, and watch the Incidents page. The full pipeline (chaos → alert → incident → Devin session) should fire without any human intervention. Check admin-service logs (`docker compose logs admin-service`) to see the Devin API call.

## Time Budget
~45-60 minutes per chaos scenario investigation. Try Flow 1 first, then escalate to Flow 2 and Flow 3 to experience the full spectrum.
