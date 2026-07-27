# Live Schema Change and Query Regression — Database Operations Demo

This walkthrough follows a document-list latency regression from alert to query,
then through pool review, an expand/contract change, and review automation in
## Table of Contents

- [Quick Start](#quick-start)
- [Repository](#repository)
- [Part 1 — From p95 alert to the query](#part-1)
- [Part 2 — Fan out across services](#part-2)
- [Part 3 — Expand/contract schema change](#part-3)
- [Part 4 — Devin Review as the migration gate](#part-4)
- [Part 5 — Wire the trigger](#part-5)
- [Part 6 — Shared context layer](#part-6)
- [Before / After](#before-after)
- [What Still Needs a Human](#what-still-needs-a-human)
- [Key Takeaways](#key-takeaways)

<a id="quick-start"></a>
## Quick Start

Run the first investigation prompt against
`Cognition-Partner-Workshops/otterworks`:

```text
In Cognition-Partner-Workshops/otterworks, trace the document-service latency
regression from the Grafana alert uid document-service-high-latency through
the API route to list_documents() in
services/document-service/app/services/document_service.py.
services/api-gateway/internal/proxy/router.go

Read these exact files:
services/document-service/app/services/document_service.py
services/document-service/app/models/document.py
services/document-service/alembic/versions/001_initial_schema.py
services/document-service/alembic/env.py
observability/grafana/provisioning/alerting/alert-rules.yml
observability/grafana/provisioning/alerting/contact-points.yml
observability/grafana/dashboards/infrastructure.json

Write docs/db/SLOW_QUERY_ANALYSIS.md with:
1. the offending access pattern;
2. the per-page query count as a function of page size;
3. missing-index findings from 001_initial_schema.py;
4. an EXPLAIN-able single-query rewrite using a lateral join or window
   function instead of the Python loop; and
5. index candidates with rationale.

Return a concise summary of the findings, the proposed SQL shape, and the
created artifact path. Do not execute production DDL.
```

```bash
./scripts/inject-bug.sh <ATTENDEE_ID> document-slow
./scripts/inject-bug.sh <ATTENDEE_ID> reset
```

<a id="repository"></a>
## Repository

`Cognition-Partner-Workshops/otterworks` has 12 service directories, one shared
RDS PostgreSQL instance, DynamoDB tables, and a shared Redis replication group.

| Service | Data store | Access layer | Pool/configuration | Migration tool |
|---|---|---|---|---|
| `auth-service` | PostgreSQL | Spring Data JPA/Hibernate | Hikari in `services/auth-service/src/main/resources/application.yml`; max 10, min idle 2 | Flyway |
| `document-service` | PostgreSQL, Redis flags | SQLAlchemy 2 async + `asyncpg` | `services/document-service/app/db/session.py`; defaults 10 + 20 overflow from `app/config.py` | Alembic |
| `analytics-service` | PostgreSQL | Slick plain SQL | `services/analytics-service/src/main/scala/com/otterworks/analytics/db/AnalyticsDb.scala`; AsyncExecutor, default 10 | Flyway |
| `admin-service` | PostgreSQL, Redis | ActiveRecord + `pg` | `services/admin-service/config/database.yml`; Rails pool defaults to `RAILS_MAX_THREADS`, 5 | ActiveRecord |
| `report-service` | PostgreSQL | Spring Data JPA/Hibernate | No explicit Hikari values in `services/report-service/src/main/resources/application.properties` | JPA `ddl-auto=update` |
| `legacy-portal` | H2 by default, PostgreSQL profile | Spring Data JPA/Hibernate | No explicit Hikari values in `services/legacy-portal/src/main/resources/application.yml` | JPA `ddl-auto=update` |
| `file-service` | DynamoDB, Redis flags | AWS SDK for Rust; `redis::aio::ConnectionManager` | Redis connection manager in `services/file-service/src/main.rs` | None |
| `audit-service` | DynamoDB | AWS SDK for .NET | Injected AWS client; no DB pool | None |
| `notification-service` | DynamoDB | AWS SDK for Kotlin | Injected AWS client; no DB pool | None |
| `collab-service` | Redis | `ioredis` | Two Redis clients in `services/collab-service/src/services/redis-adapter.ts` | None |
| `search-service` | MeiliSearch, Redis flags | MeiliSearch SDK, `redis` | Lazy Redis singleton in `services/search-service/app/api/search.py` | None |
| `api-gateway` | None | Go/Chi routing and proxy | None | None |
Infrastructure facts are in `infrastructure/terraform/modules/database/main.tf`
and `infrastructure/terraform/modules/cache/main.tf`. RDS is PostgreSQL 15.7,
database `otterworks`, encrypted, and defaults to `db.t3.micro`. No
`parameter_group_name` or `aws_db_parameter_group` is defined in
`infrastructure/terraform`, so the instance uses the AWS default parameter
group. Redis is 7.1, defaults to `cache.t3.micro`, and uses `default.redis7`.
The DynamoDB tables are file metadata, audit events, notifications, folders,
file versions, and file shares; each uses `PAY_PER_REQUEST`.

<a id="part-1"></a>
## Part 1 — From p95 alert to the query that caused it

The alert is in `observability/grafana/provisioning/alerting/alert-rules.yml`
with uid `document-service-high-latency`. It detects document-service P95
latency above 3 seconds over one minute, summarizes
`P95 latency spike on document-service`, and labels
`affected_service: document-service`.

The contact point in
`observability/grafana/provisioning/alerting/contact-points.yml` posts to
`http://admin-service:8089/api/v1/admin/alerts/ingest` with a Bearer secret.
The route is real: `services/admin-service/config/routes.rb` defines
`post 'alerts/ingest', to: 'alerts#ingest'`, implemented in
`services/admin-service/app/controllers/api/v1/admin/alerts_controller.rb`.

The slow access pattern is in
`services/document-service/app/services/document_service.py`. `list_documents()`
pages `documents`, then runs one query per returned document:

```python
for doc in documents:
    ver_result = await self.db.execute(
        select(DocumentVersion)
        .where(DocumentVersion.document_id == doc.id)
        .order_by(DocumentVersion.version_number.desc())
        .limit(5)
    )
    doc.recent_versions = list(ver_result.scalars().all())
```

The source marks this loop with `TODO: This is slow for large result sets
(ETL-445, deferred Q2 2024)`. For page size `N`, the endpoint performs one
document-page query plus `N` version queries, or `N + 1` before the count query.

The Alembic revision
`services/document-service/alembic/versions/001_initial_schema.py` indexes
`documents.owner_id` and `document_versions.document_id`. It does not define
an index on `documents.folder_id`, a composite index for the list filters and
`updated_at DESC`, or a composite
`document_versions (document_id, version_number DESC)` index.

This is a shared-resource incident. The Python query uses the same `db.t3.micro`
RDS instance as the other PostgreSQL services, so document-service pool waits
can compete with auth-service and report-service. The
`PostgreSQL Active Connections` panel in
`observability/grafana/dashboards/infrastructure.json` uses
`pg_stat_activity_count{datname="otterworks"}`; Prometheus scrapes
`postgres-exporter:9187` in `observability/prometheus/prometheus.yml`.

<a id="part-2"></a>
## Part 2 — Fan out across services with child sessions

```text
Use one orchestrator and one child session per PostgreSQL consumer:

In Cognition-Partner-Workshops/otterworks, create one child session for each
PostgreSQL-touching service: auth-service, document-service, analytics-service,
admin-service, report-service, and legacy-portal.

Each child must read the service's actual build/configuration files, models or
repositories, and migration files. Use these paths as starting points:
services/auth-service/src/main/resources/application.yml
services/document-service/app/config.py
services/document-service/app/db/session.py
services/analytics-service/src/main/resources/application.conf
services/analytics-service/src/main/scala/com/otterworks/analytics/db/AnalyticsDb.scala
services/admin-service/config/database.yml
services/report-service/src/main/resources/application.properties
services/legacy-portal/src/main/resources/application.yml
infrastructure/terraform/modules/database/main.tf
infrastructure/terraform/modules/database/variables.tf

Aggregate the child reports into docs/db/CONNECTION_POOL_AUDIT.md. Include:
- configured pool size and location for each service;
- services with no explicit pool configuration;
- a clearly labeled worst-case connection total based only on explicit
  settings, with assumptions for implicit defaults;
- why db.t3.micro capacity and the default parameter group require a human
  capacity decision; and
- the missing aws_db_parameter_group finding.

Return the artifact path and a table of sources. Do not invent a database
capacity limit or execute production changes.
```

A second fan-out follows the column through the application boundary:

```text
In Cognition-Partner-Workshops/otterworks, find each application and schema
reference to the current document soft-delete column before a contract change.

Read and search these exact paths:
services/document-service/app/models/document.py
services/document-service/app/services/document_service.py
services/document-service/app/schemas/document.py
services/document-service/alembic/versions/001_initial_schema.py
shared/openapi/document-service.yaml
clients/windows-desktop/OtterWorks.Desktop/Models/DocumentModels.cs

Create docs/db/IS_DELETED_RIPPLE.md with a table containing each reference,
the language or schema format, the current read/write behavior, and the
release step that must change it. Include a grep command and the exact
matched paths so a reviewer can reproduce the inventory.
```

<a id="part-3"></a>
## Part 3 — The expand/contract schema change

The change replaces `documents.is_deleted` with nullable `deleted_at
TIMESTAMPTZ`, plus the composite index identified in Part 1. It is reviewed
before production DDL runs.

```text
In Cognition-Partner-Workshops/otterworks, design an expand/contract change
that replaces documents.is_deleted with nullable documents.deleted_at
TIMESTAMPTZ and adds the composite index justified in
docs/db/SLOW_QUERY_ANALYSIS.md.

Read:
services/document-service/alembic/env.py
services/document-service/alembic/versions/001_initial_schema.py
services/document-service/app/models/document.py
services/document-service/app/services/document_service.py
services/document-service/app/schemas/document.py
shared/openapi/document-service.yaml
clients/windows-desktop/OtterWorks.Desktop/Models/DocumentModels.cs
services/report-service/src/main/resources/application.properties
services/legacy-portal/src/main/resources/application.yml

Produce:
1. docs/db/MIGRATION_PLAN.md;
2. the Alembic revision files under
   services/document-service/alembic/versions/; and
3. a separate resumable backfill job under
   services/document-service/.

Use these phases:
1. Expand: add deleted_at and create the chosen index concurrently. Mark the
   Alembic migration non-transactional. Do not add NOT NULL or a default that
   rewrites the table.
2. Dual-write deleted_at behind the existing document read path.
3. Backfill in rate-limited, resumable chunks as a separate job, never inside
   the migration.
4. Switch reads to deleted_at.
5. Contract in a later release, after the six-file ripple is merged; drop
   is_deleted only in a separate release.

For each phase, state rollback steps and whether it is safe while the
application serves traffic. Explain that CREATE INDEX CONCURRENTLY cannot run
inside a transaction and what that requires in Alembic. Call out the
production hazard of ddl-auto=update in report-service and legacy-portal.

Write the plan and code only. A human DBA executes production DDL after plan
approval. The agent may run checks against local docker-compose Postgres or a
development tenant namespace, but it must not touch production.
```

`CREATE INDEX CONCURRENTLY` requires a non-transactional migration boundary.
The backfill should record a stable cursor or key range, cap batches, tolerate
retries, and expose progress. A production operator decides batch size, rate
limit, maintenance constraints, and index definition.

<a id="part-4"></a>
## Part 4 — Devin Review as the migration gate

Make the review hazard concrete with this deliberately unsafe migration:

```sql
ALTER TABLE documents
  ADD COLUMN deleted_at TIMESTAMPTZ NOT NULL DEFAULT NOW();

CREATE INDEX documents_updated_at_idx
  ON documents (updated_at DESC);

UPDATE documents
SET deleted_at = NOW()
WHERE is_deleted = TRUE;

ALTER TABLE document_versions
  ADD COLUMN document_owner_id UUID REFERENCES users(id);
```

Commit that snippet as
`services/document-service/alembic/versions/999_review_fixture.py` in the
`Cognition-Partner-Workshops/otterworks` working branch, then ask Devin Review
to inspect the resulting PR diff:

```text
Review the new migration in Cognition-Partner-Workshops/otterworks as a
production database reviewer.

Read:
services/document-service/alembic/env.py
services/document-service/alembic/versions/001_initial_schema.py
services/document-service/app/models/document.py
services/document-service/app/services/document_service.py

Comment on the migration with specific hazards and safe rewrites. Check for:
- a NOT NULL column with a DEFAULT rewrite on a large table;
- a plain CREATE INDEX where CREATE INDEX CONCURRENTLY is needed;
- an unbounded UPDATE backfill inside the migration; and
- a new foreign-key column without an index.

Recommend an expand/contract sequence, a separate resumable backfill, and the
Alembic transaction setting. Do not execute DDL and do not change unrelated
files.
```

A useful review comment flags table-lock or rewrite risk, a transaction
boundary conflict, unbounded write load, and foreign-key lookup performance.
It should propose a safe rewrite rather than merely labeling SQL unsafe.

Close the loop when a reviewer requests a concrete revision:

```text
In Cognition-Partner-Workshops/otterworks, update the migration plan and
backfill job in docs/db/MIGRATION_PLAN.md and
services/document-service/ so the backfill is resumable and caps each batch
at N rows. Preserve the expand/contract phases, rollback notes, and the
non-transactional CREATE INDEX CONCURRENTLY migration setting.

Return the changed file paths, the cursor or checkpoint mechanism, the batch
limit, and the local verification commands. Do not execute production DDL.
```

Make the standards reusable as shared context:

```text
In Cognition-Partner-Workshops/otterworks, inspect the existing repository
guidance before adding migration-review rules. Check whether REVIEW.md exists.
If it does not, create a concise review guidance artifact that records:
expand/contract sequencing, non-transactional concurrent indexes, resumable
backfills, rollback evidence, foreign-key indexing, ddl-auto=update hazards,
and the requirement for human production approval.

Also write the same rules as a reusable !review-schema-change playbook or
knowledge-note payload, clearly marking which content is repository guidance
and which content belongs in the Devin shared context layer. Return the exact
artifact paths and do not alter application code.
```

The otterworks repository has no root `REVIEW.md`; do not claim it was updated.

<a id="part-5"></a>
## Part 5 — Wire the trigger: hands-free

`.github/workflows/ci.yml` is path-filtered per service and validates Terraform.
Other workflows are
`.github/workflows/docker-build.yml`,
`.github/workflows/sast-auto-remediate.yml`, and
`.github/workflows/security-scan.yml`. There is no dedicated migration-check
workflow, so add one:

```text
In Cognition-Partner-Workshops/otterworks, add
.github/workflows/db-change-guard.yml.

Read the existing automation precedent:
.github/workflows/ci.yml
.github/workflows/sast-auto-remediate.yml

The new workflow must trigger on pull_request and be path-filtered to:
services/document-service/alembic/versions/**
services/auth-service/src/main/resources/db/migration/**
services/analytics-service/src/main/resources/db/migration/**
services/admin-service/db/migrate/**

It must:
1. inspect the pull-request diff for blocking DDL patterns, including
   NOT NULL DEFAULT additions, non-concurrent CREATE INDEX, unbounded UPDATE
   backfills, and foreign-key additions without a matching index;
2. post a PR comment containing the matched pattern, file path, and safe
   rewrite;
3. on a finding, call the Devin v3 API using the same bot-author check,
   attempt cap, and GitHub Issue escalation pattern as
   sast-auto-remediate.yml; and
4. leave production execution to a human DBA.

Pass this trigger payload to the session:
{
  "pr_number": "<pull request number>",
  "head_sha": "<head commit SHA>",
  "changed_migration_paths": ["<changed migration paths>"],
  "matched_pattern": "<blocking DDL pattern>"
}

The Devin session rewrites the migration in expand/contract style, adds or
updates the resumable backfill plan, and returns the changed paths and local
verification output. Do not include production credentials or execute DDL.
```

Grafana sends alert data to `admin-service` at
`/api/v1/admin/alerts/ingest`; the Rails controller expects an `alerts` array.
A forwarder can pass these fields to a Devin v3 session:

- alert uid: `document-service-high-latency`
- `affected_service`: `document-service`
- summary: `P95 latency spike on document-service`
- time window: the alert evaluation window, one minute for this rule

At 2 a.m., that path can open an investigation session with the alert payload,
query analysis, and shared-resource panels. The agent investigates and
proposes; it does not touch production.

<a id="part-6"></a>
## Part 6 — Shared context layer

- A Knowledge note can define the organization's DDL rules, maintenance
  windows, and which services share the RDS instance.
- DeepWiki over `Cognition-Partner-Workshops/otterworks` provides repository
  orientation before child sessions divide the inventory.
- A `!review-schema-change` playbook can encode expand/contract sequencing,
  rollback evidence, and the human-approval boundary.
- The repository-local skill
  `.agents/skills/synthetic-testdata-generation/SKILL.md` is a precedent for
  keeping repeatable procedure close to the codebase.
- Where the organization has an MCP integration configured, the session can
  connect ticket or observability context without claiming that a specific
  integration is wired into this workshop environment.

<a id="before-after"></a>
## Before / After

Use directional, illustrative measures; replace them with measured values:

| Measure | Before | After |
|---|---|---|
| Schema-change lead time | Changes wait for manual discovery and review | Plans arrive with impact, rollback, and local evidence |
| Application PRs needing DBA review | DBA reviews repeated mechanical hazards | Automation flags patterns; DBA reviews plans and exceptions |
| Document-list P95 | Regression is discovered through a shared alert | Query and index evidence accompany the proposed fix |
| Alert-to-root-cause hypothesis | Depends on who is awake and has repository context | An alert session assembles Grafana, code, and schema evidence |
| Pool configuration across 12 services | Explicit settings are uneven; report and legacy have none | Pool settings and capacity assumptions are inventoried and owned |

<a id="what-still-needs-a-human"></a>
## What Still Needs a Human

- Production credentials and execution of DDL.
- Approval of the migration plan and maintenance window.
- Capacity, instance-class, and parameter-group decisions for the shared RDS.
- Judgment on data-loss-adjacent contract steps such as dropping a column.
- Decisions involving `ddl-auto=update` in report-service and legacy-portal.
- Incident command and prioritization during an active outage.
- Acceptance of the final query plan and index tradeoffs against production
  data distribution.

<a id="key-takeaways"></a>
## Key Takeaways

- A day-two DBA investigation starts with the alert, shared-resource evidence,
  and the exact query path—not with a speculative schema rewrite.
- The document-service loop is a concrete N+1 pattern: one version query per
  document returned by the page.
- Pool settings, RDS sizing, and the default parameter group form one
  operational system across the polyglot PostgreSQL consumers.
- Expand/contract changes separate additive DDL, dual-write, backfill, read
  switch, and contract so traffic can continue through the release sequence.
- Concurrent indexes and resumable backfills require explicit migration and
  operational boundaries.
- Review and webhook automation can catch recurring hazards and prepare
  evidence, while a human DBA retains production authority.
- Shared Knowledge, DeepWiki, playbooks, repository skills, and conditional
  MCP context let child sessions work from the same approved procedure.
