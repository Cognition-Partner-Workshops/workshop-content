# Incident First Responder — From Page to Merged Fix With No Prompt

A production alert fires on the OtterWorks `document-service`. Nobody types a
prompt. Alertmanager posts the page to a Devin Automation webhook, a session
starts, and Devin does what a good on-call engineer does — reads the telemetry
before the code, reproduces the incident on its own machine, ships the smallest
fix, proves it under the same load that fired the alert, posts the RCA in the
alert's Slack thread, and opens a focused PR. Merging that PR into the sandbox
branch deploys the fix to an isolated tenant and the dashboards recover.

The session runs the `!incident_responder` Devin Playbook, whose source lives in
the target repo at `.workshop/playbooks/incident-responder.devin.md`. The
repo-specific mechanics (the Compose stack, the `make incident-*` targets, the
before/after gate) come from that repo's Skill at
`.agents/skills/incident-responder/SKILL.md`, which Devin auto-loads when working
there. The Automation itself is documented in `docs/incident-responder/automation.md`.

## Table of Contents

- [Quick Start](#quick-start)
- [Repository and Before-State](#repository)
- [Phase 1 — Orient Over the Estate](#phase-1)
- [Phase 2 — One Incident, End to End](#phase-2)
  - [The alert fires](#alert-fires)
  - [The unattended session](#unattended)
  - [Reproduction: the before gate](#before-gate)
  - [The fix](#the-fix)
  - [Proof: the after gate, and the divergence it caught](#after-gate)
- [Phase 3 — Fan Out to the Other Three Alerts](#phase-3)
- [Confidence = Programmatic Verification](#confidence)
- [Run the Produced Artifact](#run-artifact)
- [Confirm Completion in the Target Tools](#confirm)
- [Screen Recording](#recording)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Nothing is pasted in the normal path. The Automation's `start_session` action
uses this prompt (from `docs/incident-responder/automation.md`), with the
Alertmanager webhook JSON appended verbatim after it:

```text
!incident_responder

You have been paged. The Alertmanager webhook payload for the firing alert is appended below; it is the whole brief, and nobody will type a follow-up prompt. The paging service lives in @Cognition-Partner-Workshops/otterworks (the document-service; its `incident-responder` Skill is auto-loaded and has every command you need).

Work in this order and do not skip a step:
1. Read the payload: alert name, `service`, `scenario`, `severity`, the summary's metric and threshold, `startsAt`, and the `dashboard_url` / `traces_url` / `runbook_url` annotations. If the payload contains a Slack channel and message timestamp, that thread is where your RCA goes; otherwise the RCA goes in the PR description and your final message.
2. Telemetry before code. Open the dashboard and trace links from the annotations and write down three facts with numbers: the user-facing symptom (p95, error ratio, memory, duplicate windows), what is NOT changing (request rate flat, no deploy), and what the traces say the time or resource is spent on. If those hosts are unreachable from your machine, say so and get the same numbers from your local reproduction in step 3 instead.
3. Reproduce on your own machine: check out the repository, run `make incident-up`, then `make incident-arm SCENARIO=<scenario from the payload>` and `make incident-verify SCENARIO=<scenario> EXPECT=before`. The gate must go green (the alert fires locally and the before-thresholds are met) before you touch code. Open the local Grafana (http://localhost:3001, admin/otterworks) and Jaeger (http://localhost:16686) and capture the flat-traffic/rising-latency panel and the one trace that fans out — those two screenshots go in the PR.
4. Find the cause in code by following the span names and SQL text back to the function that emits them; check `git log -S` for when it arrived. Name the file, function and line.
5. Make the smallest fix that removes the cause: a query change, a migration if an index is missing (a new Alembic revision; never edit an existing one), and one regression test that pins the property the alert measured. No refactors, no changes to alert rules, thresholds, `incident/scenarios.yaml`, `incident/expected.yaml`, seeds or dashboards. Run the service's lint (`ruff`) and its focused tests.
6. Prove it with the same load: rebuild (`make incident-up`), run `make incident-verify SCENARIO=<scenario> EXPECT=after`, and record the before/after numbers side by side from the gate output (for n-plus-one that is p95 and SQL statements per request; for the others, the metric named in the alert). The alert must be inactive under the same load. If the gate is red, the fix is not done — read the trace for the new build and iterate; never edit the gate.
7. Open one PR from a new branch `devin/<unix-timestamp>-<alert-slug>` against the branch the paging tenant tracks (`demo-incident` for the sandbox tenant; `main` if the payload does not name one) with: what users saw, what telemetry showed, the root cause (file:function:line), the fix in one sentence, the before/after table, the two screenshots, and the exact gate commands you ran with their output. Keep the diff to the query change, the migration, the test and (if needed) the model/service code the query touches.
8. Post the RCA: one message in the alert's Slack thread if you have one (users saw / telemetry showed / root cause / fix / before-after / PR link), otherwise as your final message. Run `make incident-disarm` before you finish.

Never push to any branch other than your own, never merge, and never silence the alert (rule edits, threshold changes, inhibitions) as a fix. If you cannot reproduce the alert locally, stop and report exactly what you saw instead of guessing at a fix.
```

**If the Automation is unavailable**, run `make incident-simulate RECEIVER=devin`
in the repo to print the exact payload Alertmanager sent, and paste this into a
new Devin session with that JSON appended:

```
!incident_responder You have been paged in Cognition-Partner-Workshops/otterworks: DocumentListLatencyHigh (severity critical, service document-service, scenario n-plus-one) is firing — the p95 of GET /api/v1/documents/ is above 1 s while request rate is flat. Treat the Alertmanager payload appended below as the whole brief. Read the telemetry first (Grafana dashboard otterworks-incident-responder, Jaeger traces for document-service), then reproduce locally with make incident-up, make incident-arm SCENARIO=n-plus-one and make incident-verify SCENARIO=n-plus-one EXPECT=before, follow the SQL spans back to the function in services/document-service/app/services/document_service.py that emits them, make the smallest fix (one batched query, one new Alembic revision under services/document-service/alembic/versions/ if an index is missing, one regression test under services/document-service/tests/ pinning the statement count), rebuild and prove it with make incident-verify SCENARIO=n-plus-one EXPECT=after under the same load, and run make incident-disarm before finishing. Expected output: a PR against the demo-incident branch containing the RCA (what users saw, what telemetry showed, root cause as file:function:line, the fix in one sentence, a before/after table of p95 and SQL statements per request, and the two gate commands with their output) and the same RCA as one reply in the alert's Slack thread if the payload names a channel and message timestamp, otherwise as your final message. Do not change alert rules, thresholds, incident/scenarios.yaml, incident/expected.yaml, seeds or dashboards, and never push to any branch other than your own.
```

---

<a id="repository"></a>
## Repository and Before-State

**Repository.** [Cognition-Partner-Workshops/otterworks](https://github.com/Cognition-Partner-Workshops/otterworks).
`main` carries the before-state: four production flaws in
`services/document-service/` (Python 3.12, FastAPI, SQLAlchemy async, Alembic)
that stay on `main` on purpose. A fix lives on the responder's own branch. The
sandbox branch **`demo-incident`** is `main` plus whatever has been merged for
this run; pushing to it deploys the isolated tenant `otterworks-incident`
(`https://t-incident.otterworks.app`, `https://api-t-incident.otterworks.app`)
through `.github/workflows/cd-tenant.yml`. The perpetual golden tenant
`t-main.otterworks.app` is never seeded, injected, or mutated.

**Local stack.** `make incident-up` brings up document-service with Postgres and
Redis, plus Prometheus, Grafana, Jaeger, Alertmanager and an alert sink — all on
loopback: document-service `8083`, Prometheus `9090`, Grafana `3001`
(`admin` / `otterworks`, dashboard `/d/otterworks-incident-responder`), Jaeger
`16686`, Alertmanager `9093`, alert sink `9095`. `make incident-up UI=1` adds
api-gateway `8080` and web-app `3000` so the slowness is visible in a browser.

**The flaw this thread follows.** `n-plus-one`: `DocumentService.list_documents`
in `services/document-service/app/services/document_service.py` runs one
`document_versions` query per document in a Python loop, and the schema lacks an
index covering the owner listing order on `documents` and a composite index on
`document_versions(document_id, version_number)`. On a 100-row page that is 104
SQL statements per request and a p95 around 2.4 s while request volume stays
flat. Nothing errors, so nobody files a bug — until the alert does.

**Telemetry the service exports** (`app/telemetry.py`): `http_requests_total`,
`http_request_duration_seconds`, `otterworks_db_queries_per_request`,
`otterworks_db_query_duration_seconds`, process memory and memory limit,
request-log bytes and capacity, render-cache entries, rollup runs and duplicate
windows. Every response also carries `X-DB-Queries` and `X-Request-Duration-Ms`
headers, so a single `curl -i` shows the fan-out.

---

<a id="phase-1"></a>
## Phase 1 — Orient Over the Estate

Open `incident/scenarios.yaml`. It is the machine-readable catalog: for each
armable flaw, what the user sees, what the chart shows, the code cause, the
correct fix, the load profile, the paging alert and its time-to-fire promise,
and the before/after thresholds the gate holds the stack to.

| Scenario | Paging alert (`page: devin`) | Fires within | Code cause | Fix shape |
|---|---|---|---|---|
| `n-plus-one` | `DocumentListLatencyHigh` (p95 of `GET /api/v1/documents/` > 1 s for 1 m) | 180 s | `app/services/document_service.py` — one versions SELECT per document; two missing indexes | one batched versions query, one Alembic migration, one test pinning the query count |
| `log-flood` | `RequestLogVolumeNearFull` (log bytes / capacity > 0.80 for 30 s) | 240 s | `app/middleware/request_log.py` — unbounded JSONL append, raises on write failure | bounded rotation, logging never fails the request, test past the cap |
| `cache-leak` | `DocumentServiceMemoryHigh` (RSS / limit > 0.75 for 1 m) | 420 s | `app/services/render_cache.py` — plain dict, no eviction | bounded LRU or TTL, test past the bound |
| `double-run` | `DocumentStatsRollupDuplicated` (duplicate windows > 0) | 240 s | `app/jobs/stats_rollup.py` — rollup scheduled in every process, no lock | advisory lock or unique `window_start`, test running two rollups concurrently |

`n-plus-one` is on regardless of any flag — it is the flaw the code shipped
with. The other three are gated behind Redis chaos flags or a second replica, so
they are off until armed. `DocumentListQueryFanout` (SQL statements per request
> 20) is diagnostic: it fires alongside `DocumentListLatencyHigh` but does not
page. Alert rules are in `observability/prometheus/incident_alerts.yml`;
routing (`page: devin` → Devin webhook and Slack, everything else → the local
sink) is in `observability/alertmanager/alertmanager.yml.tmpl`.

Two documents drive the session. The Playbook
(`.workshop/playbooks/incident-responder.devin.md`) is the *order of work* —
telemetry first, then reproduce, then code, and a fix is proven by the alert
clearing under the same load that fired it. The Skill
(`.agents/skills/incident-responder/SKILL.md`) is the *mechanics* — the exact
`make` targets, ports, Prometheus queries, and the reset. The Automation
(`docs/incident-responder/automation.md`) is the *trigger*: event
`webhook:incoming`, action `start_session` with the prompt above, Normal agent
mode, ACU limit 15, at most 3 invocations per hour, one concurrent run, and a
network allowlist limited to GitHub, container registries, PyPI/astral, the
Debian/Ubuntu mirrors and `*.otterworks.app`. No observability MCP connector is
required: the local Prometheus/Grafana/Jaeger fallback is the default, and a
Grafana, Datadog or Sentry MCP connector is the upgrade path for a real tenant.

---

<a id="phase-2"></a>
## Phase 2 — One Incident, End to End

<a id="alert-fires"></a>
### The alert fires

```bash
make incident-up
make arm SCENARIO=n-plus-one
```

`arm` seeds the deterministic dataset (one owner, 400 documents, 8 versions
each, `random_seed: 20260924`) and starts the load profile from the catalog:
`GET /api/v1/documents/?size=100`, 24 concurrent, 24 rps *offered*. The
degraded service serves only about 11.5 rps — offered and served diverge, which
is itself a symptom.

Open Grafana at `http://localhost:3001/d/otterworks-incident-responder`. The
three panels to watch: **request rate** (flat), **p95 latency** (climbing from
tens of milliseconds toward 2.4 s), and **SQL statements per request** (stepping
from 4 to 104). One request, one row:

```bash
curl -si 'http://localhost:8083/api/v1/documents/?size=100' -H "Authorization: Bearer $TOKEN" | grep -i 'X-DB-Queries\|X-Request-Duration-Ms'
```

Measured while degraded: `X-DB-Queries: 104`, `X-Request-Duration-Ms` ≈ 1938.

`DocumentListLatencyHigh` (`page: devin`) fired about 87 s after `make arm` in
the authoring runs; the catalog's promise is `fires_within_seconds: 180`.
`DocumentListQueryFanout` fires alongside it. In Alertmanager
(`http://localhost:9093`) both show as firing; only the paging one is routed to
the Devin receiver, and only with `status: firing` (`send_resolved: false`), so
resolved notifications and diagnostic alerts never start a session.

Open Jaeger (`http://localhost:16686`), search `document-service` with
`minDuration=1s`, and open one trace. Measured: a ≈ 2.3 s trace with 111
spans, 104 of them SQL SELECTs — 100 of those the per-document
`document_versions` query. This waterfall is the finding; the code is where it
comes from.

<a id="unattended"></a>
### The unattended session

Alertmanager posts the firing alert to `DEVIN_WEBHOOK_URL` — the Automation's
incoming-webhook URL — and, when `SLACK_WEBHOOK_URL` is set, to the alert
channel. The Automation starts a session with the prompt above and the payload
appended. Nobody has typed anything.

What the session does, in order, and what to look for in its timeline:

1. **Acknowledges in the thread** — one line ("Investigating
   `DocumentListLatencyHigh` on `document-service`; reading traces now"), then
   nothing else in the thread until it has a root cause.
2. **Reads telemetry before code** — writes down three facts with numbers: the
   symptom (p95 ≈ 2.4 s against a 1 s threshold), what is *not* changing
   (request rate flat, no deploy), and where the time goes (one request fanning
   into ~100 identical SELECTs on `document_versions`). Flat volume with rising
   latency is a per-request cost growing with data, not capacity; the RCA says
   which.
3. **Reproduces on its own machine** (next section) before touching code.

If the session opens `document_service.py` first and reasons from the code
alone, that is the wrong order — the Playbook's forbidden-actions list exists so
that the finding comes from the trace, not from a plausible reading of the diff.

<a id="before-gate"></a>
### Reproduction: the before gate

```bash
make incident-verify SCENARIO=n-plus-one EXPECT=before
```

The gate is fail-closed. It checks that the fixture fingerprint matches
`incident/expected.yaml`, that the source fingerprint matches the recorded
before-state, that `DocumentListLatencyHigh` fired within 180 s, that the
before-thresholds hold (`queries_per_request_min: 50`, `p95_seconds_min: 1.0`),
and that the webhook was captured. Green here **is** the reproduction: the
session's machine sees the same incident the alert saw. Measured on the
authoring runs: p50 ≈ 1.89 s, p95 ≈ 2.43 s, p99 ≈ 2.49 s, 104 statements per
request.

Every run — red or green — writes
`incident/reports/n-plus-one-before-<stamp>.json`; the report file names go in
the PR.

<a id="the-fix"></a>
### The fix

Guided by the span names and the SQL text, the session lands in
`DocumentService.list_documents` in
`services/document-service/app/services/document_service.py` and ships the
smallest change that removes the cause:

- **One batched query** for the recent versions of the whole page (a window
  function), replacing the per-document loop.
- **One Alembic migration** — a new revision under
  `services/document-service/alembic/versions/`, never an edit to
  `001_initial_schema.py` — adding `ix_documents_owner_listing` and
  `ix_document_versions_document_version_desc`.
- **One regression test** under `services/document-service/tests/` asserting
  the list endpoint issues a constant number of statements regardless of page
  size.

No refactor, no drive-by cleanups, no change to alert rules, thresholds, the
scenario catalog, seeds or recorded evidence. Lint (`ruff`) and the focused
tests pass; the nine pre-existing failures in `tests/test_documents_api.py` on
`main` are reported as pre-existing, not fixed and not hidden.

<a id="after-gate"></a>
### Proof: the after gate, and the divergence it caught

```bash
docker compose -f docker-compose.yml -f docker-compose.infra.yml -f docker-compose.incident.yml up -d --build document-service
make incident-verify SCENARIO=n-plus-one EXPECT=after
```

The `after` gate refuses to run against an unchanged source fingerprint, so it
cannot be passed by waiting for the load to stop, and it drives the *same* load
profile (24 concurrent, 24 rps) for its soak. What happened on the authoring
run is the beat that makes this credible, and it was not staged.

The correct fix was **rejected**:

```
PASS alert DocumentListLatencyHigh is inactive under the same load
PASS p95_seconds=0.125 <= 0.5
FAIL queries_per_request=5.000 <= 4
```

The alert had cleared and p95 had fallen from 2.4 s to 125 ms, so the fix
worked. The question was whether the fifth statement was a leftover fan-out or
a wrong ceiling. The session did not shave a query to hit the number and did
not edit the threshold. It read the Jaeger trace for the new build: **count,
page, the two relationship loads the page query had always made (versions and
comments), and the one batched recent-versions query** — five statements,
genuinely. The catalog's ceiling of `4` had been authored as "count + page +
versions" from memory; the before-state already issued 4 statements before the
loop started, and 104 on a 100-row page. The gate was right to stop, the fix
was right, and the contract was wrong.

The correction went to the root, not to the evidence: `incident/scenarios.yaml`
`after.queries_per_request_max` `4 → 5`, re-pinned with
`make incident-record REASON="..."` so the reason is committed in
`incident/expected.yaml`:

> after gate rejected the correct fix: queries_per_request_max was authored as 4
> (count + page + versions) but the page query also selectin-loads versions and
> comments, so the before-state base is 4 and a batched fix lands at 5; ceiling
> raised to 5 and the catalog text corrected to the measured 4 -> 104 step. No
> seed, load or alert-rule change

Re-run: green. Measured after: p95 ≈ 0.13–0.21 s, 5 SQL statements per request,
alert inactive under the same load. The same run also settled which part did
what: with the index migration rolled back the batched query alone brought p95
to 0.21 s; with the indexes applied it was 0.125 s. The RCA says the query
change carried the recovery and the indexes keep the owner listing and the
per-document version lookup off sequential scans as the tables grow.

The session then posts the RCA as one reply in the alert's Slack thread — what
users saw, what telemetry showed, root cause with file and function, the fix in
one sentence, the before/after numbers, the PR link — opens the PR against
`demo-incident` with the two gate summary lines and the report file names, and
runs `make incident-disarm`.

---

<a id="phase-3"></a>
## Phase 3 — Fan Out to the Other Three Alerts

Nothing in the Automation is specific to the N+1. Alertmanager routes every
`page: devin` alert to the same receiver, and the same `!incident_responder`
Playbook answers `RequestLogVolumeNearFull`, `DocumentServiceMemoryHigh` and
`DocumentStatsRollupDuplicated` with the same order of work — payload, telemetry,
`EXPECT=before`, smallest fix, `EXPECT=after`, RCA, PR. Each scenario has its own
load profile, its own before-thresholds and its own paging alert in the catalog,
and each responder works on its own `devin/<timestamp>-<alert-slug>` branch, so
three pages arriving together are three independent PRs rather than one
entangled one. Fanning out by hand looks like this, from a parent session:

```
In Cognition-Partner-Workshops/otterworks, start three child sessions in parallel, one per scenario in incident/scenarios.yaml other than n-plus-one (log-flood, cache-leak, double-run). Each child runs the !incident_responder playbook against its scenario exactly as an Alertmanager page would: make incident-up, make incident-arm SCENARIO=<name>, make incident-verify SCENARIO=<name> EXPECT=before, the smallest fix in the file named under code_cause, one regression test under services/document-service/tests/, rebuild, make incident-verify SCENARIO=<name> EXPECT=after, then make incident-disarm, each on its own devin/<timestamp>-<alert-slug> branch with its own PR against demo-incident carrying the RCA and the before/after gate output. Do not change alert rules, thresholds, incident/scenarios.yaml or incident/expected.yaml. Report back one table: scenario, alert, before number, after number, PR link.
```

The Automation's concurrency setting is one running session and a queue depth
of zero, on purpose: two responders on one page would collide on the same
branch prefix and the same tenant. Parallelism belongs to distinct alerts, not
to duplicate pages.

---

<a id="confidence"></a>
## Confidence = Programmatic Verification

Every claim in the RCA is a number the gate measured, not an estimate:

- **`make incident-verify SCENARIO=<name> EXPECT=before`** — fixture pinned,
  alert fired in time, before-thresholds met, webhook captured. Green means
  "the incident is reproduced here."
- **`make incident-verify SCENARIO=<name> EXPECT=after`** — source changed,
  same load, alert clears, after-thresholds met. Red means "the fix is not
  done" — or, as above, "the contract is wrong," and the trace arbitrates.
- **Fail-closed fingerprints** — `make incident-fingerprint` compares the
  fixture and source fingerprints to `incident/expected.yaml`. A drifted
  fixture reports fixture drift instead of a green run; an unchanged source
  refuses the `after` gate.
- **Audited re-record** — the only way to change what the gate expects is
  `make incident-record REASON="..."`, and the reason is committed. A red gate
  is either a real divergence or a fixture defect; fix the cause, never the
  measurement.
- **The alert is the SLO** — the number that paged (p95) going back under its
  threshold while the same load is running is what "fixed" means. Silencing
  the rule, raising the threshold or adding an inhibition is on the Playbook's
  forbidden list.

---

<a id="run-artifact"></a>
## Run the Produced Artifact

Merge the PR into `demo-incident`. `.github/workflows/cd-tenant.yml` rebuilds
only the changed service and deploys it to the `otterworks-incident` tenant;
migrations run on container start, so the new Alembic revision is applied by the
rollout. Under the same load the tenant's p95 falls, statements per request drop
from 104 to 5, and `DocumentListLatencyHigh` resolves. On the browser view
(`https://t-incident.otterworks.app`, or `http://localhost:3000` with
`make incident-up UI=1`), "My documents" opens without the spinner.

---

<a id="confirm"></a>
## Confirm Completion in the Target Tools

| Tool | Where to look | What confirms completion |
|---|---|---|
| **Grafana** | `/d/otterworks-incident-responder` | Request rate still flat; p95 falling from ≈ 2.4 s to ≈ 0.13–0.21 s; SQL statements per request stepping 104 → 5 |
| **Alertmanager** | `http://localhost:9093` (or the tenant's) | `DocumentListLatencyHigh` and `DocumentListQueryFanout` no longer firing |
| **Jaeger** | search `document-service`, `GET /api/v1/documents/` | A trace for the new build with 5 SQL spans instead of 104 |
| **GitHub** | the PR against `demo-incident` | Query change + one migration + one test; before/after table (p95 2.43 s → 0.13 s, 104 → 5 statements); the two gate summary lines and report file names; the two screenshots |
| **Slack** | the alert's thread | One acknowledgement, one RCA reply with the PR link — not a new message, not a DM |

---

<a id="recording"></a>
## Screen Recording

`<recording>` — the recording of the authoring run. Read it as two segments:

- **First ≈ 13 s — the golden tenant `t-main.otterworks.app`, read-only.** It
  shows the tenant is reachable and that the API rejects unauthenticated calls
  (HTTP 401). It does **not** show the slowness: `main`'s tenant holds only a
  couple of documents and is never seeded or injected.
- **The rest — the isolated local reproduction.** Flat traffic and rising p95 on
  the Grafana dashboard, `X-DB-Queries: 104` on a single request, the Jaeger
  trace fanning into the per-document SELECTs, and both
  `DocumentListLatencyHigh` and `DocumentListQueryFanout` firing.

The three on-screen moments that carry the thread: the flat-traffic /
rising-latency chart, the trace waterfall fanning into ~100 queries, and the
before/after p95 on one chart after the fix is deployed.

---

<a id="key-takeaways"></a>
## Key Takeaways

- The brief was the page. No prompt was typed; the Automation turned an
  Alertmanager webhook into a session and the Playbook supplied the order of
  work.
- Telemetry came before code. The flat-rate/rising-p95 panel said *that*, the
  trace waterfall with ~100 identical SELECTs said *why*, and the code was only
  where the cause lived.
- Reproduction was mandatory and programmatic: a green `EXPECT=before` on the
  responder's own machine, under the catalog's load profile, before any diff.
- The gate rejected a correct fix, and the trace arbitrated. The response was
  to correct the contract at its root with an audited reason — not to shave a
  query, not to raise a threshold, not to edit evidence.
- "Fixed" means the number that paged went back under its threshold under the
  same load: p95 ≈ 2.43 s → ≈ 0.13 s, 104 → 5 statements per request, alert
  inactive — then a reviewable PR and one RCA reply in the thread.
- The same wiring answers the other three alerts on `main`, each on its own
  branch; a Grafana, Datadog or Sentry MCP connector is the upgrade path from
  the local telemetry stack to a real tenant, with no prompt change.
