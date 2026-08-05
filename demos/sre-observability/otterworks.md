# SRE Incident Response and Observability — OtterWorks Demo

A single linear demo for SREs, on-call engineers, and observability engineers. A
chaos scenario is injected into an ephemeral OtterWorks tenant, surfaces as a
Prometheus/Grafana alert, becomes an incident in the admin dashboard, and starts
a Devin session that finds the root cause and lands the fix. The same thread then
does the two pieces of on-call hygiene that never get done: grounding a skeleton
runbook in the code and alerts that actually exist, and closing a real
instrumentation gap in a weakly-observed service.

The whole loop is the SRE lifecycle — detect, triage, remediate, document,
instrument — run against a live polyglot platform rather than a slide.

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Detect and Remediate](#part-1)
  - [Act 1 — Stand up a tenant and see the golden app healthy](#act-1)
  - [Act 2 — Map the detection pipeline](#act-2)
  - [Act 3 — Inject chaos and watch it surface](#act-3)
  - [Act 4 — Devin investigates the incident and fixes it](#act-4)
- [Part 2 — Document and Instrument](#part-2)
  - [Act 5 — Ground the runbook in real code and alerts](#act-5)
  - [Act 6 — Close the observability gap in analytics-service](#act-6)
- [Part 3 — Fan Out the Remaining Backlog](#part-3)
- [Confirming Completion](#confirming-completion)
- [Where This Goes Next](#going-next)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

The golden app is already deployed and browsable:

```
https://t-main.otterworks.app          # user SPA
https://t-main.otterworks.app/admin    # admin dashboard (incidents, demo controls)
https://t-main.otterworks.app/api/health
```

The Prometheus / Grafana / Jaeger stack ships in `docker-compose.infra.yml`, so
the dashboards and traces are viewed from a local run of the platform:

```bash
cd otterworks
make up seed=1        # all services + infra (Postgres, Redis, LocalStack, MeiliSearch)
# Grafana     http://localhost:3001   (dashboards incl. incident-overview, chaos-scenarios)
# Prometheus  http://localhost:9090
# Jaeger      http://localhost:16686
# Admin dash  http://localhost:4200   Client app  http://localhost:3000
```

`t-main` is the perpetual reference tenant and is **chaos-forbidden**: the ops
dashboard rejects bug injection into a persistent tenant with a 409 (see
`demo-platform/dashboard/app/api/tenants/[id]/inject/route.ts`). Chaos always
goes into an ephemeral tenant.

---

<a id="repositories"></a>
## Repositories

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) — a
  polyglot collaborative file-storage and document-editing platform: 11 backend
  services (Go, Java 17, Rust, Python x2, Node.js, Kotlin, Scala, Ruby, C#, and
  an intentionally legacy Java 8 report-service) plus a React client app and an
  Angular admin dashboard, running on EKS behind a multi-tenant demo platform.
  The SRE surfaces used here: `observability/` (Grafana dashboards, Prometheus
  alerts and recording rules, Jaeger, OTel collector, Fluent Bit),
  `scripts/inject-bug.sh` with `scripts/bug-catalog.yaml`, `docs/runbooks/`, and
  the incident pipeline in `services/admin-service/`.

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Observability | Operability |
|---|---|---|
| **Before** | `search-service` exports `search_service_requests_total` and `search_service_request_duration_seconds` and logs JSON via `structlog`; `analytics-service` declares three Prometheus metrics that no code path ever records | all 7 files in `docs/runbooks/` are skeletons — each carries 3 `TODO` markers for Investigation, Resolution, and Post-Incident |
| **After** | `analytics-service` records its declared metrics on real request and event paths, so its `/metrics` endpoint returns moving series to the Prometheus job that already scrapes `analytics-service:8088` | the `search-suggest-500` runbook names the actual alert, metric, Redis key, log line, and code path an on-call engineer needs |

Two verification gates sit under this demo:

- **The alert is the gate for detection.** A fix is real when
  `SearchSuggestHighErrorRate` stops firing and the 5xx ratio on
  `search_service_requests_total` returns to zero — not when the diff looks
  plausible.
- **The test suites are the gate for change.** `cd services/search-service &&
  pytest` for the fix, `cd services/analytics-service && sbt test` plus
  `sbt scalafmtCheck` for the instrumentation work.

The golden app on `main` is durable; every chaos experiment and every fix lives
on a branch and its own ephemeral tenant, which is what makes this safe to repeat
and safe to run concurrently.

---

<a id="part-1"></a>
## Part 1 — Detect and Remediate

<a id="act-1"></a>
### Act 1 — Stand up a tenant and see the golden app healthy

Open `https://t-main.otterworks.app` and `https://t-main.otterworks.app/admin`.
The Incidents page is empty and `/api/health` is green — this is the reference
state everyone compares against for the rest of the demo.

Now create the environment that will get broken. Push a `demo-<id>` branch off
`main`; `.github/workflows/cd-tenant.yml` builds only the services that changed,
deploys through the ops dashboard, and creates the tenant with a 72h TTL on first
push:

```bash
cd otterworks
git checkout main && git pull
git checkout -b demo-sre01
git push -u origin demo-sre01
# CD publishes and deploys -> https://t-sre01.demo.otterworks.app
#                             https://t-sre01.demo.otterworks.app/admin
```

<a id="act-2"></a>
### Act 2 — Map the detection pipeline

Before breaking anything, have Devin explain the path a failure takes from a
metric to a Devin session. This is the piece most on-call engineers inherit
without ever seeing end to end.

```
In the Cognition-Partner-Workshops/otterworks repo, map the incident
detection pipeline end to end and write it up as an ordered list of
hops with the file and symbol at each hop.

Start from the SearchSuggestHighErrorRate rule in
observability/grafana/provisioning/alerting/alert-rules.yml (quote the
PromQL and explain which metric and labels it reads), follow the
webhook contact point in
observability/grafana/provisioning/alerting/contact-points.yml, then
services/admin-service/app/controllers/api/v1/admin/alerts_controller.rb
(process_alert: severity mapping, the dedup guard on open incidents,
and resolved-alert handling), the Auto-Investigate toggle in
services/admin-service/app/services/admin_settings_service.rb, and
services/admin-service/app/services/devin_session_service.rb.

Also explain where search_service_requests_total is recorded in
services/search-service/app/main.py, and what
services/admin-service/app/services/chaos_probe_service.rb exists for.
Note any environment variables the pipeline needs to reach the Devin
API.
```

Expected: a hop-by-hop trace — 5xx ratio over `search_service_requests_total` →
Grafana rule → `POST /api/v1/admin/alerts/ingest` → `Incident` record (deduped
per affected service) → `DevinSessionService` calling the Devin
`/v3/organizations/{org_id}/sessions` endpoint when `DEVIN_API_KEY` and
`DEVIN_ORG_ID` are set — plus the after-request hook in `main.py` that records
the metric, and the chaos probe that generates enough synthetic traffic for
`rate()` to move.

<a id="act-3"></a>
### Act 3 — Inject chaos and watch it surface

Inject the scenario into the ephemeral tenant. The `chaos` mechanism sets a flag
in that tenant's own in-cluster Redis with a TTL, so it is instant, scoped to one
namespace, and self-clearing:

```bash
./scripts/inject-bug.sh sre01 list                 # the catalog
./scripts/inject-bug.sh sre01 search-suggest-500   # SETEX chaos:search-service:suggest_500
```

Three surfaces now show the same failure:

1. **The product.** Type into the search bar at
   `https://t-sre01.demo.otterworks.app` — autocomplete stops returning
   suggestions.
2. **The dashboards.** On the local stack, the Grafana *Chaos Scenarios* and
   *Incident Overview* dashboards (`observability/grafana/dashboards/`) show the
   search-service error rate climbing; Prometheus at `:9090` confirms it directly
   with `sum(rate(search_service_requests_total{job="search-service",status=~"5.."}[1m]))`,
   and Jaeger at `:16686` shows the failing `/api/v1/search/suggest` spans.
3. **The incident.** Within roughly 30s–2m the Grafana alert fires, the webhook
   reaches `admin-service`, and an incident appears on the Incidents page at
   `/admin` for the tenant. With the Auto-Investigate toggle **on**, a Devin
   session is attached automatically; with it **off**, the incident card offers a
   **Launch Devin** button.

Never run this against `t-main` — the ops dashboard refuses it, and the whole
point of the perpetual tenant is that it stays the clean reference.

<a id="act-4"></a>
### Act 4 — Devin investigates the incident and fixes it

Click **Launch Devin** on the incident (or **View Session** if Auto-Investigate
already started one). The auto-generated session carries the incident context.
To run the same investigation deliberately, paste this:

```
An incident is open on the OtterWorks admin dashboard: the Grafana
alert SearchSuggestHighErrorRate is firing and search autocomplete
returns HTTP 500 on the tenant at
https://t-sre01.demo.otterworks.app. The chaos flag
chaos:search-service:suggest_500 is set in that tenant's Redis.

Working in Cognition-Partner-Workshops/otterworks, investigate and
fix it:

1. Read the suggest() handler in
   services/search-service/app/api/search.py and identify the exact
   code path that runs when the chaos flag is active, and the precise
   expression that raises. Explain why the failure is a 500 rather
   than a degraded response, and contrast it with how the non-chaos
   path handles errors.
2. Fix the handler so the ranking-score enrichment cannot crash the
   endpoint: request the ranking score explicitly where the search
   client is called, or sort defensively when the field is absent, and
   keep suggest() returning 200 with a suggestions list either way.
   Do not delete the enrichment feature.
3. Add a pytest case under services/search-service/tests/ that drives
   suggest() with results missing _rankingScore and asserts a 200 with
   a suggestions list. Run: cd services/search-service && pytest.
4. Report the failing expression, the fix, and the pytest output.
```

Expected root cause: the enrichment path sorts with
`key=lambda s: s["_rankingScore"]`, and MeiliSearch only returns `_rankingScore`
when it is explicitly requested — so the key lookup raises `KeyError`, the Flask
handler returns 500, and the after-request hook in `main.py` records that 5xx
into `search_service_requests_total`, which is exactly what the alert reads. The
non-chaos branch swallows failures and returns an empty suggestion list, which is
why only this path pages anyone.

Verify the way an SRE verifies. First the code gate:

```bash
cd services/search-service && pytest
```

Then the signal gate — clear the flag and confirm the alert resolves rather than
trusting the diff:

```bash
./scripts/inject-bug.sh sre01 reset
curl -s "https://t-sre01.demo.otterworks.app/api/v1/search/suggest?q=re" | head
# Prometheus: the 5xx ratio on search_service_requests_total returns to 0
# Incidents page: the alert's resolved webhook closes the incident
```

Push the fix to `demo-sre01`; CD rebuilds only `search-service` and redeploys the
tenant, so the fix is verifiable in a browser on the same host that showed the
outage. Devin Review comments on the PR, and the review thread is where the
change gets accepted before it reaches `main` and, from there, `t-main`.

---

<a id="part-2"></a>
## Part 2 — Document and Instrument

<a id="act-5"></a>
### Act 5 — Ground the runbook in real code and alerts

The incident just produced everything the runbook was missing. Every file in
`docs/runbooks/` is a skeleton with 3 `TODO` markers; fill the one that matches
this scenario using what the investigation established.

```
In Cognition-Partner-Workshops/otterworks, complete
docs/runbooks/search-suggest-500.md. It currently has TODO markers
for the rest of Investigation Steps, Resolution Steps, and
Post-Incident.

Ground every step in code and configuration that exists on main:

- The alert: the SearchSuggestHighErrorRate rule in
  observability/grafana/provisioning/alerting/alert-rules.yml, quoting
  its PromQL and threshold, plus the copy-pasteable Prometheus queries
  an on-call engineer should run against
  search_service_requests_total.
- The dashboards: the chaos-scenarios and incident-overview dashboards
  under observability/grafana/dashboards/, and the Jaeger view of
  /api/v1/search/suggest spans.
- The failing code: the suggest() handler in
  services/search-service/app/api/search.py, naming the expression
  that raises and the log event emitted by the non-chaos path.
- Resolution: how to confirm and clear the chaos flag
  (./scripts/inject-bug.sh <ATTENDEE_ID> reset), and the code-level
  fix with the pytest command that proves it.
- Post-Incident: concrete follow-ups tied to real gaps, including the
  fact that the non-chaos path returns 200 with an empty list on
  failure and so is invisible to a 5xx-ratio alert.

Keep the existing headings and severity. Use only paths, alert names,
metric names, and Redis keys that exist in the repo — no generic
boilerplate. Note in the runbook that chaos injection is for ephemeral
tenants only and that the perpetual tenant refuses it.
```

Expected: a runbook whose investigation section is a sequence of commands and
queries that work, whose resolution distinguishes "clear the injected flag" from
"ship the code fix", and whose post-incident section names the silent-failure
path the current alerting cannot see.

<a id="act-6"></a>
### Act 6 — Close the observability gap in analytics-service

Not every service is equally observable, and the gaps are invisible until an
incident crosses them. `analytics-service` (Scala) declares three Prometheus
metrics in `HealthRoutes.scala` — `analytics_events_received_total`,
`analytics_request_duration_seconds`, `analytics_active_connections` — and no
code path records any of them, so the Prometheus job that already scrapes
`analytics-service:8088` gets a `/metrics` endpoint with nothing moving in it.

```
In Cognition-Partner-Workshops/otterworks, close the observability
gap in services/analytics-service using services/search-service as
the reference implementation.

First, the audit. In
services/analytics-service/src/main/scala/com/otterworks/analytics/api/HealthRoutes.scala
the metrics analytics_events_received_total,
analytics_request_duration_seconds and analytics_active_connections
are declared but never recorded anywhere in the service. Confirm that,
and compare against services/search-service, where
app/api/health.py declares the metrics and app/main.py records them on
every request.

Then instrument:

1. Record analytics_request_duration_seconds (labels method, path,
   status) and analytics_active_connections for HTTP requests handled
   by the Akka HTTP routes in
   src/main/scala/com/otterworks/analytics/api/ — add a single
   reusable directive rather than repeating timing code per route.
2. Record analytics_events_received_total, labeled by event type,
   where events are actually accepted: AnalyticsService.trackEvent and
   the SQS path in
   src/main/scala/com/otterworks/analytics/service/EventProcessor.scala.
3. Keep the metric names, help text and label names exactly as
   declared so the existing Prometheus scrape config in
   observability/prometheus/prometheus.yml keeps working.
4. Add ScalaTest coverage under
   src/test/scala/com/otterworks/analytics/api/ asserting that a
   request increments the duration histogram and that tracking an
   event increments the counter for its type.
5. Note in your summary that docker-compose.yml sets
   OTEL_EXPORTER_OTLP_ENDPOINT and OTEL_SERVICE_NAME for
   analytics-service while build.sbt has no OpenTelemetry
   dependencies, so trace propagation is a separate follow-up. Do not
   add tracing in this change.

Verify with: cd services/analytics-service && sbt scalafmtCheck test
```

Expected: a directive-based timing wrapper on the routes, counters incremented on
both the HTTP and SQS ingestion paths, green `sbt test`, and a `/metrics` endpoint
whose series move when the service is exercised:

```bash
curl -s localhost:8088/metrics | grep -E 'analytics_(events_received_total|request_duration_seconds|active_connections)'
```

Scoping matters here: the change adds metrics that the existing scrape config
already expects, and defers tracing — the OTel environment variables are set for
the container but nothing in `build.sbt` consumes them, which is a real finding
worth naming rather than quietly fixing in the same PR.

---

<a id="part-3"></a>
## Part 3 — Fan Out the Remaining Backlog

Runbook debt and instrumentation debt are wide, shallow, and independent — the
shape of work that fans out. Run one orchestrator session that spawns a child
Devin session per item and monitors them, each on its own branch with its own
gate.

```
Act as the orchestrator for an SRE documentation and instrumentation
wave in Cognition-Partner-Workshops/otterworks, using child Devin
sessions to parallelize the work. Give each child the repo, its own
branch, and the reference material below.

Reference: docs/runbooks/search-suggest-500.md as the completed
runbook example, and services/search-service (metrics in
app/api/health.py recorded in app/main.py, structlog JSON logging) as
the instrumentation example.

Children:
1. Complete docs/runbooks/file-upload-failure.md, grounded in
   services/file-service and the FileUploadHighErrorRate rule in
   observability/grafana/provisioning/alerting/alert-rules.yml.
2. Complete docs/runbooks/document-service-slow.md, grounded in
   services/document-service and the DocumentServiceHighLatency rule.
3. Complete docs/runbooks/notification-processing-failure.md, grounded
   in services/notification-service and the
   NotificationConsumerProcessingErrors rule.
4. Complete docs/runbooks/service-down.md and
   docs/runbooks/high-latency.md against the ServiceDown,
   HighLatencyP95 and HighLatencyP99 rules in
   observability/prometheus/alerts.yml.
5. Audit trace propagation across services: which services set
   OTEL_EXPORTER_OTLP_ENDPOINT in docker-compose.yml but have no
   OpenTelemetry dependency or initialization in their build files,
   and report it as a prioritized gap list with file references.

Each child keeps the existing runbook headings, cites only paths,
alert names, metric names and Redis keys that exist on main, and
avoids generic boilerplate. Monitor the children until each is done
and summarize which alerts still have no grounded runbook.
```

Children inherit the organization's scoped credentials and each writes to its own
branch, so the parallel work never collides. Capturing the Act 5 runbook style as
a Knowledge note (or a playbook) is what makes every later session produce the
same shape of runbook instead of re-deriving it.

---

<a id="confirming-completion"></a>
## Confirming Completion

**1. The signal came back, not just the code.** Prometheus shows the 5xx ratio on
`search_service_requests_total` back at zero, `SearchSuggestHighErrorRate` is no
longer firing, and the incident on the tenant's `/admin` Incidents page is
resolved by the alert's resolved webhook.

**2. The product works on the host that showed the outage.** Autocomplete returns
suggestions at `https://t-sre01.demo.otterworks.app` after CD redeploys the
branch, and `cd services/search-service && pytest` is green with the new
missing-field test.

**3. The runbook is usable by someone who was not on the call.** Open
`docs/runbooks/search-suggest-500.md`: no `TODO` markers remain, and every
command, query, alert name, metric, and file path in it resolves against the
repo.

**4. The gap is closed and measurable.** `curl localhost:8088/metrics` on
`analytics-service` shows the three declared series moving, and
`sbt scalafmtCheck test` is green.

**5. The reference environment is untouched.** `https://t-main.otterworks.app`
still serves the golden app with an empty Incidents page — the chaos lived and
died inside the ephemeral tenant, which is why the demo is repeatable.

---

<a id="going-next"></a>
## Where This Goes Next

- **Event-driven response.** The Grafana alert webhook already creates the
  incident and, with Auto-Investigate on, starts the session — an alert becomes an
  investigation with no human in the loop. Devin
  [Automations](https://docs.devin.ai/product-guides/automations) extend the same
  pattern to ticket and CI events.
- **Scheduled hygiene.** Run the runbook-completeness and instrumentation audits
  on a cadence so the gap list stays short instead of being rediscovered during
  the next incident.
- **Shared context.** Knowledge notes and DeepWiki over the repo let each session
  start with the incident pipeline and observability conventions already
  understood, rather than rebuilding that map per page (coverage depends on repo
  structure).
- **Chaos as a rehearsal loop.** The remaining scenarios in
  `scripts/bug-catalog.yaml` — `file-upload-fails`, `document-slow`,
  `notification-schema`, `file-bad-bucket`, `code-variant` — each exercise a
  different alert, runbook, and service, on tenants that are TTL'd and reaped.
  Note that tenant SNS/SQS eventing is disabled by default, so scenarios that
  depend on queue consumers behave differently on a tenant than on the local
  stack.

---

<a id="key-takeaways"></a>
## Key Takeaways

- The detection pipeline is the demo: metric → alert rule → webhook → incident →
  Devin session. Devin joins the on-call workflow where the signal already lands
  instead of being a separate tool an engineer remembers to open.
- **Resolution is verified by the signal, not the diff.** The fix is done when the
  alert stops firing and the error-rate series returns to baseline, with a test
  that reproduces the failing condition — that is the SRE definition of done.
- Root cause is usually a small, findable expression. Here a sort key on a field
  the search client returns only when asked for it turned an enrichment feature
  into a 500-per-request outage, while the neighboring code path failed silently
  and paged no one — a gap the runbook now records.
- **Runbook and instrumentation debt is where SRE capacity goes to die.** It is
  wide, shallow, independent work that parallelizes across child sessions, and
  the output is grounded in real alert names, metrics, and code paths rather than
  boilerplate.
- Blast radius is a first-class design choice: chaos is scoped to one ephemeral
  tenant with a self-expiring flag, the perpetual reference environment refuses
  injection outright, and every fix travels through a branch, CD, a PR, and
  review before it reaches the golden app.
