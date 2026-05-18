# Lab: Complete the Runbooks

## Objective

Use Devin to fill in the incomplete incident runbooks in `docs/runbooks/` based on codebase knowledge.

## What's Wrong

The runbooks in `docs/runbooks/` are skeleton documents. Each has a title, severity, alert reference, and symptoms — but the Investigation Steps, Resolution Steps, and Post-Incident sections are marked with `<!-- TODO -->` placeholders. The engineering team started writing them but never finished.

## Where to Look

- `docs/runbooks/high-error-rate.md` — Generic high error rate runbook
- `docs/runbooks/service-down.md` — Service down/unreachable runbook
- `docs/runbooks/high-latency.md` — High latency runbook
- `docs/runbooks/search-suggest-500.md` — Search suggest 500 errors (maps to chaos scenario)
- `docs/runbooks/file-upload-failure.md` — File upload failures (maps to chaos scenario)
- `docs/runbooks/notification-processing-failure.md` — Notification processing errors (maps to chaos scenario)
- `docs/runbooks/document-service-slow.md` — Document service latency (maps to chaos scenario)
- Service source code — Devin needs to read the actual service implementations to write accurate investigation and resolution steps

## What Done Looks Like

- [ ] At least 2 runbooks have complete Investigation Steps (specific commands, queries, dashboards to check)
- [ ] Resolution Steps reference actual code paths, configuration, or Redis keys
- [ ] Post-Incident sections include realistic action items (monitoring gaps, code fixes, process improvements)
- [ ] Runbook content is grounded in the actual codebase (not generic boilerplate)

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started
Start with `docs/runbooks/search-suggest-500.md` — it maps directly to the chaos scenario you may have already investigated in B1.

### Hint 2 — Specific Direction
Ask Devin to read the runbook skeleton, the corresponding service code, the alert rules, and the Grafana dashboard configuration. Then ask it to fill in the TODO sections with specific, actionable steps.

### Hint 3 — Approach
Ask Devin: "Read `docs/runbooks/search-suggest-500.md` and the search-service code at `services/search-service/app/api/search.py`. Fill in the Investigation Steps, Resolution Steps, and Post-Incident sections based on the actual code. Reference specific files, functions, Redis keys, and Prometheus metrics."

## Time Budget
~15-20 minutes per runbook. Try to complete at least 2-3 in 45-60 minutes.
