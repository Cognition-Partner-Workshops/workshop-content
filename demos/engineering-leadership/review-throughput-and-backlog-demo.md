# Review Throughput and Backlog Burn-Down — Engineering Leadership Demo

A single-thread demo for engineering managers, delivery leads, and heads of
engineering. The subject is not one developer going faster — it is the two
queues a delivery org actually runs on: **the review queue** and **the small-work
backlog**. Devin Review sits on the pull requests of four repositories as a
standing reviewer, a batch of well-specified backlog issues is worked in
parallel by child sessions and lands as separate PRs, and a scheduled sweep
plus a ticket transition open work without anyone typing a prompt.

The thread starts by writing the team's review standard down as a committed
artifact, because that artifact is what makes agent output consistent across
repositories and across sessions.

<a id="toc"></a>
## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Before and After](#before-after)
- [Part 1 — Write the Review Standard Down](#part-1)
- [Part 2 — Devin Review on a Human PR](#part-2)
- [Part 3 — The Review Loop Closes on an Agent PR](#part-3)
- [Part 4 — Backlog Burn-Down in Parallel](#part-4)
- [Part 5 — Hands-Free: A Ticket Transition and a Scheduled Sweep](#part-5)
- [Part 6 — Cost and Capacity](#part-6)
- [What Still Needs a Human](#human-in-the-loop)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

The first artifact is the one the rest of the demo depends on. Paste this into
Devin to create the review standard across the four repositories:

```
Create a REVIEW.md at the repository root of each of these
four Cognition-Partner-Workshops repos, one PR per repo:

- otterworks
- timesheet-app
- eventflow-order-service
- petclinic-angular

REVIEW.md is the repo-scoped review standard a reviewer
applies to incoming pull requests. Derive each one from the
repo's actual contents — do not write a generic checklist.
Read the repo first (for otterworks, read AGENTS.md,
docs/CI_STRATEGY.md, and the Makefile lint/test targets;
for petclinic-angular read AGENTS.md).

Each REVIEW.md uses these sections:
1. Repository Context — stack, entry points, what the repo
   does, and which directories carry the most risk.
2. Priority Review Areas — ranked, with a severity label
   (High/Medium/Low) on each, specific to this codebase.
3. Verification Expectations — the exact lint and test
   commands a PR is expected to pass, quoted from the repo.
4. Common Pitfalls — patterns actually present in this repo.
5. Out of Scope — what a reviewer should not flag.

For otterworks, the Out of Scope section must state that the
deliberately planted bugs described in AGENTS.md under
"Golden app policy" are not defects and must not be flagged
or fixed.

Output: four PRs, each adding exactly one file, REVIEW.md,
at the repo root. In each PR body, list the specific repo
evidence (file paths) each Priority Review Area came from.
```

Prerequisites: Devin Review enabled for the four repositories, and a Devin
account with permission to launch child sessions.

---

<a id="repositories"></a>
## Repositories

Four repositories, four stacks — enough surface that no single engineer holds
the whole context.

| Repository | Stack | Why it is in the demo |
|---|---|---|
| [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) | 12 service directories under `services/` (Go, Rust, Python ×2, Java ×3, Kotlin, Scala, Ruby, C#, Node.js) plus `frontend/admin-dashboard` (Angular) and `frontend/client-app` (React) | The anchor. Polyglot, 29,030 service source lines, four workflows in `.github/workflows/`, and an `AGENTS.md` that already encodes repo policy |
| [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) | Node.js/Express backend + React frontend | Second stack; has `.github/workflows/pr-checks.yml` and `sast-scan.yml` |
| [eventflow-order-service](https://github.com/Cognition-Partner-Workshops/eventflow-order-service) | Python / FastAPI, publishes to Azure Service Bus | Small service with `app/`, `tests/`, and `.github/workflows/ci.yml` |
| [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular) | Angular / TypeScript, `src/` + `e2e/` | Frontend-only repo, already carries an `AGENTS.md` |

None of the four has a `REVIEW.md` on `main` today. Part 1 creates it.

---

<a id="before-after"></a>
## Before and After

The unit of measurement for this demo is not lines of code. It is queue time
and where senior engineers spend their attention.

| | Before | After |
|---|---|---|
| **First review response** | A PR waits for a human reviewer to have a free block — typically hours, and longer across a weekend | Devin Review responds on the PR shortly after it opens, with findings ranked by severity |
| **Who reviews the mechanical layer** | The most senior available engineer, reading for null-handling, error paths, missing tests, and convention drift | Devin Review, applying the committed `REVIEW.md` for that repo |
| **What humans review** | Everything in the diff, mixed together | Design, blast radius, product intent — with the mechanical layer already triaged |
| **Small, well-specified backlog issues** | Queued behind feature work, worked one at a time, often stale | Fanned out to child sessions, landing as separate PRs that enter the same review gate |
| **Consistency across repos** | Each reviewer's personal standard; varies by reviewer and by repo | One committed standard per repo, applied the same way on each PR |
| **Trigger** | A human notices the PR or the ticket | Ticket transition and a scheduled sweep open work with nobody watching |

The numbers a team should track after running this pattern: median time to
first review comment, PRs merged per week, and the share of review comments
that concern design rather than mechanics.

---

<a id="part-1"></a>
## Part 1 — Write the Review Standard Down

Run the [Quick Start](#quick-start) prompt. It produces four PRs, each adding a
single `REVIEW.md`.

This is the management artifact. A review standard that lives in a senior
engineer's head applies when that person is reviewing; a review standard
committed to the repository applies on each PR in that repo, including PRs the
team's agents open. It is also a multi-repo change: four repositories, four
different stacks, four different sets of risk areas — work that does not fit in
one engineer's editor session because no single editor has the four repos open
with their CI configuration understood.

### What to check in the output

Open the otterworks PR and read the `REVIEW.md` diff. The Priority Review Areas
should be recognizably about *this* repo — per-service test commands from the
`Makefile`, the path-based CI behavior described in `docs/CI_STRATEGY.md`, the
polyglot manifest layout — not a generic list that would fit any codebase. The
Out of Scope section should name the golden-app policy from `AGENTS.md`, so
that the planted bugs used by other labs are not reported as findings.

### Promote the standard into the shared context layer

`REVIEW.md` is repo-scoped. Two things belong above the repo, so that sessions
in any of the four repos inherit them:

```
Create a Devin Knowledge note titled "Review standard for
the four demo repos" that captures the cross-repo rules
common to the REVIEW.md files just added to otterworks,
timesheet-app, eventflow-order-service, and
petclinic-angular:

- severity vocabulary (High/Medium/Low) and what each means
- the expectation that a finding cites a file and line and
  proposes a concrete change, not a general concern
- the rule that a PR touching more than one service in
  otterworks must state why in the PR body
- the escalation rule: architectural or cross-service
  concerns are raised for a human owner rather than fixed
  in the same PR

Then write a playbook source to
otterworks/.workshop/playbooks/pr-review-standard.devin.md
that a session can follow to review a pull request against
that standard: read the repo's REVIEW.md, read the diff,
run the repo's lint and test commands for the touched
services, then post findings grouped by severity with a
short summary at the top.

Match the structure of the existing playbook source at
.workshop/playbooks/synthetic-testdata-generation.devin.md.
```

The repository already carries `.workshop/playbooks/synthetic-testdata-generation.devin.md`
and `.agents/skills/synthetic-testdata-generation/SKILL.md`, so the new playbook
lands next to an existing example of the same shape.

Three layers now exist, and they compound: the Knowledge note is the org-wide
standard, `REVIEW.md` is the repo-scoped standard, and the playbook is the
procedure. DeepWiki supplies the architectural context on top. When a reviewer
leaves feedback that turns out to be a durable rule, it goes into the Knowledge
note and applies to later sessions — coverage depends on how well the repo's
conventions are actually documented, but the standard improves rather than
resetting with each session.

---

<a id="part-2"></a>
## Part 2 — Devin Review on a Human PR

Now open a human-authored PR and watch the standard get applied.

```bash
cd otterworks
git checkout -b workshop-ratelimit-window main
```

Make a small, plausible change a manager would recognize: adjusting rate-limit
behavior in the Go API gateway. Edit
`services/api-gateway/internal/middleware/ratelimit.go` to change how the limit
window is computed, and commit without touching
`services/api-gateway/internal/middleware/ratelimit_test.go`.

```bash
git add services/api-gateway/internal/middleware/ratelimit.go
git commit -m "gateway: adjust rate limit window handling"
git push origin workshop-ratelimit-window
```

Open the PR against `main`.

### What arrives on the PR

Devin Review runs on the new PR and posts findings as PR comments, ranked by
severity, along with a summary of the diff. On a change like this it typically
reports:

- the behavior change in the limiter window, described in plain terms, so a
  reviewer who has never opened this file can follow it
- that `ratelimit_test.go` covers the previous window semantics and was not
  updated — a test-coverage gap on the changed path
- whether the change interacts with `internal/middleware/metrics.go` or the
  circuit breaker in `internal/proxy/circuitbreaker.go`

The mechanical layer of the review is done before a human opens the PR. What is
left for the engineering manager or the service owner is the question that
actually needs judgment: is this the right limit policy for the gateway.

### Ask for the aggregate, not the PR

The individual review is the visible part. The part a delivery lead buys is the
aggregate:

```
For the Cognition-Partner-Workshops repos otterworks,
timesheet-app, eventflow-order-service, and
petclinic-angular, produce a review-load report for the
last 30 days as a Markdown file, REVIEW_LOAD.md, in
otterworks under docs/.

For each repo include:
- PRs opened, PRs merged, PRs still open
- median and 90th-percentile time from PR opened to first
  review comment
- median time from PR opened to merge
- the three files or directories that appear most often in
  changed-file lists

Then a cross-repo section: which repo has the longest
time to first response, and which directories concentrate
the review load. Use the GitHub API for the data and state
the query window and the exact counts you used.
```

This is the report a delivery lead usually cannot get without asking someone to
spend an afternoon on it. It runs while nobody watches, across four repos at
once, and produces a committed artifact rather than a screenshot.

---

<a id="part-3"></a>
## Part 3 — The Review Loop Closes on an Agent PR

Reviewing human PRs is half the loop. The other half is what happens to PRs the
agent itself opens — this is the part that decides whether delegated work is
actually mergeable.

Start a session that does real work in the repo:

```
In Cognition-Partner-Workshops/otterworks, the
document-service has comment endpoints in
services/document-service/app/api/comments.py but the
listing path does not enforce a bound on the number of
comments returned.

Add pagination to the comment listing endpoint: limit and
offset query parameters with a sane default and a maximum
page size, the schema change in
services/document-service/app/schemas/, and the service
layer change in
services/document-service/app/services/document_service.py.

Add tests to
services/document-service/tests/test_comments_api.py
covering the default page size, an explicit page, an
out-of-range offset, and the maximum-page-size clamp.

Run the service tests from services/document-service with
pytest and get them green before pushing. In the PR body,
state the default and maximum page size you chose and why.
```

When the PR appears, Devin Review runs on it the same way it ran on the human
PR in Part 2 — the reviewer does not care who the author was. Findings show up
as PR comments against the same `REVIEW.md` standard.

### Close the loop

Take one finding from the review and hand it back to the session as a PR
comment, exactly as you would to a person:

```
The review flagged that the maximum page size is hard-coded
in the endpoint rather than read from
services/document-service/app/config.py, where the rest of
the service's tunables live. Move it to config with the same
default, keep the clamp behavior identical, and extend the
maximum-page-size test to assert the value comes from
config. Re-run pytest for services/document-service and
push to the same branch.
```

The session picks up the comment, makes the change, re-runs the tests, and
pushes to the same branch. The review re-runs on the new commit.

That is the whole point of this part for a delivery lead: agent-authored work
is not a separate track with a separate quality bar. It enters the same review
gate, gets the same standard applied, and is iterated through the same comment
mechanism the team already uses. Nothing new has to be added to the process for
the team to absorb it.

---

<a id="part-4"></a>
## Part 4 — Backlog Burn-Down in Parallel

Now the second queue. The otterworks repository has an issue backlog, and a
meaningful share of it is small, well-specified, and independent — the work that
never gets scheduled because it is individually too small to justify a sprint
slot and collectively too large to squeeze in.

First, separate what is delegable from what is not:

```
Read the open issues on
Cognition-Partner-Workshops/otterworks and produce
docs/BACKLOG_TRIAGE.md with three tables:

READY TO DELEGATE — issues that are scoped to a single
service directory, have an unambiguous acceptance
condition, and can be verified by a test or a lint/test
command that already exists in the repo. Give each one the
service directory, the verification command from the
Makefile or the service's own tooling, and a one-line
statement of done.

NEEDS SPECIFICATION — issues that are small but ambiguous.
For each, state the specific question that has to be
answered before the work can be delegated.

HUMAN JUDGMENT — issues that involve a cross-service
contract change, a data migration, production
configuration, or a product decision.

Do not open any code changes in this session. Exclude
anything touching the planted bugs described in AGENTS.md
under "Golden app policy".
```

The READY TO DELEGATE table is the unit of delegation. The NEEDS SPECIFICATION
table is more useful than it looks: it is a precise list of the decisions a
team lead has to make before capacity can be applied, which is usually the real
bottleneck rather than engineering time.

### Fan out

```
Act as the orchestrator for a backlog burn-down on
Cognition-Partner-Workshops/otterworks using child Devin
sessions.

Take the first six issues from the READY TO DELEGATE table
in docs/BACKLOG_TRIAGE.md and spawn one child session per
issue. Give each child:
- the issue number and its statement of done
- the single service directory it is allowed to change
- its own branch, backlog/issue-<number>
- the verification command for that service from the
  Makefile, which it has to get green before pushing
- the instruction to follow
  .workshop/playbooks/pr-review-standard.devin.md when
  self-checking its own diff

Each child produces its own separate PR — do not combine
them onto one branch.

Monitor the children until each has a green verification
run. Then write docs/BURNDOWN_SUMMARY.md with a row per
issue: issue number, service, branch, PR link, verification
command and result, and any finding the child escalated
instead of fixing.
```

Six branches, six PRs, six independent verification runs, one parent session
holding the ledger. Devin Review then runs on each of the six, so the same
standard from Part 1 is applied six times in parallel rather than six times in
series by whoever is on review duty.

This is the shape of the argument for a cloud agent over an editor assistant.
Six concurrent VMs, each with its own checkout, its own dependency install, and
its own test run, is not something an IDE-resident assistant does — not because
the model is different, but because the execution environment is.

---

<a id="part-5"></a>
## Part 5 — Hands-Free: A Ticket Transition and a Scheduled Sweep

Parts 1 through 4 each started with a human pasting a prompt. This part removes
that step.

### The scheduled sweep

Review latency is a queue problem, and queues need a sweeper. Create the
workflow:

```
Create a GitHub Actions workflow at
.github/workflows/review-sweep.yml in
Cognition-Partner-Workshops/otterworks.

Triggers: a weekday schedule at 13:00 UTC, and
workflow_dispatch with an optional stale_hours input
defaulting to 24.

The job:
(1) queries the GitHub API for open PRs on this repository
    that have had no review comment for more than
    stale_hours, excluding drafts and excluding PRs
    authored by devin-ai-integration[bot],
(2) for each stale PR, calls the Devin v3 API to create a
    session whose prompt names the PR number, the changed
    files, and instructs it to review the PR against the
    repo's REVIEW.md and post findings grouped by severity,
(3) caps the number of sessions created per run at 5 and
    logs which PRs were skipped by the cap,
(4) posts a single summary comment on each swept PR linking
    the session, and
(5) if a PR has been swept twice with no human response,
    stops creating sessions for it and adds the label
    needs-human-review.

Read .github/workflows/sast-auto-remediate.yml first and
follow its conventions for the Devin API call, secret
names, and bot-loop prevention. Document the workflow in
docs/REVIEW_SWEEP.md, including the trigger payload fields
and the escalation rule.
```

The repo already runs `sast-auto-remediate.yml` on `pull_request` events with
`MAX_FIX_ATTEMPTS=2` and a bot-author skip, so the new workflow inherits a
proven pattern for loop prevention and escalation rather than inventing one.

### The ticket transition

The other trigger is upstream of the repository entirely. With the Jira
integration connected, configure a Devin Automation on an issue transition:

- **Trigger:** an issue in the delivery project moves to the `Ready for Dev`
  status and carries the `agent-ready` label.
- **Payload:** issue key, summary, description, acceptance criteria field,
  component, and the repository named in the component mapping.
- **What happens hands-free:** a session opens, reads the issue body and
  acceptance criteria, maps the component to one of the four repositories,
  makes the change on a branch named after the issue key, runs that repo's
  verification command, and pushes. Devin Review runs on the resulting PR. The
  session comments the PR link back onto the ticket.

For a delivery lead, this is the interesting one, because it moves the trigger
from "an engineer had capacity" to "the work was specified well enough to
start." The `agent-ready` label is a deliberate gate: work enters the automated
path when a human has judged the specification sufficient, which is the same
judgment call as assigning a ticket to a junior engineer.

The escalation rule matters as much as the trigger. If the session cannot reach
a green verification run, it stops and moves the ticket back with a comment
naming what was ambiguous — the queue does not silently fill with
half-finished branches.

---

<a id="part-6"></a>
## Part 6 — Cost and Capacity

The question that follows a demo like this is what it costs and where the line
sits. Sessions consume ACUs while actively executing; hibernation and time
spent waiting on human review do not consume compute. Child sessions raise
throughput and raise consumption roughly in proportion — six children doing six
issues is close to six times the ACUs of doing one, delivered in a fraction of
the wall-clock time. ACU budgets can be set at the organization, team, or user
level, which is the control a delivery lead actually operates.

The shape of the trade-off, using this demo's own steps. **These figures are
illustrative and will vary with repo size, test-suite duration, and how much
iteration a task needs — treat them as a way to reason about the trade-off, not
as a quote:**

| Work in this demo | Rough shape of consumption | What it displaces |
|---|---|---|
| Part 1 — four `REVIEW.md` files | One session reading four repos; modest | A half-day of a staff engineer writing standards that then go stale |
| Part 2 — review on one PR | Small and bounded per PR | The wait, not the work — the review would have happened anyway, later |
| Part 3 — feature + review loop | One session, dominated by test runs and one iteration | A day of an engineer's time, plus the reviewer's |
| Part 4 — six issues in parallel | Roughly 6× a single issue, concurrently | Six issues that were not going to be scheduled |
| Part 5 — scheduled sweep | Per swept PR, capped at 5 per run | Nothing — this work was not being done |

The cap in the sweep workflow is a cost control as much as a loop guard: a
runaway sweep across a busy repo is the realistic way to overspend.

### What to delegate, and what stays human

| Delegate | Keep human |
|---|---|
| Small issues with an unambiguous statement of done and an existing verification command | Anything where the acceptance condition is a product decision |
| The mechanical layer of review — coverage gaps, error paths, convention drift | Design review, blast radius, service-boundary changes |
| Cross-repo consistency work: standards files, dependency alignment, docs drift | Cross-service contract changes and data migrations |
| Reporting that requires reading many repos (the Part 2 review-load report) | Deciding what the report means for the roadmap |
| Recurring sweeps: stale PRs, unaddressed review comments, flaky-test triage | Prioritization — what the team works on next |

The pattern that holds: delegate work whose *done* condition can be stated in
advance and checked by a command. Keep work whose done condition is a judgment.

---

<a id="human-in-the-loop"></a>
## What Still Needs a Human

Naming the limits honestly is part of the pitch to this audience.

- **Merge approval.** Devin Review posts findings and can be wired to block
  merge through a required status check, but the decision to merge stays with a
  human owner. This demo does not merge anything automatically.
- **Production access and deploys.** Nothing in this thread touches a
  production environment. Deployment stays behind whatever gate the team
  already has.
- **Specification.** The NEEDS SPECIFICATION table in Part 4 exists because a
  meaningful share of any backlog is not delegable until someone answers a
  question. Agents surface the questions; they do not decide the answers.
- **Prioritization.** Which six issues go into the fan-out is a management
  decision, and it stays one.
- **Review quality varies with context.** Findings are as good as the repo's
  documented conventions and structure. A repo with a thin `REVIEW.md` and no
  `AGENTS.md` typically gets a shallower review than otterworks does. Coverage
  depends on repo structure.
- **Cross-service and architectural concerns.** The Part 1 standard explicitly
  escalates these to a named human owner rather than fixing them inline. That
  rule exists because the blast radius of a contract change is a judgment call
  about the system and the roadmap.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **Review latency is a queue problem, and this fixes the queue, not the
  reviewer.** Devin Review responds on new PRs against a committed standard, so
  the first substantive feedback arrives without waiting for a human to have a
  free block. Senior review time moves off mechanics and onto design.
- **The review standard is a management artifact.** `REVIEW.md` per repo, a
  Knowledge note for the cross-repo rules, and a playbook for the procedure —
  together they are why output is consistent across four repos and across
  sessions instead of varying by whoever happened to be reviewing.
- **Agent-authored work goes through the same gate.** Part 3 shows the loop
  closing on the agent's own PR: review comment in, fix and green tests out, on
  the same branch. Delegated work does not need a parallel process.
- **Parallelism converts an unschedulable backlog into merged PRs.** Six child
  sessions, six branches, six verification runs, one ledger — the work that was
  individually too small to schedule gets done concurrently rather than never.
- **The triggers are not human.** A weekday sweep finds stale PRs; a ticket
  moving to `Ready for Dev` with an `agent-ready` label opens work. Both run
  with nobody watching, both cap themselves, and both escalate to a named human
  when they stall.
- **Capacity is now a budget line you control.** ACU consumption scales with
  concurrency and can be capped at the organization, team, or user level, and
  the sweep's per-run cap is a cost control as well as a loop guard.
- **The honest boundary is stable.** Merge approval, production access,
  specification, and prioritization stay human. What changes is that senior
  engineers spend their time on those four things instead of on the mechanical
  layer of the review queue.
