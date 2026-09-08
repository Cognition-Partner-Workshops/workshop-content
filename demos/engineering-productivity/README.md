# Engineering Productivity Demos — Devin Across the SDLC

Four single-thread demos that show Devin working the way an engineering team
actually works: pulling a story off a board, closing a security finding,
unblocking a red pipeline, and reviewing a teammate's PR. Every demo runs against
the same brownfield monorepo — [OtterWorks](https://github.com/Cognition-Partner-Workshops/otterworks),
a polyglot document-management platform (Go, Java, Kotlin, Python, Rust, Ruby,
Node, React/Angular) deployed to a shared EKS cluster with real GitHub Actions
pipelines, Trivy/Semgrep/Gitleaks gates, and per-attendee runtime tenants.

The point of each demo is that Devin reasons over **more than the source tree**:
the ticket in the tracker, the Actions log, the deployed pod's behavior, the
scanner output, and the review thread on the PR. The prompts are one line each so
they paste cleanly into the Devin UI.

## Table of Contents

- [Quick Start](#quick-start)
- [The Four Demos](#the-four-demos)
- [Shared Setup](#shared-setup)
- [What the Demo Org Needs](#demo-org)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

1. In `Cognition-Partner-Workshops/otterworks`, create your branch from the
   long-lived **`workshop`** branch (not `main`): `git checkout -b workshop-<attendee_id> origin/workshop && git push -u origin workshop-<attendee_id>`.
   The push deploys your private tenant and starts CI on your branch.
2. Pick a demo below; each one takes 20–30 minutes end to end and can run in
   parallel with the others because they touch different services.
3. Paste the first prompt into a new Devin session. Every prompt is a single line.

---

<a id="the-four-demos"></a>
## The Four Demos

| # | Demo | Devin reads from | Devin writes to | Before-state on `workshop` |
|---|------|------------------|-----------------|----------------------------|
| 1 | [Story to PR via Otter Projects](01-story-to-pr-otter-projects.md) | Otter Projects ticket, source, deployed tenant API | PR, ticket comments and board column, tenant deploy | Ticket `OTTER-7` (mirrors issue [#1507](https://github.com/Cognition-Partner-Workshops/otterworks/issues/1507)); export returns 401 on the deployed tenant |
| 2 | [Vulnerability and Dependency Remediation](02-vulnerability-dependency-remediation.md) | Trivy/Semgrep results in Actions, dependency trees, advisory gate | PR with the version pin, behavior transcript, green security scan | `commons-text` 1.9 (CVE-2022-42889) in three JVM services |
| 3 | [CI/CD Failure Diagnosis and Fix](03-ci-failure-diagnosis-and-fix.md) | Failed Actions job log, race-detector report | Fix commit, rerun, root-cause writeup | `services/api-gateway/internal/proxy/router.go` upstream-error counter fails `go test -race` |
| 4 | [PR Review and Safe Auto-Fix](04-pr-review-and-auto-fix.md) | PR diff, Devin Review + Semgrep findings, sibling code | Review comments, then approved fixes with tests | `services/document-service/app/api/documents.py` owner-stats and duplicate endpoints |

Run them in order for a full "sprint in an hour", or run any one standalone.

---

<a id="shared-setup"></a>
## Shared Setup

**Repository.** [Cognition-Partner-Workshops/otterworks](https://github.com/Cognition-Partner-Workshops/otterworks).
`main` is the golden app. The long-lived **`workshop`** branch is `main` plus the
before-state for these demos (the merged fixture commits for demos 3 and 4);
CI is intentionally red on it. Attendees branch `workshop-<attendee_id>` from
`workshop` and open PRs back into `workshop`, never into `main`. Pushing
`workshop-<attendee_id>` deploys a private tenant automatically
(`.github/workflows/cd-tenant.yml`), reachable at `https://t-<attendee_id>.otterworks.app`
and `https://api-t-<attendee_id>.otterworks.app`. The perpetual shared tenant is
`t-main.otterworks.app`; nothing is ever injected or mutated there.

**Runtime.** Each tenant is a namespace on the shared `otterworks-dev` EKS
cluster with its own Postgres database, Redis, and MeiliSearch. Runtime failures
for a tenant can be injected without code changes from
`scripts/bug-catalog.yaml` via `scripts/inject-bug.sh <id> <scenario>` or the ops
dashboard (`demo-platform/docs/api-contract.md`).

**Tracker.** [Otter Projects](https://github.com/Cognition-Partner-Workshops/otterworks/tree/main/demo-platform/otter-projects)
is a minimal issue tracker (projects, board, tickets, labels, assignment) that
ships in the OtterWorks repo and is deployed at `https://projects.otterworks.app`.
Assigning a ticket to the built-in `devin` user, or adding the `devin` label,
posts the ticket to a Devin Automation webhook; Devin's progress, comments, and
PR link stream back onto the ticket and the board. It stands in for Jira so the
flow can be shown without a Jira license — the webhook contract is the same
shape a Jira Automation rule would send.

**Pipelines.** `.github/workflows/ci.yml` runs per-service jobs (Go `-race`,
Gradle, Poetry/pytest, npm). `security-scan.yml` runs Trivy (fail only on newly
introduced CVEs), Gitleaks, and Semgrep (`p/owasp-top-ten`, `p/security-audit`)
on every PR. Devin Review comments on every PR in the org.

---

<a id="demo-org"></a>
## What the Demo Org Needs

These demos are run by attendees in the **Demo** Devin org, which does not share
credentials with the org that authored this content. To run all four, the Demo
org needs:

| Need | Used by | How it is provided |
|------|---------|--------------------|
| GitHub App installed on `Cognition-Partner-Workshops/otterworks` with contents, pull-requests, issues, checks, and **actions: read** | all | Devin GitHub integration (org settings) |
| Ability to push `workshop-<id>` branches | 1, 2, 3, 4 | GitHub App write access |
| Devin Automation with a **Webhook** trigger, plus its URL and secret configured on Otter Projects as `DEVIN_WEBHOOK_URL` / `DEVIN_WEBHOOK_SECRET` | 1 | Demo org Automations page or `POST /v3/organizations/{org}/automations` |
| Org secret `PROJECTS_API_KEY` (Otter Projects API key) so a session can post progress to `https://projects.otterworks.app/api/webhooks/devin` | 1 | Demo org Secrets |
| Org secret `PROJECTS_BASE_URL` = `https://projects.otterworks.app` | 1 | Demo org Secrets |
| Read-only AWS credentials for account-scoped `eks:DescribeCluster` + Kubernetes RBAC `view` on `otterworks-<id>` namespaces (pod logs, events) | 1, 3 (optional runtime verification) | Demo org Secrets `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY`, `aws-auth` mapping |
| Ops dashboard passcode for tenant checkout / bug injection | 1 (optional) | Demo org Secret `OTTERWORKS_OPS_PASSCODE` |
| Devin API key (`cog_…`) and org ID to create sessions as a named user via `create_as_user_id` | facilitator scripting only | Demo org Settings → API |

No demo requires AWS *write* access from a Devin session: deployments happen
through GitHub Actions, and runtime injection goes through the ops dashboard.

---

<a id="key-takeaways"></a>
## Key Takeaways

- Devin is most useful when it is wired into the systems the team already uses:
  the tracker, the pipeline, the scanner, the review thread, the runtime.
- Each demo ends in a PR gated by a green run, not a code snippet — the team's
  existing quality bar is what decides when the work is done.
- The webhook shape used here (ticket assigned → prompt → session → status back)
  is tracker-agnostic; Otter Projects is only the stand-in.
- Runtime context (a deployed tenant, a pod log, a scanner report) turns "looks
  right" into "verified", and Devin can gather it without a human relaying it.
