# Pipeline and Infrastructure Operations — DevOps / Platform Engineering Demo

A single-thread demo showing Devin operating as a platform engineering agent on
the OtterWorks polyglot monorepo and its surrounding IaC repos. The thread
covers four beats: a CI failure that Devin diagnoses from the logs and fixes
hands-free off the failure event; a Terraform/Helm change where a human reviews
the plan before anything is applied; a platform-wide change (base image and
pinned-action hygiene) fanned out across services and repos with child
sessions; and Devin Review enforcing platform standards on infra PRs raised by
application teams.

<a id="toc"></a>
## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Part 1 — Red Build to Green: Event-Driven CI Triage](#part-1)
- [Part 2 — Terraform and Helm: Plan Is Devin's, Apply Is Yours](#part-2)
- [Part 3 — Platform-Wide Change with Child Sessions](#part-3)
- [Part 4 — Devin Review as the Platform Standards Gate](#part-4)
- [The Shared Context Layer](#shared-context)
- [What Still Needs a Human](#human-in-the-loop)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Paste this into Devin to wire the CI-failure trigger the whole first part runs
on:

```
In the Cognition-Partner-Workshops/otterworks repo, create a GitHub
Actions workflow at .github/workflows/ci-auto-triage.yml that turns
CI failures into Devin sessions:

(1) trigger on workflow_run (types: completed) for the existing
    "CI Pipeline" workflow (.github/workflows/ci.yml),
(2) proceed only when conclusion == failure and the head branch is
    not a devin/... branch (bot-loop prevention),
(3) collect the failed run's id, head branch, head SHA, and the
    names of the failed jobs from the GitHub API,
(4) call the Devin v3 API (secrets DEVIN_API_KEY, DEVIN_ORG_ID —
    the same secrets sast-auto-remediate.yml already uses) to
    create a session whose prompt includes the run URL, head
    branch, and failed job names, and instructs Devin to read the
    job logs, find the root cause, fix it on the same branch, and
    push,
(5) comment on the associated PR (if one exists) with the Devin
    session link.

Follow the structure and API-call conventions already used in
.github/workflows/sast-auto-remediate.yml. Output: the new
workflow file plus a short docs/CI_AUTO_TRIAGE.md describing the
trigger payload and the loop guard.
```

While that session runs, walk the repo landscape below.

---

<a id="repositories"></a>
## Repositories

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) —
  polyglot monorepo: 12 service directories under `services/` (Go, Java, Rust,
  Python, Node.js, Kotlin, Scala, Ruby, C#, plus a legacy portal), two
  frontends, 13 Helm charts under `infrastructure/helm/`, and two Terraform
  roots — `platform/terraform/` (VPC, EKS, ECR, Karpenter) and
  `infrastructure/terraform/` (RDS, ElastiCache, DynamoDB, S3, SNS/SQS,
  Cognito). CI lives in `.github/workflows/ci.yml` (path-filtered per-service
  build/test jobs), `docker-build.yml` (ECR image matrix), and
  `sast-auto-remediate.yml` (an existing event-driven Devin integration).
  Deploys run through `scripts/deploy-dev.sh` — there is no apply pipeline.
- [quickapp-iac](https://github.com/Cognition-Partner-Workshops/quickapp-iac) —
  app-specific IaC: 11 Helm charts under `charts/` with per-environment
  overrides in `environments/{dev,staging,prod}/values.yaml`.
- [ordermanager-iac](https://github.com/Cognition-Partner-Workshops/ordermanager-iac) —
  app-specific IaC: one chart at `helm/ordermanager/`, ArgoCD applications
  under `argocd/`, and a build/push pipeline at `ci/build-push.yaml`.
- [platform-engineering-shared-services](https://github.com/Cognition-Partner-Workshops/platform-engineering-shared-services) —
  the shared platform: CDK (TypeScript) under `cdk/` for VPC/EKS/ECR/DNS,
  shared Helm releases (`helm-releases/` — ingress-nginx, cert-manager,
  Prometheus/Grafana, ArgoCD), and baseline Kubernetes policy templates under
  `k8s/` (default-deny NetworkPolicy, standard ResourceQuota).

---

<a id="part-1"></a>
## Part 1 — Red Build to Green: Event-Driven CI Triage

The Quick Start session lands `ci-auto-triage.yml` as a PR. Review and merge
it. From that point, the trigger is live: the payload is the `workflow_run`
event GitHub emits when `CI Pipeline` completes — run id, head branch, head
SHA, and conclusion — and the workflow forwards the failed job names and run
URL into a Devin session prompt. No human types anything when a build goes
red.

### Trigger it live

Break the build the way an ordinary feature PR breaks it. Create a working
branch and introduce a realistic defect — for example, change a function
signature in `services/api-gateway` without updating its callers, or bump a
dependency in `services/collab-service/package.json` to a version that fails
`npm test`. Push and open a PR against `main`.

The sequence in the PR's Checks tab is:

1. `ci.yml` runs its path-filtered jobs — only the jobs for the changed
   service run (the workflow uses `dorny/paths-filter` to skip untouched
   services).
2. The affected job fails.
3. `ci-auto-triage.yml` fires on the `workflow_run` completion event, sees
   `conclusion: failure`, and calls the Devin API.
4. A comment appears on the PR linking the Devin session.

Open the session. Devin pulls the failed job's logs, reads the actual compiler
or test output (not a summary of it), traces the failure to the change on the
branch, fixes it in the right language for that service, runs that service's
own test command, and pushes to the same branch. The push re-triggers
`ci.yml` — the same pipeline that caught the failure verifies the fix. That is
the closed loop: red build → session → fix commit → green build, with the
platform team never paged.

This is the work an IDE assistant cannot do: nobody was in an editor. The
trigger was a CI event, the diagnosis happened on Devin's own VM against real
logs, and the result is a verified commit. The pattern already runs in this
repo for security findings (`sast-auto-remediate.yml` routes Trivy and
SonarCloud failures to Devin the same way) — Part 1 extends it to ordinary
build failures.

---

<a id="part-2"></a>
## Part 2 — Terraform and Helm: Plan Is Devin's, Apply Is Yours

Infrastructure changes carry a different risk profile than application code,
so the demo is explicit about the boundary: **Devin produces the change and
the plan; a human approves; the apply happens through the team's existing
deploy path.** In otterworks there is no Terraform apply pipeline — `ci.yml`
runs only `terraform fmt`/backendless `init`/`validate`, and real applies run
through `scripts/deploy-dev.sh`. The demo does not pretend otherwise.

Paste:

```
In Cognition-Partner-Workshops/otterworks, make a right-sizing
change to the dev infrastructure:

1. In infrastructure/terraform/, enable deletion protection on the
   RDS PostgreSQL instance and add a 7-day backup retention window
   if not already set.
2. In infrastructure/helm/document-service/values.yaml and
   infrastructure/helm/search-service/values.yaml, set explicit
   CPU/memory requests and limits consistent with the other charts
   in infrastructure/helm/.

Verification, in order:
- terraform fmt -check and terraform validate in
  infrastructure/terraform/
- terraform plan against the dev workspace, and paste the full
  plan output (resource-by-resource) into a PLAN.md at the branch
  root
- helm lint infrastructure/helm/document-service and
  infrastructure/helm/search-service

Do NOT run terraform apply or helm upgrade. The output of this
task is the diff plus the plan for human review.
```

Review the PR the way a platform engineer actually reviews infra changes: read
`PLAN.md` line by line. The plan shows exactly which attributes change on
which resources — an in-place modify on the RDS instance, no
destroy-and-recreate. That distinction (modify vs. replace) is the thing plan
review exists to catch, and it is in the PR before anyone touches the
environment.

When the plan looks right, a human merges and a human applies — here, via
`scripts/deploy-dev.sh --skip-platform --skip-build` (which re-applies
`infrastructure/terraform` and re-deploys the charts), or by running
`terraform apply` directly in `infrastructure/terraform/`. Devin's boundary in
this demo is the PR; the apply credentials and the moment of execution stay
with the team. In organizations with an Atlantis- or pipeline-driven apply,
the same PR slots into that flow — the boundary just moves to the pipeline's
approval step.

---

<a id="part-3"></a>
## Part 3 — Platform-Wide Change with Child Sessions

Platform work is rarely one repo deep. The hygiene sweep below is the kind of
change that sits in a platform team's backlog for months because it is
tedious, wide, and individually low-stakes: unpinned base images and
tag-pinned (rather than SHA-pinned) GitHub Actions.

The current state is verifiable in the repos:

- `services/file-service/Dockerfile` builds `FROM rust:latest` — a mutable tag
  in a production build.
- `ci.yml` uses `actions/checkout@v4` in 16 places, `docker-build.yml` in 1,
  and `sast-auto-remediate.yml` in 2 — all tag-pinned. Only
  `security-scan.yml` pins by commit SHA
  (`actions/checkout@11bd719... # v4`), which is the hardened pattern the rest
  should follow.
- `ordermanager-iac/ci/build-push.yaml` and `ordermanager-iac/docker/Dockerfile`
  carry the same classes of reference in a second repo.

One engineer doing this by hand context-switches across 12 Dockerfiles, 4
workflow files, and 2+ repos. Instead, run it as an orchestrated fan-out:

```
Act as the orchestrator for a supply-chain hygiene sweep across
two repos in the Cognition-Partner-Workshops org: otterworks and
ordermanager-iac. Spawn child Devin sessions to parallelize, then
aggregate.

Child 1 — otterworks Dockerfiles: audit all 12 Dockerfiles under
services/ plus the two frontend Dockerfiles. Replace any mutable
base tag (services/file-service/Dockerfile uses FROM rust:latest)
with the current stable version-pinned tag. Keep multi-stage
structure intact. Verify each touched image still builds with
docker build.

Child 2 — otterworks workflows: in .github/workflows/, pin every
third-party action reference in ci.yml, docker-build.yml, and
sast-auto-remediate.yml to a full commit SHA with a trailing
version comment, matching the style already used in
security-scan.yml. Do not change trigger or job logic.

Child 3 — ordermanager-iac: apply the same two treatments to
ci/build-push.yaml and docker/Dockerfile.

Each child works on its own branch in its own repo. After all
children complete, post a summary table here: repo, files
changed, images/actions pinned, build verification result.
```

Each child session runs on its own VM with its own clone, so the builds and
audits run genuinely in parallel; the parent aggregates the results into one
table and the changes land as one reviewable PR per repo. The same pattern
scales to the platform team's other fan-out work — a new required status
check, a runner migration, a logging-library bump — across however many repos
the org actually has.

This is the second cloud-agent-only beat: the work is horizontal. No single
engineer's editor session spans two repos and fourteen Dockerfiles with
per-file build verification.

---

<a id="part-4"></a>
## Part 4 — Devin Review as the Platform Standards Gate

Platform teams spend a large share of their week reviewing other teams'
infrastructure PRs against standards only the platform team has memorized.
Devin Review moves that gate into the PR itself.

### Encode the standard once

The standards already exist as artifacts: otterworks charts under
`infrastructure/helm/` ship `networkpolicy.yaml` and `servicemonitor.yaml` in
their templates, and `platform-engineering-shared-services` carries the
baseline policies at `k8s/network-policies/default-deny.yaml` and
`k8s/resource-quotas/standard.yaml`. Capture them as a knowledge note so
Devin applies them in reviews and sessions across the org:

```
Create a knowledge note titled "Helm chart platform standards"
triggered when reviewing or writing Helm charts in the
Cognition-Partner-Workshops org. Content: every chart is expected
to include a NetworkPolicy restricting ingress (see
otterworks infrastructure/helm/*/templates/networkpolicy.yaml and
platform-engineering-shared-services
k8s/network-policies/default-deny.yaml), a ServiceMonitor when
metrics are exposed, explicit resource requests/limits within
namespace quotas (k8s/resource-quotas/standard.yaml), and health
probes. Cite the missing template file path when flagging a gap.
```

### The gate in action — a human's PR

The `quickapp-iac` charts are a real example of drift: the 11 charts under
`charts/` template only `deployment.yaml`, `ingress.yaml`, and `service.yaml`
— no NetworkPolicy, no ServiceMonitor. Have a teammate (or a second browser
profile) open a PR to `quickapp-iac` adding a chart for a new service by
copying an existing one, which propagates the gap.

With Devin Review enabled on the repo, review comments typically arrive on
the PR within minutes: the missing `networkpolicy.yaml` and `servicemonitor.yaml`
are flagged with reference to the standard, at the file and line where the
template set is defined — before a platform engineer has looked at the PR at
all. The application team gets the feedback while the context is fresh, and
the platform team reviews an already-conformant PR instead of writing the
same comment for the tenth time.

### The loop closes on Devin's own PRs too

The PRs Devin opened in Parts 1–3 get the same treatment: Devin Review
comments on Devin's sessions' PRs, and the authoring session reads the
feedback and pushes fixes. Open the Part 3 otterworks PR and look at the
review thread — reviewer and author are both agents, and the human's job
collapses to reading a resolved thread and merging. Standards enforcement
stops being a per-PR human activity and becomes a property of the repo.

---

<a id="shared-context"></a>
## The Shared Context Layer

Why the output above is org-specific rather than generic:

- **Knowledge notes** — the Helm standards note from Part 4 applies to future
  sessions and reviews in most cases without being re-stated; the same
  mechanism carries the team's Terraform conventions and apply boundary.
- **DeepWiki** — indexed over otterworks, it gives sessions an architecture
  map of the 12 services, the two Terraform roots, and the deploy script
  before they read a line of code (coverage depends on repo structure).
- **Playbooks** — the CI-triage prompt and the hygiene-sweep orchestration
  are candidates to become playbooks, so the next failure or the next sweep
  is a one-line invocation with the procedure versioned centrally.
- **MCP integrations** — sessions can query the org's operational systems
  (ticketing, monitoring) through MCP servers, so a triage session can read
  the alert or ticket that motivated it rather than starting cold.

Each artifact created in this demo — the workflow, the note, the summary
tables — makes the next session better. That compounding is the difference
between a team resource and a per-seat tool.

---

<a id="human-in-the-loop"></a>
## What Still Needs a Human

Honest boundaries for this job function:

- **Terraform apply and Helm upgrade against shared environments.** In these
  repos, applies run through `scripts/deploy-dev.sh` or a manual
  `terraform apply` with credentials Devin's sessions are not given for
  production-like environments. Devin's deliverable is the diff and the plan.
- **Plan judgment.** Devin can produce and annotate a plan, but deciding
  whether a resource replacement is acceptable during business hours is a
  human call.
- **Merge authority and required approvals.** Devin Review comments; it does
  not merge. Branch protection and CODEOWNERS stay in force.
- **Secrets and cloud credentials.** Rotating the AWS credentials the
  workflows use, and deciding what access a session gets, remain platform-team
  actions.
- **Ambiguous failures.** CI triage typically lands clean fixes for
  code-level failures; infrastructure-flake or credential-expiry failures
  (like a Docker build failing on credential loading) usually end with Devin's
  diagnosis in a comment and a human fixing the secret — which is still faster
  than starting the investigation from zero.

---

<a id="key-takeaways"></a>
## Key Takeaways

1. **The trigger is an event, not a person.** A `workflow_run` failure event
   creates the session, and the fix is verified by the same pipeline that
   caught the failure. Before: a red build waits for whoever is on rotation.
   After: in most cases the rotation engineer's first look is at a green
   re-run and a diff.

2. **Strict apply boundary.** Devin writes Terraform and Helm changes and
   produces the plan; humans approve and apply through the existing deploy
   path. This maps to how change failure rate is actually managed — review
   the plan, not the incident.

3. **Fan-out is the platform team's shape of work.** Base-image pinning and
   action-SHA pinning across 12 services, 4 workflows, and 2 repos ran as
   parallel child sessions with one aggregated summary — backlog items that
   age for months close in an afternoon, which is what shortens lead time for
   platform-wide changes.

4. **Devin Review turns standards into a gate.** The platform team encodes
   the standard once (knowledge note + reference templates); application
   teams get flagged at PR time; the platform team stops being a ticket queue
   for "please review my chart."

5. **Both sides of the loop are agents.** Devin reviews humans' infra PRs,
   and Devin Review's comments on Devin's own PRs are addressed by the
   authoring session — the human's role shifts to approving resolved threads.

6. **Everything referenced is real.** The workflows, Terraform roots, Helm
   charts, mutable tags, and chart gaps named in this demo exist on the
   repos' `main` branches — the demo runs against the actual estate, not a
   mock.
