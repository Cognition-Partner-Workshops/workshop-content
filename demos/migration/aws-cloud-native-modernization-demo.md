# AWS Cloud-Native Modernization — The R's of Migration Demo

A single linear demo that shows Devin taking a running, self-managed application
(OtterWorks) and moving it onto AWS across the **R's of migration** — rehost,
replatform, refactor, re-architect, retire — and proving every move is
behavior-identical through the repo's own contract tests before it is trusted.
The story works for both directions an AWS-native audience cares about:
**on-prem → AWS** and **another cloud / self-managed → AWS**.

The thread orients over the estate → does one move live with verification (catch
+ fix a real divergence) → shows the move that is really a hundred small repeated
edits across a polyglot codebase → fans the R's out in parallel → confirms the
cloud-native win in the AWS console. The flagship live move migrates
`search-service` from a self-managed MeiliSearch instance to **Amazon OpenSearch
Serverless** — the canonical "retire the toil, go serverless" story, with an
existing contract harness and a great console.

The prompts below invoke the `!aws-cloud-native` Devin Playbook — the reusable
migration procedure — whose source lives in the code repo at
[`otterworks/.workshop/playbooks/aws-cloud-native-modernization.devin.md`](https://github.com/Cognition-Partner-Workshops/otterworks/blob/main/.workshop/playbooks/aws-cloud-native-modernization.devin.md).
The repo-specific mechanics (the migration menu, adapter seams, IaC modules,
deploy wiring, test commands) come from that repo's Skill
(`.agents/skills/aws-cloud-native-modernization/SKILL.md`).

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [The R's of Migration, Mapped to OtterWorks](#rs)
- [On-Prem → AWS and Another-Cloud → AWS](#direction)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Does the Migration](#part-1)
  - [Act 1 — Orient over the estate](#act-1)
  - [Act 2 — Replatform one component live, with verification](#act-2)
  - [Act 3 — The repeated change: switch the database target across the codebase](#act-3)
  - [Act 4 — Refactor & re-architect: serverless compute, event-driven, and a lakehouse](#act-4)
  - [Act 5 — Fan the R's out in parallel](#act-5)
  - [Act 6 — Confidence = programmatic verification](#act-6)
- [Part 2 — Confirm the Result in the AWS Console](#part-2)
- [Concurrent Runs](#concurrent)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

The verification loop is the repo's own contract suite pointed at a running
`search-service`:

```bash
pip install -r tests/api/requirements.txt
SEARCH_SERVICE_URL=http://localhost:8087 \
  pytest tests/contract/test_search_contract.py -v
```

The managed target is provisioned as namespaced Terraform and deployed via the
repo's deploy script:

```bash
cd infrastructure/terraform && terraform apply -target=module.opensearch
AWS_ACCOUNT_ID=$AWS_ACCOUNT_ID DB_PASSWORD=$DB_PASSWORD \
  ./scripts/deploy-dev.sh --skip-platform
```

Prerequisites: AWS credentials for the workshop account, `kubectl` context for
EKS `otterworks-dev`, Terraform, Python 3.12, and a load-test tool (`hey` / `k6`).

---

<a id="repositories"></a>
## Repositories

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) — the
  running polyglot platform (11 backend services across 9 languages + 2
  frontends) with two-layer Terraform (`platform/terraform` for VPC/EKS/ECR,
  `infrastructure/terraform` for app resources: `modules/database`, `cache`,
  `messaging`, `storage`, `search`, `auth`, `irsa`, `monitoring`), Helm charts,
  the contract/flow test suites (`tests/contract/`, `tests/api/`), the OpenAPI
  specs (`shared/openapi/`), the migration playbook source
  (`.workshop/playbooks/`), and the repo Skill
  (`.agents/skills/aws-cloud-native-modernization/`).

OtterWorks is the **golden base repo for every migration demo**: one estate with
the self-managed "before", the IaC to provision the managed/serverless "after",
and the contract tests that prove parity. Because the services are genuinely
polyglot (Go, Java, Rust, Python, C#, Scala, Ruby, Node.js, Kotlin), it is a
realistic stand-in for an enterprise estate where the *same* migration lands
differently in every codebase.

---

<a id="rs"></a>
## The R's of Migration, Mapped to OtterWorks

Modernization is not one move — it is a portfolio of moves, each with a different
risk/reward. This is the industry-standard **R's** framing, grounded in real
OtterWorks components. Each row is a runnable unit of the same `!aws-cloud-native`
playbook, held to the same verification bar. The flagship (⭐) is the one this
thread executes live end to end.

| R | What it means | OtterWorks unit (before) | AWS target (after) |
|---|---|---|---|
| **Rehost** | Lift-and-shift a VM-hosted app as-is | A legacy VM/on-prem-style component *(see [coverage note](#direction))* | **EC2 + Auto Scaling Group** behind an ALB |
| **Replatform** ⭐ | Swap a backing service for a managed one, minimal app change | `search-service` on self-managed **MeiliSearch** (ECS Fargate `modules/search`) | **Amazon OpenSearch Serverless** |
| **Replatform** | Move the relational store to managed, change only the connection | PostgreSQL reached from all 11 services (`modules/database`) | **Aurora Serverless v2** (scale-to-zero) |
| **Replatform** | Managed cache instead of self-run Redis | Redis (`modules/cache`) | **ElastiCache** |
| **Refactor** | Re-package always-on code as serverless functions | `report-service` (legacy **Java 8**, always-on EKS pod) | **AWS Lambda + API Gateway** |
| **Refactor** | Evolve the data schema with continuous validation | Flyway/Rails schema (`auth-service`, `admin-service`, `testdata/harness`) | New schema, parity-checked against the old |
| **Re-architect** | Batch/synchronous → event-driven | `notification-service` in-cluster consumer; `analytics-service` SQS intake | **EventBridge + SQS + Lambda** |
| **Re-architect** | Analytics store → open-table lakehouse | `analytics-service` (Scala) aggregating in-memory/Postgres | **S3 data lake in Apache Iceberg** (Athena/Glue) |
| **Retire** | Decommission the self-managed thing you replaced | Self-managed MeiliSearch / Redis footprint | *(gone — replaced by managed)* |
| **Retain / Repurchase / Relocate** | Keep as-is, buy SaaS, or VMware→AWS | Golden `main` is retained as the durable before-state | *(out of scope for this repo)* |

The message: the audience picks the R that matches the pain in their estate, and
the *same* Devin playbook + verification bar applies to every one.

---

<a id="direction"></a>
## On-Prem → AWS and Another-Cloud → AWS

The R's story is symmetric across where you are starting from:

- **Another cloud / self-managed → AWS.** MeiliSearch, Redis, and PostgreSQL in
  OtterWorks are *self-managed* — the exact shape of a workload running on
  someone else's cloud or on hardware you patch yourself. The migration is a
  **replatform**: keep the application contract, point it at the managed AWS
  service (OpenSearch Serverless, ElastiCache, Aurora), retire the self-managed
  thing. Devin's adapter-behind-a-config-flip pattern means the source could have
  been running anywhere.
- **On-prem → AWS.** Two moves carry this narrative: a **rehost** of a
  VM-hosted app onto EC2 + Auto Scaling, and a **data lift** of an on-prem
  analytics store into an S3 + Iceberg lakehouse. The value is Devin authoring
  the EC2/ASG Terraform, the AMI/user-data or containerization, and the data
  loader — then proving the rehosted app still passes its contract suite.

> **Coverage note (golden-base gap).** OtterWorks today runs entirely on EKS plus
> one ECS Fargate task (MeiliSearch); there is **no EC2-hosted / VM component and
> no `aws_instance` IaC**, and **no scheduled/batch job**. To make the *rehost*
> and *batch → event-driven* stories first-class on the golden base, add (1) a
> VM-hosted "legacy on-prem" component (or an on-prem `docker-compose` profile)
> plus an EC2 + ASG Terraform module as its target, and (2) a scheduled batch job
> (e.g. a nightly analytics rollup) as the "before" for the event-driven move.
> The replatform / refactor / re-architect-to-Lambda / Iceberg rows are all
> already backed by real source. See the source-coverage summary shared with this
> thread.

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Code | Infra / Data |
|---|---|---|
| **Before** | `main`: `search-service` talking to MeiliSearch through `app/services/meilisearch_client.py`, backend-agnostic API layer, the contract suite, the playbook source, and the Skill | Self-managed MeiliSearch (ECS Fargate `modules/search`, or in-cluster via `deploy-dev.sh`); the indexed documents/files |
| **After** | a `migration/<ns>` branch: a new `opensearch_client.py` adapter selected by `SEARCH_BACKEND=opensearch`, a namespaced `modules/opensearch` Terraform module, IRSA + deploy wiring — built live by Devin | **Amazon OpenSearch Serverless** collection (namespaced); the same corpus indexed into it |

The **before** state is durable on `main`: the self-managed backend stays, and
the swap is a config flip (`SEARCH_BACKEND`), never a rewrite of the caller. The
**after** lives on a namespace branch with a namespaced OpenSearch collection —
which is what makes the demo safe to repeat and to run concurrently.

The verification loop sits between them: the migrated backend must satisfy the
**identical** OpenAPI contract before it is trusted.

> **On "parity":** parity means the contract suite
> (`tests/contract/test_search_contract.py`, gated by
> `shared/openapi/search-service.yaml`) passes unchanged against the
> OpenSearch-backed service — endpoint existence, query/suggest/advanced
> semantics, response schemas, status codes, and the `/health/ready` reason
> string — not "the new backend returned 200".

---

<a id="part-1"></a>
## Part 1 — Devin Does the Migration

<a id="act-1"></a>
### Act 1 — Orient over the estate

Open OtterWorks and ask Devin to map the search component and its backend seam.
With DeepWiki over the repo, Devin typically maps an unfamiliar estate in minutes
(coverage depends on repo structure).

```
Using the Cognition-Partner-Workshops/otterworks repo, map the search-service: how app/api/*.py routes reach the backend through app/services/meilisearch_client.py, how app/config.py selects the backend from env, what MeiliSearch provides today (self-managed on ECS Fargate in infrastructure/terraform/modules/search plus the in-cluster MeiliSearch in scripts/deploy-dev.sh), and every behavior the contract suite tests/contract/test_search_contract.py asserts against shared/openapi/search-service.yaml.
```

Expected: a tour of the backend seam (`query` / `suggest` / `advanced` / `index`
/ `analytics`), the env-driven config, the self-managed MeiliSearch footprint you
own today, and the contract behaviors — including that `/suggest` is prefix-first
type-ahead. Devin identifies that the API layer is backend-agnostic, so the
migration is an adapter swap behind a config flip.

<a id="act-2"></a>
### Act 2 — Replatform one component live, with verification

The core beat, and the flagship **replatform** (another-cloud/self-managed →
AWS). Paste the playbook prompt. Devin provisions OpenSearch Serverless as
namespaced IaC, writes the OpenSearch adapter behind the existing interface,
deploys, runs the contract suite, catches a divergence, fixes it, and produces a
PR with the verification report.

```
!aws-cloud-native

Migrate search-service from self-managed MeiliSearch to Amazon OpenSearch Serverless in Cognition-Partner-Workshops/otterworks.

- Backend seam: services/search-service/app/services/meilisearch_client.py (add a sibling opensearch_client.py implementing the same methods, selected by a new SEARCH_BACKEND env; default meilisearch so main is unchanged)
- Contract / source of truth: tests/contract/test_search_contract.py + shared/openapi/search-service.yaml
- IaC: add infrastructure/terraform/modules/opensearch (an OpenSearch Serverless SEARCH collection + encryption/network/data-access policies, namespaced) and extend the search-service IRSA policy with
  aoss:APIAccessAll
- Namespace: os-demo   (branch migration/opensearch-os-demo)
- Prove parity with the contract suite, then run a before/after load test and capture the OpenSearch Dashboards + CloudWatch view.
```

**The verification beat (the real bug).** The OpenSearch adapter uses a `match`
query for both search and suggest. It connects and returns `200` — so "looks
reasonable" review would ship it. But the contract suite catches the semantics
gap:

```
tests/contract/test_search_contract.py::TestSuggestEndpoint::test_suggest_valid_prefix FAILED Expected: prefix "tes" returns type-ahead suggestions
  Actual:   [] — OpenSearch `match` tokenizes on whole terms;
            MeiliSearch is prefix-first by default
```

MeiliSearch is prefix-first (type-ahead out of the box); an OpenSearch `match`
query is not. The fix is to map the suggest path to a `search_as_you_type` /
`match_phrase_prefix` query (a field mapping + query rewrite in the adapter) —
**not** to relax the test. Re-run, and the suite goes green:

```bash
SEARCH_SERVICE_URL=http://<gateway>/api/v1/search pytest \
  tests/contract/test_search_contract.py -v
#   28 passed
```

The point: a "clean" SDK swap would have silently broken type-ahead; the contract
test against the OpenAPI spec caught it. The full write-up is in the playbook
under *Worked example*.

<a id="act-3"></a>
### Act 3 — The repeated change: switch the database target across the codebase

Some R's are not one edit — they are the *same* edit repeated everywhere, which
is exactly the toil Devin removes. Replatforming the relational store to **Aurora
Serverless v2** keeps the schema and the queries, but the connection layer
changes in every service — and OtterWorks reaches PostgreSQL from 11 services in
9 different languages (JDBC in Java/Kotlin, `database/sql` in Go, `sqlx` in Rust,
`psycopg` in Python, Npgsql in C#, Slick in Scala, ActiveRecord in Ruby, the Node
pool). One human doing this hand-edits dozens of files consistently across
languages they may not all know; Devin does it as one governed sweep.

```
In Cognition-Partner-Workshops/otterworks, replatform the PostgreSQL data layer to Amazon Aurora Serverless v2 (provisioned as a namespaced Terraform module alongside modules/database, not replacing it). Do not change any schema or SQL.

Across every service that connects to PostgreSQL, update only the connection layer to target the Aurora endpoint via the existing DB_HOST / DATABASE_URL config, add IAM database authentication and TLS, and keep the current PostgreSQL config wired for revert. Enumerate every file you change, per language (Java/Kotlin JDBC, Go database/sql, Rust sqlx, Python psycopg, C# Npgsql, Scala Slick, Ruby ActiveRecord, Node pool). Prove parity by running each service's existing DB-backed tests and the tests/api flow suite against Aurora, and report a before/after connection/latency comparison.
```

Expected: a single PR with the identical connection change applied
service-by-service in each language's idiom, the same tests green against Aurora,
and a change list you can audit. This is the "large-scale, repetitive,
capacity-constrained" work that is painful for a team and natural for a fleet of
Devins — and it generalizes to driver bumps, dialect changes, and endpoint
cutovers.

<a id="act-4"></a>
### Act 4 — Refactor & re-architect: serverless compute, event-driven, and a lakehouse

The higher-reward R's change the *shape* of the system to capitalize on
cloud-native primitives. Three moves, each still gated by the repo's tests:

**Refactor to serverless compute — `report-service` (Java 8 pod) → Lambda + API
Gateway.** An always-on EKS pod that is idle most of the day becomes a
pay-per-request function behind API Gateway, preserving the API.

```
!aws-cloud-native

Refactor services/report-service (legacy Java 8, always-on EKS pod) to run as AWS Lambda behind API Gateway in Cognition-Partner-Workshops/otterworks, preserving its HTTP API exactly. Provision the function and gateway as a namespaced Terraform module; keep the EKS deployment intact on main for revert. Prove the existing report flow tests (tests/api/test_audit_analytics_report_flow.py) pass through the API Gateway URL, and capture Lambda invocations/duration/cold-start from CloudWatch.
```

**Re-architect to event-driven — `notification-service` → EventBridge + SQS +
Lambda.** A synchronous/in-cluster consumer becomes a decoupled event pipeline.

```
!aws-cloud-native

Re-architect notification delivery in Cognition-Partner-Workshops/otterworks to an event-driven pipeline: publish domain events to Amazon EventBridge, route to an SQS queue, and process with a Lambda consumer, provisioned as a namespaced Terraform module. Keep the existing in-cluster consumer on main. Prove the notification flow tests still pass end to end, and show the EventBridge rule + SQS + Lambda wiring in the console.
```

**Re-architect the analytics store — Scala `analytics-service` → S3 + Apache
Iceberg lakehouse.** `analytics-service` already carries an
`s3.data-lake-bucket` config and an SQS `EventProcessor`, but aggregates
in-memory today. Move the durable analytics store to an S3 data lake in **Iceberg**
table format, queryable with Athena/Glue — the on-prem-analytics → cloud-native
lakehouse story.

```
!aws-cloud-native

In Cognition-Partner-Workshops/otterworks, re-architect analytics-service persistence to an S3 data lake in Apache Iceberg table format (Glue catalog + Athena), provisioned as a namespaced Terraform module and wired through the existing analytics.s3.data-lake-bucket config. Events consumed from SQS are written as Iceberg records; the dashboard summary reads back via Athena. Add a continuous-validation check that reconciles Iceberg aggregates against the current in-memory/Postgres path for a seeded event set, and keep the existing path on main for revert.
```

Note the continuous-validation ask in the last prompt: when a **refactor** also
changes the data schema, the verification loop becomes a reconciliation harness
that proves old and new produce the same aggregates — not an eyeball check.

<a id="act-5"></a>
### Act 5 — Fan the R's out in parallel

The R's are independent, so run one **orchestrator** session that spawns a child
Devin session per move and monitors them — one agent fanning itself out across a
modernization wave, each child on its own namespace and branch, each opening its
own verified PR.

```
Act as the orchestrator for an AWS cloud-native modernization of Cognition-Partner-Workshops/otterworks, using child Devin sessions to parallelize the R's of migration.

Spawn one child Devin session per row below. Give each child the repo, its own namespace + branch (migration/<row>-<ns>), and tell it to follow the !aws-cloud-native playbook (the repo Skill supplies the adapter seams, IaC module locations, deploy wiring, and contract-test commands): provision the managed/serverless target as namespaced least-privilege IaC, wire the app behind its existing interface via a config flip or connection change, prove parity with the repo's contract/flow tests, catch and fix any behavioral divergence against the contract, and run a before/after performance test.

Rows:
1. Replatform: search-service   -> Amazon OpenSearch Serverless (flagship)
2. Replatform: PostgreSQL layer -> Aurora Serverless v2
3. Refactor:   report-service   -> AWS Lambda + API Gateway
4. Re-architect: notification    -> EventBridge + SQS + Lambda
5. Re-architect: analytics store -> S3 + Apache Iceberg

After launching, monitor the child sessions until each row's verification is green. Summarize the results and call out every divergence the children caught (e.g. the prefix/type-ahead gap in search).
```

Each child runs in its own VM with scoped credentials and its own namespace, so
the parallel migrations are safe and never collide. Positioned as a **scheduled**
or **event-driven** run, the same playbook also sweeps the estate unattended — a
nightly parity re-check, or an Automation that re-runs the migration loop and
pushes a fix on a CI failure.

<a id="act-6"></a>
### Act 6 — Confidence = programmatic verification

The gates that make every migration PR trustworthy:

- **Contract tests** (`tests/contract/test_search_contract.py` against the running
  service): endpoint existence, query/suggest/advanced semantics, response schema
  parity, status codes, and the `/health/ready` reason string — all gated by
  `shared/openapi/search-service.yaml`.
- **API flow tests** (`make test-api-flows`): the black-box suites that exercise
  each migrated component end to end through the gateway.
- **Reconciliation / continuous validation**: for schema and data-store moves,
  old vs. new aggregates must match on a seeded dataset before the swap is trusted.
- **Terraform plan/validate**: the namespaced managed target provisions cleanly
  with least-privilege IAM, changing no shared or `main` resources.
- **Devin Review**: an automated reviewer on every PR.

A migration is "done" when the tests are green against the managed/serverless
backend — not when the new backend merely connects.

---

<a id="part-2"></a>
## Part 2 — Confirm the Result in the AWS Console

The cloud-native payoff is visible, not narrated. Walk the console views in this
order:

**1. The managed target exists — OpenSearch Serverless.** In the AWS console open
**OpenSearch Service → Serverless → Collections** and show the namespaced
`otterworks-search-<ns>` collection `Active`, with its data-access and network
policies. This is the "self-managed thing you used to babysit is now a managed
service" beat — no instances, no patching, no capacity to size.

**2. Query performance — OpenSearch Dashboards.** Open the collection's
**Dashboards** endpoint and show index size, document count, and query latency
for the load-test traffic. Contrast with the before: the MeiliSearch task you ran
yourself on ECS Fargate.

**3. The performance delta — CloudWatch.** Open the **CloudWatch** dashboard for
the collection (search/index request counts, latency) captured during the
before/after `hey`/`k6` run. Show the after numbers next to the baseline.

**4. Serverless economics.** Call out what changed operationally: OpenSearch
Serverless scales capacity to the workload (no always-on instance to size), and
for the refactor rows the **Lambda + API Gateway** console shows **scale-to-zero**
(invocations/duration/concurrency in CloudWatch) — you pay per request, not per
idle hour. For the event-driven row, the **EventBridge** rule + **SQS** queue +
**Lambda** consumer show a decoupled pipeline; for the lakehouse row, **Athena**
querying **S3/Iceberg** tables shows analytics on open table format.

**5. The before is untouched.** Show `main` still has `search-service` on
MeiliSearch (the `SEARCH_BACKEND` default) and the `modules/search` MeiliSearch
module intact. Each move lives on its `migration/<ns>` branch and its namespaced
resources, ready for review — and `terraform destroy -target=module.<name>` is
the one-command revert.

---

<a id="concurrent"></a>
## Concurrent Runs

Every run carries a namespace suffix applied to the Terraform module/resources,
the branch, and any per-run k8s objects, so multiple migrations — and the Act 5
fan-out — coexist with no collisions:

```bash
# Run 1
terraform apply -target=module.opensearch   # collection otterworks-search-os1
git checkout -b migration/opensearch-os1

# Run 2 (concurrent)
terraform apply -target=module.opensearch   # collection otterworks-search-os2
git checkout -b migration/opensearch-os2
```

The self-managed before-state on `main` is shared and read-only to these runs, so
concurrent migrations never step on the golden app. Revert is per-namespace:
`terraform destroy -target=module.<name>` plus dropping the config flip.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **Modernization is a portfolio of R's, not one move.** The demo walks rehost, replatform, refactor, re-architect, and retire against one real estate (OtterWorks), so the audience can map each R to the pain in their own — and it works whether the source is on-prem or another cloud.
- **The value on display is Devin doing the work.** Reading a running estate, provisioning a managed/serverless AWS target as least-privilege IaC, and wiring the app behind its existing interface off a reusable playbook — not a finished artifact to run.
- **Some R's are the same change repeated everywhere.** Switching the database target touches the connection layer in all 11 polyglot services; Devin applies the identical change idiomatically per language as one auditable sweep — the large-scale, repetitive work that is painful for a team.
- **Confidence comes from programmatic verification.** The repo's own contract/flow tests (and a reconciliation harness for schema/data moves) gate every migration, and the demo shows a real divergence — OpenSearch `match` breaking MeiliSearch's prefix/type-ahead `/suggest` — being caught and fixed against the contract, not the test relaxed.
- **One playbook, the whole menu, run in parallel — and unattended.** The same `!aws-cloud-native` procedure covers every row; a parent session fans children out across the R's, each producing its own verified PR, and the same loop runs on a schedule or an Automation.
- **The cloud-native win is visible and reversible.** The payoff shows up in the AWS console (OpenSearch Dashboards + CloudWatch, Lambda scale-to-zero, EventBridge/SQS, Athena over S3/Iceberg), the before-state stays untouched on `main`, and namespaced IaC + `terraform destroy` make the whole story safe to repeat.
