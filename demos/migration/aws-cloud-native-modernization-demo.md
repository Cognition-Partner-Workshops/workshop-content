# AWS Cloud-Native Modernization — Managed & Serverless Migration Demo

A single linear demo that shows Devin taking a running, self-managed application
(OtterWorks) and moving its components onto **fully managed** and **serverless**
AWS services — proving each swap is behavior-identical through the repo's own
contract tests, then showing the cloud-native win as a before/after performance
run in the AWS console. Orient over the estate → migrate one component live with
verification (catch + fix a real divergence) → fan the migration menu out in
parallel → confirm the result in the AWS console.

The flagship flow migrates `search-service` from a self-managed MeiliSearch
instance to **Amazon OpenSearch Serverless** — the canonical "retire the toil,
go serverless" story, with an existing contract harness and a great console.

The prompts below invoke the `!aws-cloud-native` Devin Playbook — the reusable
migration procedure — whose source lives in the code repo at
[`otterworks/.workshop/playbooks/aws-cloud-native-modernization.devin.md`](https://github.com/Cognition-Partner-Workshops/otterworks/blob/main/.workshop/playbooks/aws-cloud-native-modernization.devin.md).
The repo-specific mechanics (the migration menu, adapter seams, IaC modules,
deploy wiring, test commands) come from that repo's Skill
(`.agents/skills/aws-cloud-native-modernization/SKILL.md`).

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [The Migration Menu](#menu)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Does the Migration](#part-1)
  - [Act 1 — Orient over the estate](#act-1)
  - [Act 2 — Migrate one component live, with verification](#act-2)
  - [Act 3 — Fan the menu out in parallel](#act-3)
  - [Act 4 — Confidence = programmatic verification](#act-4)
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
  running polyglot platform (11 backend services across 8 languages + 2
  frontends) with two-layer Terraform (`platform/terraform` for VPC/EKS/ECR,
  `infrastructure/terraform` for app resources), Helm charts, the contract/flow
  test suites (`tests/contract/`, `tests/api/`), the OpenAPI specs
  (`shared/openapi/`), the migration playbook source (`.workshop/playbooks/`),
  and the repo Skill (`.agents/skills/aws-cloud-native-modernization/`).

Everything the demo needs lives in one repo: the self-managed "before", the IaC
to provision the managed/serverless "after", and the contract tests that prove
parity.

---

<a id="menu"></a>
## The Migration Menu

The demo frames modernization as two themes an AWS-native audience cares about.
Each row is one runnable unit of the `!aws-cloud-native` playbook; the flagship
(⭐) is the one this thread executes end to end.

**Theme 1 — Retire the toil: self-managed → fully managed**

| Component | Today (self-managed pain) | Managed AWS target |
|---|---|---|
| **search-service** ⭐ | MeiliSearch you patch, scale, and back up yourself | **Amazon OpenSearch Serverless** |
| relational DB | always-on RDS you size for peak | **Aurora Serverless v2** (scale-to-zero) |
| identity (auth-service) | hand-rolled JWT issue/validate | **Amazon Cognito** |
| cache | (already ElastiCache — the reference pattern) | ElastiCache |

**Theme 2 — Capitalize on cloud-native: serverless compute & event-driven**

| Component | Today | Serverless AWS target |
|---|---|---|
| report-service (legacy Java 8) | always-on EKS pod, idle most of the day | **AWS Lambda + API Gateway** |
| notification-service | in-cluster SNS→SQS consumer | **EventBridge + SQS + Lambda** |
| web-app / admin-dashboard | container `LoadBalancer` | **S3 + CloudFront** |

The message: the same reusable playbook and the same verification bar apply to
every row — the audience picks the pain that matches their estate.

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
Using the Cognition-Partner-Workshops/otterworks repo, map the
search-service: how app/api/*.py routes reach the backend through
app/services/meilisearch_client.py, how app/config.py selects the
backend from env, what MeiliSearch provides today (self-managed on ECS
Fargate in infrastructure/terraform/modules/search plus the in-cluster
MeiliSearch in scripts/deploy-dev.sh), and every behavior the contract
suite tests/contract/test_search_contract.py asserts against
shared/openapi/search-service.yaml.
```

Expected: a tour of the backend seam (`query` / `suggest` / `advanced` / `index`
/ `analytics`), the env-driven config, the self-managed MeiliSearch footprint you
own today, and the contract behaviors — including that `/suggest` is prefix-first
type-ahead. Devin identifies that the API layer is backend-agnostic, so the
migration is an adapter swap behind a config flip.

<a id="act-2"></a>
### Act 2 — Migrate one component live, with verification

The core beat. Paste the playbook prompt. Devin provisions OpenSearch Serverless
as namespaced IaC, writes the OpenSearch adapter behind the existing interface,
deploys, runs the contract suite, catches a divergence, fixes it, and produces a
PR with the verification report.

```
!aws-cloud-native

Migrate search-service from self-managed MeiliSearch to Amazon
OpenSearch Serverless in Cognition-Partner-Workshops/otterworks.

- Backend seam: services/search-service/app/services/meilisearch_client.py
  (add a sibling opensearch_client.py implementing the same methods,
  selected by a new SEARCH_BACKEND env; default meilisearch so main is
  unchanged)
- Contract / source of truth: tests/contract/test_search_contract.py +
  shared/openapi/search-service.yaml
- IaC: add infrastructure/terraform/modules/opensearch (an OpenSearch
  Serverless SEARCH collection + encryption/network/data-access policies,
  namespaced) and extend the search-service IRSA policy with
  aoss:APIAccessAll
- Namespace: os-demo   (branch migration/opensearch-os-demo)
- Prove parity with the contract suite, then run a before/after load
  test and capture the OpenSearch Dashboards + CloudWatch view.
```

**The verification beat (the real bug).** The OpenSearch adapter uses a `match`
query for both search and suggest. It connects and returns `200` — so "looks
reasonable" review would ship it. But the contract suite catches the semantics
gap:

```
tests/contract/test_search_contract.py::TestSuggestEndpoint::test_suggest_valid_prefix
  FAILED
  Expected: prefix "tes" returns type-ahead suggestions
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
### Act 3 — Fan the menu out in parallel

The migration menu rows are independent, so run one **orchestrator** session that
spawns a child Devin session per row and monitors them — one agent fanning itself
out across a modernization wave, each child on its own namespace and branch,
each opening its own verified PR. Paste:

```
Act as the orchestrator for an AWS cloud-native modernization of
Cognition-Partner-Workshops/otterworks, using child Devin sessions to
parallelize the migration menu.

Spawn one child Devin session per row below. Give each child the repo,
its own namespace + branch (migration/<row>-<ns>), and tell it to follow
the !aws-cloud-native playbook (the repo Skill supplies the adapter
seams, IaC module locations, deploy wiring, and contract-test commands):
provision the managed/serverless target as namespaced least-privilege
IaC, wire the app behind its existing interface via a config flip, prove
parity with the repo's contract/flow tests, catch and fix any behavioral
divergence against the contract, and run a before/after performance test.

Rows:
1. search-service  -> Amazon OpenSearch Serverless   (flagship)
2. report-service  -> AWS Lambda + API Gateway
3. notification-service -> EventBridge + SQS + Lambda
4. auth-service identity -> Amazon Cognito

After launching, monitor the child sessions until each row's contract
suite is green. Summarize the results and call out every divergence the
children caught (e.g., the prefix/type-ahead gap in search, or a token
claim mismatch in the Cognito swap).
```

Each child runs in its own VM with scoped credentials and its own namespace, so
the parallel migrations are safe and never collide. This is the same verified
migration loop as a single session — run many times at once, from one parent.

<a id="act-4"></a>
### Act 4 — Confidence = programmatic verification

The gates that make every migration PR trustworthy:

- **Contract tests** (`tests/contract/test_search_contract.py` against the running
  service): endpoint existence, query/suggest/advanced semantics, response schema
  parity, status codes, and the `/health/ready` reason string — all gated by
  `shared/openapi/search-service.yaml`.
- **API flow tests** (`make test-api-flows`): the black-box suites that exercise
  each migrated component end to end through the gateway.
- **Terraform plan/validate**: the namespaced managed target provisions cleanly
  with least-privilege IAM, changing no shared or `main` resources.
- **Devin Review**: an automated reviewer on every PR.

A migration is "done" when the contract suite is green against the
managed/serverless backend — not when the new backend merely connects.

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
for the Theme-2 rows the Lambda + API Gateway console shows **scale-to-zero**
(invocations/duration/concurrency in CloudWatch) — you pay per request, not per
idle hour.

**5. The before is untouched.** Show `main` still has `search-service` on
MeiliSearch (the `SEARCH_BACKEND` default) and the `modules/search` MeiliSearch
module intact. The OpenSearch swap lives on the `migration/opensearch-<ns>`
branch and its namespaced collection, ready for review — and `terraform destroy
-target=module.opensearch` is the one-command revert.

---

<a id="concurrent"></a>
## Concurrent Runs

Every run carries a namespace suffix applied to the Terraform module/resources,
the branch, and any per-run k8s objects, so multiple migrations — and the Act 3
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
`terraform destroy -target=module.opensearch` plus dropping the `SEARCH_BACKEND`
flip.

---

<a id="key-takeaways"></a>
## Key Takeaways

- The value on display is **Devin doing the modernization**: reading a running estate, provisioning a managed/serverless AWS target as least-privilege IaC, wiring the app behind its existing interface off a reusable playbook, and proving parity against the OpenAPI contract — not a finished artifact to run.
- **Confidence comes from programmatic verification.** The repo's own contract tests gate every migration, and the demo shows a real divergence (OpenSearch `match` breaking MeiliSearch's prefix/type-ahead `/suggest`) being caught and fixed against the contract. "Connects and looks reasonable" review would have missed it.
- **The API contract is the source of truth.** Migrations reproduce behavior exactly (semantics gaps flagged and fixed, not silently accepted); the managed swap is a config flip behind a stable interface, never a caller rewrite.
- **One playbook, a whole menu, run in parallel.** The same `!aws-cloud-native` procedure covers search → OpenSearch Serverless, a service → Lambda + API Gateway, an event pipeline → EventBridge, identity → Cognito — and a parent session fans children out across the menu, each producing its own verified PR.
- **The cloud-native win is visible, and reversible.** The payoff shows up in the AWS console (OpenSearch Dashboards + CloudWatch, Lambda scale-to-zero), the before-state stays untouched on `main`, and namespaced IaC + `terraform destroy` make the whole story safe to repeat.
