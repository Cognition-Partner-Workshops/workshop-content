# Operating the Multi-Tenant Platform — Platform & DevOps Engineering Demo

A single linear demo that shows Devin working on the **platform itself** rather than
the application running on it: the tenant control plane that creates, expires,
suspends, and garbage-collects ephemeral OtterWorks environments, the three Terraform
layers underneath it, the Helm charts it ships, and the CI/CD path that carries
a change to a tenant.

The thread orients over the control plane → fixes a real cost-and-correctness
defect in the image retention policy that no CI job currently gates → adds the
gates that would have caught it → proves the behavior on a live ephemeral tenant
while the perpetual environment at <https://t-main.otterworks.app> keeps serving
`main` untouched → fans the rest of the platform backlog out across parallel
sessions.

## Table of Contents

- [Quick Start](#quick-start)
- [Repository](#repository)
- [The Two Planes](#two-planes)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Devin Works on the Platform](#part-1)
  - [Act 1 — Orient over the tenant control plane](#act-1)
  - [Act 2 — Fix the retention defect live, with verification](#act-2)
  - [Act 3 — Add the gate that would have caught it](#act-3)
  - [Act 4 — Prove it on an ephemeral tenant](#act-4)
  - [Act 5 — Fan the platform backlog out in parallel](#act-5)
  - [Act 6 — Confidence = programmatic verification](#act-6)
- [Part 2 — Confirm the Result in a Browser and on the Cluster](#part-2)
- [Where This Goes Next](#going-next)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

The control plane's verification loop runs entirely offline — the AWS CLI is
stubbed in the shell suites, so nothing touches an account:

```bash
cd demo-platform/dashboard && npm ci && npm run typecheck && npm run lint
cd ../..
shellcheck demo-platform/reaper/*.sh demo-platform/scripts/*.sh \
  demo-platform/lib/*.sh demo-platform/runner/entrypoint.sh
./demo-platform/reaper/test-reaper.sh
./demo-platform/reaper/test-idle-suspend.sh
./demo-platform/reaper/test-infra-sweep.sh
./demo-platform/reaper/test-orphan-sweep.sh
```

The infrastructure loop is Terraform validation, per layer:

```bash
cd infrastructure/terraform && terraform fmt -check -recursive \
  && terraform init -backend=false && terraform validate
cd ../../platform/terraform && terraform fmt -check -recursive \
  && terraform init -backend=false && terraform validate
cd ../../demo-platform/infra/terraform && terraform fmt -check -recursive \
  && terraform init -backend=false && terraform validate
```

Prerequisites: Node 20, `shellcheck`, Terraform 1.7, `helm`, and — for Act 4
only — AWS credentials for the workshop account and a `kubectl` context for the
EKS cluster `otterworks-dev`.

---

<a id="repository"></a>
## Repository

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) — a
  polyglot platform (11 backend microservices across 9 languages plus a React
  `frontend/client-app` and an Angular `frontend/admin-dashboard`) *and* the
  multi-tenant platform that runs it:
  - `demo-platform/` — the control plane: a Next.js ops dashboard
    (`demo-platform/dashboard/`), a DynamoDB control table, the reaper
    (`demo-platform/reaper/reaper.sh`), idle suspend
    (`demo-platform/reaper/idle-suspend.sh`), the infrastructure orphan sweep
    (`demo-platform/reaper/infra-sweep.sh`), a Kubernetes Job runner
    (`demo-platform/runner/`), and its own Terraform and Helm chart.
  - `platform/terraform/` — the shared platform layer: `modules/vpc`,
    `modules/eks`, `modules/ecr`.
  - `infrastructure/terraform/` — the application layer: `modules/database`,
    `cache`, `messaging`, `storage`, `search`, `auth`, `irsa`, `monitoring`.
  - `infrastructure/helm/` — 13 service charts; `scripts/deploy-tenant.sh`,
    `scripts/teardown-tenant.sh`, `scripts/tenant-scale.sh`, and
    `scripts/lib/tenant-common.sh` for per-tenant operations.
  - `.github/workflows/` — `ci.yml`, `cd-tenant.yml`, `docker-build.yml`,
    `security-scan.yml`, `sast-auto-remediate.yml`.

---

<a id="two-planes"></a>
## The Two Planes

| | Platform plane (shared, always on) | Multi-tenant plane (ephemeral, per checkout) |
|---|---|---|
| Compute | one ingress-nginx + NLB, cert-manager wildcard TLS, external-dns | namespace `otterworks-<id>` with all services plus in-namespace Redis and MeiliSearch |
| State | one RDS instance, shared S3 and DynamoDB (tenant-prefixed), the DynamoDB control table | database `otterworks_<id>`, its own prefix in the shared buckets and tables |
| Entry | the ops dashboard and the reaper CronJob | `t-<id>.demo.otterworks.app` and `api-t-<id>.demo.otterworks.app` |

A tenant id comes from the git branch: `branch_tenant_id` in
`scripts/lib/tenant-common.sh` strips the `workshop-` or `demo-` prefix and
sanitizes the rest to an RFC-1123 label, so branch `workshop-lab7` becomes tenant
`lab7`, namespace `otterworks-lab7`, database `otterworks_lab7`, host
`t-lab7.demo.otterworks.app`.

One tenant is different. `main` is the perpetual environment at
<https://t-main.otterworks.app> — the user SPA at `/`, the admin dashboard at
`/admin`, health at `/api/health`. It carries `persistent: true` in the control
table, which is the one flag the reaper skips (`reaper.sh` → `reap_expired`), the
idle scan skips (`idle-suspend.sh`), and the dashboard refuses to check in or
inject bugs into. Everything else is TTL'd (72 hours when CD creates it),
idle-suspended after `idle_after_seconds` (default 3600) with no ingress traffic,
and eventually reaped.

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Platform code | Gates |
|---|---|---|
| **Before** | `platform/terraform/modules/ecr/main.tf` keeps the last 10 images per repository with `tagStatus = "any"` — the golden `main` tag is expirable by tenant build churn, and `scripts/deploy-tenant.sh` → `resolve_tag` falls back to `main` for every service a tenant's branch did not build | `.github/workflows/ci.yml` filters `infrastructure/**`, `demo-platform/**`, and `scripts/**`. A change under `platform/**` matches no filter, so it runs **no** job. There is no `helm lint` step anywhere. The root `Makefile` still points `build-web`, `test`, and `lint` at `frontend/web-app`, which does not exist |
| **After** | a PR branch with a retention policy that protects the golden and per-tenant tags, the CI gates that cover the platform layer and the charts, and a Makefile whose targets run | the four control-plane shell suites, ShellCheck, dashboard typecheck/lint, `terraform validate` on all three layers, and `helm lint` on all 14 charts |

The defect is worth stating precisely, because it is the kind that only platform
engineers see. `.github/workflows/cd-tenant.yml` publishes three tags per build:
an immutable `<slug>-<sha>`, the environment's `tenant-<id>`, and — only from this
repository's `main` — `main`, the golden image. A tenant then resolves each
service through `resolve_tag`: its own `tenant-<id>` if that tag exists, else
`main`, else the newest image pushed to the repository. Ten tenant builds of one
service are enough for a keep-last-10 rule with `tagStatus = "any"` to expire the
image that holds `main`, at which point the fallback silently becomes "whichever
branch built last" — another tenant's code — which is exactly the outcome the
comment above `resolve_tag` says the tag scheme exists to prevent.

The verification loop is the point of the demo: platform code fails silently by
nature. A tenant that was never examined looks exactly like one that was
correctly left alone, and an image that was expired looks exactly like one that
was never built. Nothing here is trusted because it reads correctly — it is
trusted because a suite asserts it, and because a live ephemeral tenant shows it.

---

<a id="part-1"></a>
## Part 1 — Devin Works on the Platform

<a id="act-1"></a>
### Act 1 — Orient over the tenant control plane

Open the repo and ask Devin to map the lifecycle. With DeepWiki over the repo,
Devin typically maps a control plane like this in minutes (coverage depends on
repo structure).

```
Using the Cognition-Partner-Workshops/otterworks repo, map the
multi-tenant control plane end to end.

Cover:
1. The tenant lifecycle in demo-platform/: how the dashboard
   (demo-platform/dashboard/) checks a tenant out, what the DynamoDB
   control table stores per tenant (see
   demo-platform/docs/control-table-schema.md), and how the runner
   Jobs in demo-platform/runner/ perform the mutations.
2. The three background loops in demo-platform/reaper/: reaper.sh
   (TTL expiry), idle-suspend.sh (scale-to-zero), and
   infra-sweep.sh (orphan AWS resources). For each, state what it
   reads to make a decision, what it deletes or scales, and every
   guard that makes it skip.
3. Isolation: how scripts/lib/tenant-common.sh derives the tenant
   id, namespace, and database name from a branch name, and where
   scripts/deploy-tenant.sh creates the per-tenant database, Redis,
   MeiliSearch, NetworkPolicy, and ingress hosts.
4. The IaC layers: platform/terraform (vpc, eks, ecr),
   infrastructure/terraform (database, cache, messaging, storage,
   search, auth, irsa, monitoring), and
   demo-platform/infra/terraform, plus how the IRSA wildcard in
   infrastructure/terraform/modules/irsa lets one role serve every
   otterworks-* tenant namespace.
5. The CI/CD path: the paths-filter change detection in
   .github/workflows/ci.yml and the branch-aware deploy in
   .github/workflows/cd-tenant.yml.

Output a markdown map with a table of the control-table attributes
that drive reaping and suspension, and a second table of which
paths in the repo are covered by which CI job. Flag any path that
is covered by no job at all.
```

Expected: a map naming `expires_at`, `persistent`, `idle_since`, `req_count`,
`was_running`, and the `CONFIG#reaper` keys (`enabled`, `grace_seconds`,
`idle_after_seconds`, `sweep_infra`, `sweep_infra_delete`), plus a coverage table
whose last row is the finding that drives Act 3: **`platform/**` matches no CI
filter**, so the VPC, EKS, and ECR layer merges ungated.

<a id="act-2"></a>
### Act 2 — Fix the retention defect live, with verification

The core beat. Devin traces the tag lifecycle across three files that no single
service owner reads together — a Terraform module, a workflow, and a deploy
script — then changes the policy and pins the intent with a test.

```
In Cognition-Partner-Workshops/otterworks, fix the ECR retention
policy so tenant build churn cannot expire the golden image.

Current state to confirm first, and quote in the PR description:
- platform/terraform/modules/ecr/main.tf creates one lifecycle
  rule: keep the last 10 images, tagStatus "any", on repositories
  whose tags are IMMUTABLE.
- .github/workflows/cd-tenant.yml pushes up to three tags per
  build: <slug>-<sha>, tenant-<id>, and main (golden, only from
  this repository's main branch).
- scripts/deploy-tenant.sh -> resolve_tag() resolves a service to
  tenant-<id>, else main, else the newest image in the repository.

Change platform/terraform/modules/ecr/main.tf so the policy
retains the golden main tag and the per-tenant tenant-* tags, and
expires only the untagged and short-lived <slug>-<sha> builds, with
retention counts exposed as module variables (keep the existing
defaults' spirit: bounded storage, not unbounded). Respect ECR
lifecycle-policy semantics: rulePriority ordering, and a
tagStatus "any" rule must have the highest priority number.

Verify:
- cd platform/terraform && terraform fmt -check -recursive
  && terraform init -backend=false && terraform validate
- Print the rendered policy JSON for one repository and paste it in
  the PR description, showing the rule set and its ordering.
- Add a test that asserts the rendered policy never expires the
  main or tenant-* tags, and that it does expire untagged images.
  Put it where the repo already keeps this kind of check, and give
  the exact command that runs it.

In the PR description, state the blast radius of the current
policy: which tenants break, how it presents to a user, and why
the failure is silent.
```

**The verification beat.** A plausible first cut adds a `tagPrefixList` rule for
`main` and `tenant-` and leaves the existing keep-last-10 rule as written. It
formats, initializes, and validates cleanly — Terraform does not evaluate ECR
rule semantics — and the assertion is what fails, because the untouched
`tagStatus = "any"` rule still selects the golden image:

```
  FAIL - golden 'main' tag is protected from expiry
         (rule 1: tagStatus=any, imageCountMoreThan=10 selects any
          image, including the one tagged 'main')
```

The fix is to reorder and re-scope the rules — protected tag prefixes first with
their own retention, the catch-all rule last and narrowed — not to relax the
assertion. Re-run and the gate is green:

```bash
cd platform/terraform && terraform validate
#   Success! The configuration is valid.
```

The point: `terraform validate` passing is not the same as the policy being
correct, and "keep the last 10 images" is a reasonable-looking line that quietly
couples every tenant's fallback image to how often other tenants build.

<a id="act-3"></a>
### Act 3 — Add the gate that would have caught it

The defect merged because nothing looked at it. Close that, plus the two other
gaps Act 1 surfaced.

```
In Cognition-Partner-Workshops/otterworks, close the CI coverage
gaps in the platform layer.

1. .github/workflows/ci.yml: the paths-filter in detect-changes has
   no entry matching platform/**, so changes to
   platform/terraform (vpc, eks, ecr) run no job. Add a filter and
   a job that runs, in platform/terraform:
     terraform fmt -check -recursive
     terraform init -backend=false
     terraform validate
   Model it on the existing infrastructure and
   demo-platform-terraform jobs, and pin the action versions the
   same way the newer jobs in this file do.

2. Add a chart validation job that runs `helm lint` over every
   chart in infrastructure/helm/ (13 charts) and
   demo-platform/helm/demo-platform, triggered when either path
   changes. No chart lint exists in CI today.

3. The root Makefile points build-web, test, and lint at
   frontend/web-app, which does not exist; the real directory is
   frontend/client-app, which is what ci.yml and cd-tenant.yml
   already use. Fix those three targets, and add a
   test-demo-platform target that runs the four control-plane
   suites (demo-platform/reaper/test-reaper.sh,
   test-idle-suspend.sh, test-infra-sweep.sh,
   test-orphan-sweep.sh).

Verify locally and paste the output:
  make -n build-web && make -n lint
  make test-demo-platform
  for c in infrastructure/helm/* demo-platform/helm/demo-platform; \
    do helm lint "$c"; done
  cd platform/terraform && terraform fmt -check -recursive \
    && terraform init -backend=false && terraform validate
Fix anything the new gates find. Report each finding rather than
weakening the gate.
```

Expected: three new or corrected gates, `make test-demo-platform` reporting the
four suites green, and `helm lint` output for all 14 charts. Any chart or format
finding the new gates surface gets fixed in the same PR and listed in the
description — the gate arrives already green, which is the only way a gate
survives.

<a id="act-4"></a>
### Act 4 — Prove it on an ephemeral tenant

Offline suites prove the policy. A live tenant proves the fallback the policy
protects is real. This runs against an ephemeral tenant; the perpetual
environment keeps tracking `main` and is not touched.

```
In Cognition-Partner-Workshops/otterworks, stand up an ephemeral
tenant for this platform branch and prove the image-tag fallback
that Act 2's retention policy protects.

1. Push the platform branch as workshop-<id> (pick a short
   lowercase id). The paths this branch changes -- platform/**,
   .github/**, Makefile -- are deliberately not in the
   `deployable` filter of .github/workflows/cd-tenant.yml, so the
   push alone will not deploy. Confirm that in the run, then start
   cd-tenant.yml manually on the branch via workflow_dispatch with
   rebuild_all=true, which builds every service and syncs the
   tenant. CD creates a tenant with a 72h TTL when the branch has
   none.

2. Once the run is green, verify the tenant:
     curl -s https://t-<id>.demo.otterworks.app/api/health
     demo-platform/scripts/tenant.sh status <id>
     kubectl -n otterworks-<id> get deploy \
       -o custom-columns='NAME:.metadata.name,\
IMAGE:.spec.template.spec.containers[0].image'
   Record which deployments run a tenant-<id> tag and which run the
   golden main tag. That split is the fallback in
   scripts/deploy-tenant.sh -> resolve_tag(), live.

3. Exercise the lifecycle the platform owns, capturing output for
   each step: scripts/tenant-scale.sh <id> down, then up (the
   idle-suspend path, scale-to-zero and back);
   demo-platform/scripts/tenant.sh extend <id> 8h (TTL); and
   demo-platform/scripts/tenant.sh status <id> after each.

4. Confirm the perpetual environment is untouched: t-main still
   serves main at https://t-main.otterworks.app/api/health, and
   demo-platform/scripts/tenant.sh status main still reports the tenant;
   its control-table record remains `persistent: true` with no TTL change.

Summarize as a markdown table: step, command, observed result. Do
not run tenant.sh checkin, inject-bug.sh, or any scale/teardown
command against main.
```

Expected: a green CD run whose `plan` job reports `deploy=false` for the push and
`deploy=true` for the dispatch; a tenant answering on its own hostname; a
deployment table mixing `tenant-<id>` and `main` image tags, which is the
fallback the retention fix protects, visible in a real cluster; a scale-to-zero
and back; and `t-main` still healthy and still `persistent`.

<a id="act-5"></a>
### Act 5 — Fan the platform backlog out in parallel

The rest of the platform backlog is independent work on the same control plane.
Run one orchestrator session that spawns a child session per item, each on its
own branch with its own gate.

| Session | Work | Primary paths |
|---|---|---|
| 1 | Tighten the infra-sweep ownership assertions for Elastic IPs and Route53 records | `demo-platform/reaper/infra-sweep.sh` + `test-infra-sweep.sh` |
| 2 | Make the idle-suspend threshold and skip rules per-tenant, from the control table | `demo-platform/reaper/idle-suspend.sh` + `test-idle-suspend.sh` |
| 3 | Reconcile the platform docs with the implemented control table | `demo-platform/docs/architecture.md`, `docs/MULTI-TENANT-RUNBOOK.md` |
| 4 | Add ECR repository-level retention variables and wire them from the platform root module | `platform/terraform/modules/ecr/`, `platform/terraform/main.tf` |

```
Act as the orchestrator for a platform-hardening wave on
Cognition-Partner-Workshops/otterworks, using child Devin sessions
to parallelize the work.

Spawn one child session per item below. Give each child the repo,
its own branch (platform/sweep-guards, platform/idle-config,
platform/docs-reconcile, platform/ecr-retention), and this shared
context: the control plane lives in demo-platform/, the AWS CLI is
stubbed in demo-platform/reaper/test-*.sh so the suites run
offline, and the gate for any control-plane change is
  shellcheck demo-platform/reaper/*.sh demo-platform/scripts/*.sh \
    demo-platform/lib/*.sh demo-platform/runner/entrypoint.sh
  ./demo-platform/reaper/test-reaper.sh
  ./demo-platform/reaper/test-idle-suspend.sh
  ./demo-platform/reaper/test-infra-sweep.sh
  ./demo-platform/reaper/test-orphan-sweep.sh
plus terraform fmt/init/validate for any Terraform change.

Items:
1. infra-sweep.sh: require an explicit ownership tag, not only a
   dead-cluster tag, before releasing an Elastic IP; and add tests
   that the platform apex and _acme-challenge records can never be
   selected for deletion.
2. idle-suspend.sh: read idle_after_seconds per tenant from the
   control-table item, falling back to the CONFIG#reaper value and
   then the 3600s default; keep the persistent and active-chaos
   skips intact and add tests for the precedence order.
3. demo-platform/docs/architecture.md states that tenant/branch
   mapping and lifecycle state are not tracked, which the
   implemented DynamoDB control table contradicts. Reconcile the
   docs with the code, and cross-check
   docs/MULTI-TENANT-RUNBOOK.md for the same drift.
4. Expose ECR retention counts as variables on
   platform/terraform/modules/ecr and wire them from
   platform/terraform/main.tf, keeping the golden-tag protection
   from the retention fix.

Every child must leave its gate green and must not change any
assertion to make it pass -- report a conflict instead. Monitor the
children until each is done, then summarize which suites ran, what
each child changed, and any safety guard a child had to add.
```

The children inherit the organization's scoped credentials and each writes to its
own branch, so the parallel runs never collide — the same offline safety suites,
run four times at once from one parent.

<a id="act-6"></a>
### Act 6 — Confidence = programmatic verification

What makes a platform change reviewable here:

- **Control-plane suites** — `test-reaper.sh`, `test-idle-suspend.sh`,
  `test-infra-sweep.sh`, `test-orphan-sweep.sh`. The AWS CLI is stubbed, so the
  destructive paths are exercised without an account. The infra-sweep suite pins
  the load-bearing invariant directly: a failed cluster lookup must never be read
  as "no clusters exist", because that inverts the safety check and makes the live
  estate look orphaned.
- **ShellCheck** over the control-plane scripts, and dashboard `typecheck` +
  `lint`.
- **Terraform** `fmt -check -recursive`, `init -backend=false`, `validate` on
  `platform/terraform`, `infrastructure/terraform`, and
  `demo-platform/infra/terraform`.
- **`helm lint`** over the 13 service charts and the platform chart.
- **A live ephemeral tenant** — the CD run, the tenant hostname, and the
  deployment image tags, with `t-main` untouched as the control.
- **Devin Review** on every PR, which also helps humans digest a diff that spans
  Terraform, a workflow, and a shell script.

A platform change is done when the suites are green *and* a tenant demonstrates
the behavior — not when the plan applies.

---

<a id="part-2"></a>
## Part 2 — Confirm the Result in a Browser and on the Cluster

Two hostnames tell the whole story. The perpetual environment, which tracks
`main` and is the reference for what the platform is supposed to look like:

```bash
curl -s https://t-main.otterworks.app/api/health
# {"status":"healthy","service":"web-app"}
```

Open <https://t-main.otterworks.app/> for the user SPA and
<https://t-main.otterworks.app/admin> for the admin dashboard. Nothing in this
demo changed it.

Then the ephemeral tenant from Act 4, on its own hostname, its own namespace, and
its own database:

```bash
curl -s https://t-<id>.demo.otterworks.app/api/health
kubectl -n otterworks-<id> get deploy,svc,networkpolicy
demo-platform/scripts/tenant.sh status <id>
```

The image tags on those deployments are the artifact: the services the branch
built carry `tenant-<id>`, the rest carry `main`. Before the retention fix, ten
tenant builds of a service were enough to expire the `main` image the rest of
that column depends on. After it, the golden tag is retained by policy.

Close the loop with the gates, which now cover the layer the change lives in:

```bash
make test-demo-platform
cd platform/terraform && terraform validate
#   Success! The configuration is valid.
```

---

<a id="going-next"></a>
## Where This Goes Next

- **Event-driven platform operations** — a Prometheus alert routed through the
  webhook path in `observability/` opens an admin-service incident and can start a
  Devin session against it, so a reaper that stopped running or a tenant that
  failed to deploy becomes a session instead of a ticket. See
  [Automations](https://docs.devin.ai/product-guides/automations).
- **Scheduled sessions** — run the cost and drift work on a cadence: a weekly
  report of the `DRY_RUN=true` infra-sweep findings, or a monthly review of tenant
  TTLs and idle-suspend savings against `demo-platform/docs/cost-and-scale.md`.
- **Child-session fan-out** — the Act 5 orchestrator is how a platform team of
  two or three keeps up with a backlog sized for six.
- **Knowledge and playbooks** — the control-table schema, the tenant naming rules,
  and the "never touch the perpetual tenant" constraint belong in Knowledge notes
  and a playbook, so every future session and every child session inherits them
  instead of rediscovering them.

---

<a id="key-takeaways"></a>
## Key Takeaways

- The work on display is **platform engineering, not application work**: a
  DynamoDB-backed tenant control plane, three background loops that delete and
  scale real infrastructure, three Terraform layers, 14 Helm charts, and a
  branch-aware CD path. Devin reads that whole surface, not one service.
- **Platform defects are cross-file and silent.** The retention defect is only
  visible when a Terraform lifecycle rule, a workflow's tag scheme, and a deploy
  script's fallback are read together — and its symptom is a tenant quietly
  running another tenant's image.
- **Coverage gaps are findable.** Mapping repo paths against CI filters surfaced a
  whole IaC layer that merged with no job attached, and the same PR that fixes the
  defect adds the gate that would have caught it.
- **Destructive automation is testable.** The reaper, idle-suspend, and sweep
  suites stub the AWS CLI, so deletion logic and its safety guards are exercised
  offline, on every change, without an account.
- **The blast radius stays bounded.** Changes are proven on an ephemeral,
  TTL'd tenant with its own namespace, database, and hostname, while the perpetual
  environment keeps serving `main` — the always-on proof that the platform works.
- **The platform backlog parallelizes.** Independent hardening items run as
  concurrent sessions on separate branches, each landing a PR gated by the same
  suites.
