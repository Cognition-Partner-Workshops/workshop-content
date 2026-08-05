# OtterWorks Data Plane Modernization — Demo

Modernize the data plane of a live product: five cron-driven Python ETL
scripts and a nightly batch usage rollup become orchestrated, tested,
event-driven pipelines. The deterministic usage-rollup baseline is the
correctness gate, so the modernization is measured by preserved results rather
than by how a rewritten pipeline looks.

| Phase | What Devin does | What proves it |
|---|---|---|
| **1. Assessment / lineage** | Maps the five ETL pipelines, analytics storage, schedules, and consuming surfaces | A file-grounded inventory and lineage map |
| **2. Pipeline migration** | Migrates one legacy pipeline into an orchestrated DAG with hooks, retries, logging, and pure-transform tests | Passing pytest tests and preserved S3/DynamoDB behavior |
| **3. Batch → event-driven** | Replaces the nightly usage rollup with an incremental event path | The incremental path reproduces the deterministic rollup baseline |
| **4. Serving gap / observability** | Serves missing metrics to the admin surface and adds freshness signals | Populated charts, API-flow coverage, and CI/alerting evidence |

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Before, After, and the Verification Loop](#before-after)
- [Phase 1 — Map the data plane and its lineage](#phase-1)
- [Phase 2 — Migrate the storage-cleanup pipeline](#phase-2)
- [Phase 3 — Re-architect the nightly rollup to event-driven](#phase-3)
- [Phase 4 — Serve the missing metrics and gate on freshness](#phase-4)
- [Fan out the remaining four pipelines](#fan-out)
- [Scheduled and event-driven automation](#automation)
- [Confidence = Programmatic Verification](#confidence)
- [Run the Produced Artifact](#run-artifact)
- [Concurrent Runs](#concurrent)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Run these commands from an `otterworks` checkout:

```bash
git clone https://github.com/Cognition-Partner-Workshops/otterworks
cd otterworks
make batch-usage-rollup-seed        # regenerate the deterministic seed events
make batch-usage-rollup OUT=/tmp/usage-rollup.json
```

The bundled seed contains 165 events across 2024-03-01 through 2024-03-03.
The expected result is three daily rollups: 55 events per day, 8 active users,
6 MiB allocated, 2 MiB released, and 4 MiB net per day.

`make batch-usage-rollup` runs sbt. Dependency resolution can fail when the
sbt/Maven mirrors rate-limit the box; the observed failure was HTTP 429 while
fetching `org.scala-sbt:sbt:1.9.9`. `make batch-usage-rollup-seed` is the
pure `python3` seed-generation command and is available independently of sbt.

The root `make test` target currently references the nonexistent
`frontend/web-app` directory. Use the per-service commands or
`make test-api-flows` instead.

The live checks for the golden app are:

```bash
curl -s https://t-main.otterworks.app/api/health
# {"status":"healthy","service":"web-app"}

curl -s -o /dev/null -w '%{http_code}\n' \
  https://t-main.otterworks.app/api/v1/analytics/dashboard
# 401
```

The 401 shows that the analytics serving path is live and auth-gated.

---

<a id="repositories"></a>
## Repositories

This walkthrough uses one repository:

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) —
  the polyglot product repository with 11 backend services, the React
  `frontend/client-app`, and the Angular `frontend/admin-dashboard`.

The data plane includes:

- `etl/scripts/` — five legacy cron-driven Python pipelines.
- `etl/config.ini` — the committed legacy configuration file.
- `etl/crontab` — the five independent schedules.
- `etl/ETL_UPGRADE_GUIDE.md` — the migration axes and script-to-DAG mapping.
- `services/analytics-service` — Scala event ingestion, PostgreSQL
  `analytics_events` and `analytics_daily_metrics`, and
  `batch/UsageRollupJob`.
- `services/admin-service` — the Rails metrics aggregator that feeds the
  admin dashboard.

The sibling demo
[Synthetic Test-Data Generation](synthetic-testdata-generation-demo.md)
covers synthetic test-data generation and the `testdata/` validation harness in
the same repository. This walkthrough does not repeat that material. The repo
Skill `.agents/skills/synthetic-testdata-generation/SKILL.md` belongs to that
sibling demo; `make testdata-setup-schema`, `make testdata-validate`, and
`make testdata-clean` are referenced there rather than here.

The live golden app is
[https://t-main.otterworks.app](https://t-main.otterworks.app); its user SPA is
at `/` and health is `/api/health`. `t-main` tracks perpetual `main` and must
not receive chaos injection or destructive changes. Ephemeral tenants come
from `workshop-<id>` or `demo-<id>` branches through
`.github/workflows/cd-tenant.yml`.

`https://t-main.otterworks.app/admin` serves the client SPA shell, so the
Angular admin dashboard where the analytics page lives is run from the repo
with `make dev-admin` on `:4200`, route `/analytics`.

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Pipelines | Serving surface |
|---|---|---|
| **Before** | Five monolithic scripts under `etl/scripts/`, plaintext credentials in `etl/config.ini`, independent cron entries, broad exception handling, timestamped `print()` logging, and no ETL tests | The admin dashboard reads Rails admin metrics. Two analytics cards are empty even though related analytics aggregates exist in the Scala plane |
| **After** | Orchestrated, tested pipelines with credentials in Connections/Variables, retries, structured logging, and an incremental rollup path | File-type and peak-hour metrics travel from the analytics plane to the admin surface, with freshness signals and alerts |

The five legacy scripts have these measured sizes:

| Script | Lines | Current behavior |
|---|---:|---|
| `etl/scripts/analytics_daily.py` | 452 | Reads SQS and DynamoDB events, aggregates with pandas, and writes S3 plus PostgreSQL |
| `etl/scripts/search_reindex_weekly.py` | 319 | Rebuilds MeiliSearch document and file indexes from service APIs |
| `etl/scripts/user_activity_daily.py` | 255 | Reads PostgreSQL aggregates and per-user S3 data, then writes S3 reports |
| `etl/scripts/audit_archive_weekly.py` | 224 | Archives old DynamoDB events to S3 Glacier, deletes them, and writes a compliance report |
| `etl/scripts/storage_cleanup_daily.py` | 217 | Compares S3 objects with DynamoDB references, quarantines orphans, and writes a savings report |

Each has a single `main()` function. `etl/config.ini` contains committed AWS
keys, a database password, and a MeiliSearch key; the values themselves are not
reproduced in this document or in the assessment output.
`etl/crontab` contains five independent schedules without dependency
management. Bare `except:` blocks log-and-continue or silently skip errors:
`analytics_daily.py:68-85` and `:93-103`, `audit_archive_weekly.py:139-167`,
`search_reindex_weekly.py:45-72`, and `user_activity_daily.py:173-176`.
The scripts use timestamped `print()` calls rather than structured logging,
and there are zero tests under `etl/`.

`etl/airflow/` does not exist yet, even though `ARCHITECTURE.md` describes it.
`docs/SDLC-COVERAGE.md` records that gap. The orchestrated pipelines in this
walkthrough are what Devin produces live.

The serving gap is visible in the Angular source. The analytics page at
`frontend/admin-dashboard/src/app/pages/analytics/analytics.component.ts`,
route `/analytics`, renders five cards: Active Users Over Time, Storage Usage,
Top File Types, User Signups, and Peak Usage Hours. The service maps
`topFileTypes: []` and `peakHours: []` in
`frontend/admin-dashboard/src/app/core/services/admin-api.service.ts`.
The dashboard reads only `GET /api/v1/admin/metrics/summary` from Rails
`admin-service`; it does not call the Scala analytics endpoints. The Scala
analytics plane does compute `/api/v1/analytics/top-content` and
`/api/v1/analytics/storage`. This is a visible lineage gap: an aggregate exists
in the analytics plane without a serving path to the surface that needs it.

The verification loop starts with the deterministic seed and rollup numbers.
`UsageRollupAggregator` is pure and I/O-free; its
`UsageRollupAggregatorSpec.scala` and `UsageRollupJobSpec.scala` test behavior.
Any re-architecture must reproduce the same numbers. `activeUsers` is
distinct, not additive: an incremental implementation that adds per-batch
distinct users would report 55 per day instead of 8. The baseline numbers are
what surface that class of error.

---

<a id="phase-1"></a>
## Phase 1 — Map the data plane and its lineage

Use this read-only assessment to produce the estate map, schedule inventory,
and forward lineage from pipeline inputs to consuming surfaces.

```
Using the Cognition-Partner-Workshops/otterworks repo, perform a read-only
assessment of the data plane.
Inventory the five pipelines under etl/scripts/: sources, transforms, sinks,
and etl/crontab schedules. Include etl/config.ini and
etl/ETL_UPGRADE_GUIDE.md, identifying plaintext credentials without printing
their values.
Trace services/analytics-service ingestion, the analytics_events and
analytics_daily_metrics tables, the nightly UsageRollupJob, and the consuming
surfaces through services/admin-service/app/services/metrics_aggregator.rb to
frontend/admin-dashboard/src/app/pages/analytics/analytics.component.ts and
frontend/admin-dashboard/src/app/core/services/admin-api.service.ts.
Identify silent-failure gaps, computed-but-unserved aggregates, and the files
that prove each finding.
Expected output: a DATA_PLANE_ASSESSMENT.md-style per-pipeline table, schedule
table, lineage map, risks, and file-level evidence. Use DeepWiki over the repo
and capture a Knowledge note so later sessions typically inherit the estate
map; coverage depends on repo structure.
```

---

<a id="phase-2"></a>
## Phase 2 — Migrate the storage-cleanup pipeline

Use this prompt to turn the clearest extract/compare/act/report pipeline into
the first orchestrated DAG and establish the pure-transform test pattern.

```
In the Cognition-Partner-Workshops/otterworks repo, migrate
etl/scripts/storage_cleanup_daily.py into an orchestrated Airflow DAG under
etl/airflow/dags/.
Read etl/ETL_UPGRADE_GUIDE.md first. Split main() into extract, compare,
quarantine, and report tasks. Preserve the S3 key formats, the
otterworks-file-metadata DynamoDB table name, and the orphan predicate.
Replace inline boto3 and psycopg2 with S3Hook, DynamoDBHook, and PostgresHook.
Move values out of etl/config.ini into Airflow Connections and Variables.
Configure exponential-backoff retries, use Python logging, and keep task
boundaries explicit.
Create etl/airflow/requirements.txt and etl/airflow/docker-compose.yml. Add
pytest tests for pure transforms under etl/airflow/tests/ proving the key
formats, table name, and quarantine predicate.
Expected output: the DAG, requirements, compose file, pytest tests, and a
report showing pytest etl/airflow/tests passes. Do not reference
etl/config.ini from the DAG.
```

From the repo root, run this against the Airflow requirements produced in
`etl/airflow/requirements.txt`; `etl/airflow/` does not exist on a bare
checkout before Phase 2.

```bash
pytest etl/airflow/tests
```

The pure-transform split makes the pipeline testable without live services.
Review the produced PR with Devin Review and use the PR feedback loop.

---

<a id="phase-3"></a>
## Phase 3 — Re-architect the nightly rollup to event-driven

Use this prompt to replace the nightly batch boundary while retaining the
existing pure aggregation semantics and deterministic comparison point.

```
In the Cognition-Partner-Workshops/otterworks repo, re-architect the nightly
usage rollup described in docs/BATCH-USAGE-ROLLUP.md from batch processing to
an incremental event-driven path.
Use an EventBridge rule feeding SQS with a dead-letter queue, then a Lambda
performing an incremental rollup upsert. Reuse
services/analytics-service/src/main/scala/com/otterworks/analytics/batch/UsageRollupAggregator.scala
semantics, including distinct activeUsers and storage bytes.
Decommission the CronJob in
infrastructure/helm/analytics-service/templates/cronjob.yaml and its
0 2 * * * value in infrastructure/helm/analytics-service/values.yaml. Keep
the local batch path as the comparison baseline.
Add tests alongside
services/analytics-service/src/test/scala/com/otterworks/analytics/batch/UsageRollupAggregatorSpec.scala
and UsageRollupJobSpec.scala. Reproduce exactly three daily rollups: 55
events, 8 active users, 6 MiB allocated, 2 MiB released, and 4 MiB net per day.
Expected output: the event path, infrastructure changes, upsert logic, tests,
and a comparison report matching the deterministic batch result.
```

```bash
make batch-usage-rollup OUT=/tmp/usage-rollup.json
```

Compare batch JSON with incremental output and assert `activeUsers` is 8, not
the sum of per-batch distinct users. Sbt/Maven resolution can return HTTP 429
for `org.scala-sbt:sbt:1.9.9`; run the comparison once resolution succeeds.
Tenant SNS/SQS eventing is disabled by default on ephemeral tenants, so use
tests and the local comparison rather than changing `t-main`.

---

<a id="phase-4"></a>
## Phase 4 — Serve the missing metrics and gate on freshness

Use this prompt to connect the two missing analytics metrics to the admin
surface and add freshness signals for stalled or silently failing pipelines.

```
In the Cognition-Partner-Workshops/otterworks repo, complete two related
deliverables.
First, populate the empty Top File Types and Peak Usage Hours cards in
frontend/admin-dashboard/src/app/pages/analytics/analytics.component.ts.
Give the admin surface a real analytics-data serving path; do not hardcode
values or leave topFileTypes and peakHours empty in
frontend/admin-dashboard/src/app/core/services/admin-api.service.ts.
Preserve `/analytics` and document endpoint lineage.
Second, add freshness and data-quality signals under observability/. Add
Prometheus alerts and a Grafana dashboard beside the existing
observability/grafana/dashboards/ files for stalled runs, failed tasks,
unexpected counts, and stale timestamps. Connect them to the alert webhook,
admin-service incident, and optional automatic Devin session flow.
Expected output: serving code and tests for both metrics, observability
configuration, a lineage note, and a verification report with exact commands.
```

The dev server on `:4200` should render `/analytics` with the previously empty
Top File Types and Peak Usage Hours cards populated:

```bash
make dev-admin
```

The API-flow suite exercises the analytics surface, including ingestion,
dashboard, activity, document stats, top content, active users, storage, and
export:

```bash
make test-api-flows
```

`tests/api/test_audit_analytics_report_flow.py` covers ingestion, dashboard,
activity, document stats, top content, active users, storage, and export.
`.github/workflows/ci.yml` gates the resulting change.

The existing observability set is:

- `observability/grafana/dashboards/business-metrics.json`
- `observability/grafana/dashboards/chaos-scenarios.json`
- `observability/grafana/dashboards/incident-overview.json`
- `observability/grafana/dashboards/infrastructure.json`
- `observability/grafana/dashboards/otterworks-overview.json`
- `observability/grafana/dashboards/service-detail.json`

Add the new data-quality dashboard beside these files.

---

<a id="fan-out"></a>
## Fan out the remaining four pipelines

Use one parent session to apply the Phase 2 pattern consistently while child
sessions handle the remaining independent scripts.

| Session | Script | Schedule | What it produces |
|---|---|---|---|
| 1 | `etl/scripts/analytics_daily.py` | Daily 02:00 UTC | SQS/DynamoDB extraction, user/document/file/hour aggregates, S3 outputs, and PostgreSQL aggregates |
| 2 | `etl/scripts/audit_archive_weekly.py` | Sunday 03:00 UTC | Compressed DynamoDB archive in S3 Glacier, deletion, and compliance report |
| 3 | `etl/scripts/search_reindex_weekly.py` | Sunday 04:00 UTC | MeiliSearch document/file reindex and count validation |
| 4 | `etl/scripts/user_activity_daily.py` | Daily 05:00 UTC | PostgreSQL/S3 activity reports for admin-service consumption |

```
Act as the parent orchestrator for the remaining data-plane migrations in the
Cognition-Partner-Workshops/otterworks repo.
Spawn one child Devin session per script below. Give each child the script,
its schedule, the existing etl/ETL_UPGRADE_GUIDE.md, and the Phase 2 pattern:
separate extract/transform/load/report tasks, provider hooks, credentials in
Connections or Variables, retries, structured logging, and pure-transform
pytest tests.
1. etl/scripts/analytics_daily.py — daily 02:00 UTC; preserve SQS/DynamoDB
   extraction, user/document/file/hour aggregates, S3 outputs, and PostgreSQL
   aggregates.
2. etl/scripts/audit_archive_weekly.py — Sunday 03:00 UTC; preserve the
   compressed S3 Glacier archive, DynamoDB deletion, and compliance report.
3. etl/scripts/search_reindex_weekly.py — Sunday 04:00 UTC; preserve the
   MeiliSearch document/file indexes and count validation.
4. etl/scripts/user_activity_daily.py — daily 05:00 UTC; preserve the
   PostgreSQL/S3 activity reports consumed by admin-service.
Each child should produce its own migration artifacts and pure-transform test
report. Monitor the children, summarize failures and preserved business rules,
and report which per-pipeline pytest commands pass. Do not modify the
deterministic usage-rollup baseline.
```

---

<a id="automation"></a>
## Scheduled and event-driven automation

Use the scheduled prompt for recurring output-quality checks with the baseline
and freshness signals as its evidence.

```
Create a scheduled Devin session for the
Cognition-Partner-Workshops/otterworks repo that runs a nightly data-quality
sweep over the produced pipeline outputs.
Read the pipeline freshness metrics and the deterministic usage-rollup
comparison. If a task is stale, a count is outside its configured threshold,
or the incremental rollup differs from the batch baseline, create an
investigation report with the failing pipeline, task, output path, and exact
comparison result. Do not change t-main or inject chaos.
```

Use the alert-triggered prompt to carry the failing task and incident context
into an investigation session.

```
When a pipeline-failure or freshness alert reaches the OtterWorks
admin-service alert webhook, start a Devin session against
Cognition-Partner-Workshops/otterworks.
Give the session the failing pipeline, DAG/task, alert labels, output
timestamp, and relevant CI job context. Reproduce the failure, identify
whether it is a code, dependency, data-quality, or infrastructure issue, and
write an investigation report with the next verified action.
```

These signals plug into the existing alert → webhook → admin-service incident
→ optional automatic Devin session path.

---

<a id="confidence"></a>
## Confidence = Programmatic Verification

- The estate map is proven by a file-grounded inventory.
- Storage cleanup is proven by pure-transform pytest tests.
- Rollup semantics are proven when incremental output matches the 165-event
  deterministic baseline, including 8 distinct active users.
- The serving fix is proven by `make dev-admin`, `/analytics`, API tests, and
  chart-data assertions.
- Freshness is proven by Prometheus alerts, Grafana panels, and incidents.
- The useful standard is not "the script ran"; it is preserved business
  results, exposed lineage, and passing tests and operational checks.

---

<a id="run-artifact"></a>
## Run the Produced Artifact

The first block regenerates and runs the deterministic rollup baseline:

```bash
make batch-usage-rollup-seed
make batch-usage-rollup OUT=/tmp/usage-rollup.json
```

The second block runs the produced Airflow tests, API-flow verification, and
local admin surface:

```bash
pytest etl/airflow/tests
make test-api-flows
make dev-admin
```

---

<a id="concurrent"></a>
## Concurrent Runs

```bash
make batch-usage-rollup OUT=/tmp/rollup-a.json
make batch-usage-rollup OUT=/tmp/rollup-b.json
```

Ephemeral tenants isolate deployments from `workshop-<id>` or `demo-<id>`;
`t-main` tracks `main` and is never touched. Namespaced-schema concurrency
belongs to the sibling synthetic-testdata demo.

---

<a id="key-takeaways"></a>
## Key Takeaways

- Correctness is proven by a deterministic baseline and pure-function tests,
  not by the statement that a script ran.
- Plaintext credentials and silent-failure classes are found by reading the
  estate and tracing code, not by guessing from a dashboard.
- Aggregates that exist but are never served are a lineage problem visible in
  the browser and in the endpoint map.
- Distinct counts are not additive; the rollup baseline catches that failure
  mode before an incremental pipeline reaches production.
- Pipeline migration is independent per script, so a parent session can fan
  out child sessions while keeping one verification contract.
- Alerts and automations turn a one-off migration into an operating model:
  freshness signals, incident context, and scheduled or event-triggered Devin
  sessions keep the data plane reviewable.
