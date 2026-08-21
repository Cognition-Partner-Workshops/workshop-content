# Visual UI Defect Remediation — See It in the Browser, Prove It with a Gate

A single-thread demo showing Devin closing the loop on defects that only exist
in front of a user: a page that renders "no notifications" while its own data
calls return `400`, a settings form that posts to a route that answers `404`, a
permanent delete with no confirmation. Unit tests and linters pass on all of
them. Devin drives the app in a browser, watches it fail, encodes the expectation
as a test that fails for the same reason, fixes the contract, and then proves the
fix by making the test pass *and* by removing the suppression that kept the suite
green while the defect was open — then fans the rest of the backlog out one
session per finding and puts the whole loop on a trigger.

<a id="toc"></a>
## Table of Contents

- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Part 1 — Orient: What Does the App Actually Do to a User](#part-1)
- [Part 2 — Reproduce It in a Browser](#part-2)
- [Part 3 — Encode the Expectation, and Require It to Fail](#part-3)
- [Part 4 — Fix the Contract and Tighten the Gate](#part-4)
- [Part 5 — Fan Out the Rest of the Backlog](#part-5)
- [Part 6 — The Long Pass: Sweep the Whole Estate](#part-6)
- [Part 7 — Where the Confidence Comes From](#part-7)
- [Part 8 — Run It Unattended: Schedules and Automations](#part-8)
- [Part 9 — Confirm It in the Dashboards](#part-9)
- [Key Takeaways](#key-takeaways)

---

<a id="prerequisites"></a>
## Prerequisites

1. **A target you own.** Either the local stack (`make up`, then `make dev-web`
   for the client app on `:3000`) or your own tenant:
   `./scripts/deploy-tenant.sh <your-id>` → `https://t-<your-id>.demo.otterworks.app`.
   Never use a tenant someone else is presenting from — this loop registers
   users, uploads files, and deletes things.
2. **The `!visual-ui-defect-remediation` playbook** is registered in your Devin
   organization. Its source is
   `.workshop/playbooks/visual-ui-defect-remediation.devin.md` on otterworks.
3. **The QA suite is on `main`:** `qa/registry.yaml`, `qa/harness/ui_gate.py`,
   `frontend/client-app/e2e/ui-console-gate.spec.ts`, and the `ui-*` targets in
   the `Makefile`.

---

<a id="quick-start"></a>
## Quick Start

Paste this into Devin to run the whole loop on the highest-severity open
finding. Everything after this is the same loop, one step at a time.

```
!visual-ui-defect-remediation

Finding: OW-UI-101 from qa/registry.yaml
Target: the local stack (make up, make dev-web)
Repo: Cognition-Partner-Workshops/otterworks

Reproduce it in a browser with screenshots, write the
reproduction spec and show it failing, fix the contract
in the layer that owns it, then flip the registry entry
to remediated, delete its suppression, and get
`make ui-gate` green. Work on your own branch; do not
commit to main.
```

---

<a id="repositories"></a>
## Repositories

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) —
  polyglot monorepo: 11 backend services across Go, Rust, Python, Java, Kotlin,
  Scala, Ruby, C#, and Node.js, plus two TypeScript frontends behind a Go API
  gateway. The defect registry and harness live in `qa/`, the browser suite in
  `frontend/client-app/e2e/`, and the repo-specific mechanics in
  `.agents/skills/visual-ui-defect-remediation/SKILL.md`, which Devin loads
  automatically when it works in this repo.

---

<a id="part-1"></a>
## Part 1 — Orient: What Does the App Actually Do to a User

Start with the question no test suite in the repo answers: what does a signed-in
user actually experience on every page?

```
Read the OtterWorks repo and orient me on the client
app's user-facing surface.

Which routes does an authenticated user reach, which
API calls does each of them make, and which service
owns each of those endpoints? Use qa/registry.yaml and
docs/exploratory-qa-report.md as the record of what a
browser pass already found, and tell me which findings
are still open and which have no reproduction spec yet.
```

Devin answers with the route map, the caller for each API call, and
`make ui-list`:

```
FINDING    SEV      STATUS      SPEC     TITLE
OW-UI-101  high     open        MISSING  The unread-count badge errors on every authenticated route
OW-UI-102  high     open        MISSING  Settings page calls a route that does not exist
OW-UI-103  medium   open        MISSING  Text file detail page never shows the file contents
OW-UI-104  medium   open        MISSING  Download action gives no visible feedback
OW-UI-105  medium   open        MISSING  Permanent delete from Trash is not confirmed
```

Five registered defects, zero reproductions. The registry is deliberately the
before-state: it records what a human saw, and the expected behavior each fix is
graded against — not the diagnosis, and not the fix.

**Key Takeaways**

- Devin orients over a polyglot estate by reading it, not by being told: the
  route map, the caller of each request, and the service that answers it.
- The registry separates *observed symptom* from *expected behavior*. A fix is
  graded against the expectation, which is what stops "the error went away" from
  counting as done.

---

<a id="part-2"></a>
## Part 2 — Reproduce It in a Browser

`OW-UI-101` is the interesting one: the notification data is broken on *every*
authenticated route, and the UI shows no sign of it.

```
Bring up the stack and reproduce OW-UI-101 in a browser
yourself.

Register a user, sign in, and walk /dashboard, /files
and /notifications with the network and console panels
open. Screenshot the notifications page in its failing
state and tell me the exact method, path, status and
response body of every notification call. Then name the
component or hook that makes each one, and what the
handler on the other side requires that it is not
getting.
```

Devin starts the stack, drives the app in its own browser, and comes back with
something better than the expected answer — **the recorded symptom is gone, and
the defect is not**:

- `GET /api/v1/notifications/unread-count` → `200`,
  `{"userId":"<uuid>","unreadCount":0}`. The registry's `400` no longer
  reproduces: `services/api-gateway/internal/proxy/router.go` now injects the
  caller identity the Ktor handler in
  `services/notification-service/.../routes/Routes.kt` requires.
- The badge is still broken, for a different reason. `src/lib/api.ts` reads
  `data.count` from a response whose contract is `{userId, unreadCount}`, so the
  query resolves `undefined` and the console carries
  `Query data cannot be undefined ... ["notifications","unread-count"]` on
  **every** authenticated route.
- The callers are `src/components/ui/notification-bell.tsx` (30-second poll on
  every route) and `src/pages/notifications.tsx`, which renders its **empty
  state** when its list call fails — so the UI reports "You're all caught up"
  whether the data is empty or broken.

That is a defect no unit test catches: the frontend's tests mock the client, the
service's tests assert its own shape, and nobody compares the two.

**Key Takeaways**

- Devin uses a real browser as an instrument: it reads the network panel and the
  rendered page, and screenshots the failing state as evidence for a human.
- A registry records what someone saw on the day they filed it. Reproducing
  first is what catches symptom drift — the status code had changed, the
  expectation was still violated, and the finding was still open.
- "Empty" versus "broken" is the whole defect. A UI that renders its empty state
  on a failed request makes a broken system look healthy on every screen.

---

<a id="part-3"></a>
## Part 3 — Encode the Expectation, and Require It to Fail

Seeing it is not enough. The next step is the one that makes the result durable.

```
Write the reproduction spec for OW-UI-101 at
frontend/client-app/e2e/ui-defects/ow-ui-101.spec.ts.

Assert the expected behavior from the registry, not the
symptom: the notification calls return 200 for a signed
in user, the badge reflects the real unread count, and
the notifications page distinguishes "no notifications"
from "could not load notifications".

Then run `make ui-repro FINDING=OW-UI-101` and show me
the failure. I want the spec red before anything is
fixed, and I want it to fail for the reason you observed
in the browser — not on a selector or a login problem.
```

`make ui-repro` inverts the usual contract: it **requires** the spec to fail.

```
$ make ui-repro FINDING=OW-UI-101
...
OW-UI-101: reproduced against http://localhost:3000.
report: qa/reports/repro-ow-ui-101.md (and .json)
```

If the spec had passed, the harness would have failed the step and said so: a
spec that has never been red may simply be asserting behavior the app already
had.

**Key Takeaways**

- The reproduction must fail before it passes. A test written after the fix
  proves nothing about the fix.
- The harness enforces that asymmetry itself — `repro` fails when the spec
  passes — so the discipline does not depend on remembering it.

---

<a id="part-4"></a>
## Part 4 — Fix the Contract and Tighten the Gate

```
Now fix OW-UI-101 at the layer that owns the behavior,
and make the notifications page tell the truth when a
load fails instead of rendering its empty state.

Then flip OW-UI-101 to remediated in qa/registry.yaml,
delete its accepted_console_errors, and run
`make ui-gate`. I want the route sweep enforcing those
calls from now on, and an after-screenshot of
/notifications from the same viewport as the before.
```

Two things happen here, and the second is the one worth watching.

The fix is one line in the layer that broke the contract —
`data.count` → `data.unreadCount` in `src/lib/api.ts` — plus a real `isError`
state on the notifications page instead of the empty state. Then the registry
edit removes the suppression that had been keeping the route sweep green:

```yaml
- id: OW-UI-101
  status: remediated            # was: open
  # accepted_console_errors: deleted with the fix
```

The harness will not let those two drift apart. Marking a finding `remediated`
while it still carries a suppression is a hard error, and `make ui-verify`
refuses to pass a finding whose registry entry still says `open`:

```
$ make ui-verify FINDING=OW-UI-101
OW-UI-101 is still `status: open` in qa/registry.yaml. Set it to
`remediated` and drop its accepted_console_errors so the console gate
starts enforcing it.
```

With both done, `make ui-gate` sweeps every authenticated route as a signed-in
user and fails on any console error or `4xx`/`5xx` that no open finding accounts
for:

```
$ make ui-gate
- Graded findings: OW-UI-101
- Suppressed (still open): OW-UI-102, OW-UI-103, OW-UI-104, OW-UI-105
- Result: **PASS**
```

Devin lands it as a PR with the before/after screenshots, the red-then-green
spec output, and that summary in the body. The reports and screenshots in
`qa/reports/` are generated output and are not committed.

**Key Takeaways**

- Fixing a defect **tightens** the gate: the suppression that made the suite
  usable during the backlog is deleted in the same change, so the route can
  never regress silently.
- The controls fail closed by construction — a closed status set, a rejected
  suppression-plus-remediated combination, and a spec that suppresses nothing
  when run standalone.
- Devin Review comments on the PR like any other reviewer, so the fix goes
  through the same collaboration model as a human's.

---

<a id="part-5"></a>
## Part 5 — Fan Out the Rest of the Backlog

Four findings remain and none of them depend on each other. That is one session
each, not one session for the batch.

```
Take the remaining open findings in qa/registry.yaml
(OW-UI-102 through OW-UI-105) and create one Devin
session per finding, each running
!visual-ui-defect-remediation for its own finding on
its own branch.

Give each child the finding id and nothing else it
does not need. Monitor them, and report a table of
finding, session, branch, spec result, gate result and
PR. Do not let two children touch the same finding or
the same registry entry.
```

The orchestrator spawns four children and watches them to green. Each child
gets its own VM, its own stack, its own throwaway users and its own branch —
which is why four browser sessions that all register accounts and delete files
can run at the same time without colliding. Isolation is what makes the
parallelism safe.

Watch for the honest outcome: a child that finds its finding is a product
decision rather than a bug (`OW-UI-102` — either back the settings surface with
a real endpoint or disable the controls visibly) reports a blocker with evidence
instead of inventing a backend. That is the correct result, and it is visible in
the table without opening four sessions.

**Key Takeaways**

- One session per unit of work: independent branches, independent verification,
  independent PRs — and one child failing does not strand the other three.
- Per-session isolation with scoped credentials is the feature that makes a
  parallel wave possible at all; blast radius stays inside a namespace.
- The orchestrator reports one table. Reviewers read a summary, not four
  transcripts.

---

<a id="part-6"></a>
## Part 6 — The Long Pass: Sweep the Whole Estate

The same fan-out pattern scales past a five-item registry to work nobody wants
to hand-schedule: the dependency and end-of-life backlog across eleven services
in nine languages.

```
!dep_sweep

Repo: Cognition-Partner-Workshops/otterworks

Inventory every service's dependency manifests across
Go, Rust, Python, Java, Kotlin, Scala, Ruby, C# and
Node.js. Report what is behind, what is end-of-life,
and what has a published advisory, grouped by service
and ranked by risk.

Then create one child session per service that has work
to do, each upgrading only its own service and proving
it with that service's own build and test suite. Report
a table of service, upgrades, suite result and PR, and
tell me which ones need a human decision.
```

This is a long-running pass: reading nine ecosystems' manifests, resolving
what is actually reachable, then a build-and-test cycle per service. It runs
unattended and reports one table at the end.

**Key Takeaways**

- The same orchestrator → child pattern applies to a nine-language estate; the
  unit of work changes, the shape does not.
- Long-duration work is where an autonomous agent separates from an assistant:
  nobody is watching the build logs for eleven services.

---

<a id="part-7"></a>
## Part 7 — Where the Confidence Comes From

Not from reading the diffs. From four properties of the loop:

1. **The reproduction was red first.** `make ui-repro` fails when the spec
   passes, so every graded spec has demonstrated it can detect the defect.
2. **The status set is closed.** `open` or `remediated`; anything else is a hard
   error, so a typo cannot quietly skip a grade.
3. **Suppressions are derived, not written.** They are generated from the
   registry at run time and only from findings still `open`. The spec run on its
   own suppresses nothing.
4. **The gate sweeps routes, not just the finding.** Any unregistered console or
   network error on any authenticated route fails the run, so a fix that breaks a
   neighboring page cannot pass.

Ask Devin to attack the gate rather than trusting the description:

```
Try to make `make ui-gate` pass while OW-UI-101 is
still broken, without editing the harness. Then try
marking it remediated while keeping its suppression,
and try a typo in its status. Show me what each attempt
does and paste the exit codes.
```

Every attempt exits non-zero. That is the demo of the controls.

**Key Takeaways**

- Audit your gates by attacking them. A control nobody has tried to defeat is a
  control nobody should trust.
- Reports are written on failure paths too — the evidence exists precisely when
  something went wrong.

---

<a id="part-8"></a>
## Part 8 — Run It Unattended: Schedules and Automations

The loop is worth more on a trigger than on a prompt. Describe the trigger in
plain language and let Devin build it.

```
Set up two Devin automations for this repo.

First: when a GitHub issue is opened in
Cognition-Partner-Workshops/otterworks with the label
`ui-defect`, start a Devin session that registers the
issue as a new finding in qa/registry.yaml, then runs
!visual-ui-defect-remediation for it end to end on its
own branch and links the resulting PR back as a comment
on the issue. If the report has no reproducible symptom,
comment asking for the route and the observed behavior
instead of guessing.

Second: every Monday at 07:00 UTC, start a session that
runs `make ui-gate` against the tenant tracking main,
sweeps every authenticated route for new console and
network errors, registers anything unaccounted-for as a
new open finding with its route and symptom, and posts
the summary with the route screenshots. Open a fan-out
of one child per new high-severity finding.

Show me each trigger's configuration before you enable
it, and use the same playbook the interactive runs used
so a scheduled run and a hand-driven run do the same
thing.
```

The event-driven one turns an issue into a verified PR. The scheduled one is
the operations and maintenance case: the sweep is worth running when nobody
asked, because the findings it produces are the ones nobody noticed.

**Key Takeaways**

- The trigger is described in natural language; the playbook is what makes a
  scheduled run and an interactive run behave identically.
- Scheduled sweeps generate the backlog that the fan-out then burns down — the
  two patterns compose into an unattended maintenance loop.

---

<a id="part-9"></a>
## Part 9 — Confirm It in the Dashboards

Close the loop where the work is visible:

- **The Devin sessions list** — the parent and its children under one user, each
  with its own PR. The fan-out is legible here as a tree, not a transcript.
- **The pull requests** — one per finding, each with its before/after
  screenshots, its red-then-green spec output, and the gate summary in the body.
  Devin Review's comments are on the same PRs.
- **CI on each PR** — the QA gate job's `qa/reports/` artifact: the per-route
  screenshots and the JSON/Markdown reports for the run that produced the PR.
- **`make ui-list` on the branch** — the registry after the wave: what is
  `remediated`, what is still `open`, and what the sweep discovered and
  registered along the way.
- **The automations view** — the issue trigger and the Monday schedule, with the
  sessions each has started.

**Key Takeaways**

- The evidence a reviewer needs is in the PR body and the CI artifact, not in
  the session transcript.
- The registry is the durable state: the same file that scoped the work reports
  the outcome.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **Devin uses the browser as an instrument.** It drives the app, reads the
  network and console panels, screenshots the failing state, and attributes the
  failure to a caller and a handler — the class of defect that unit tests and
  linters pass on.
- **The reproduction is red before it is green.** The harness requires it, so
  every graded spec has proven it can detect the defect it covers.
- **Fixing a defect tightens the gate.** The suppression that kept the suite
  usable during the backlog is deleted in the same change, so the route is
  enforced from then on.
- **The controls fail closed.** A closed status set, a rejected
  remediated-plus-suppression combination, derived suppressions, and a
  whole-surface sweep — verified by attacking them, not by reading them.
- **One session per unit of work.** Isolated VMs and scoped credentials make a
  parallel wave of browser sessions safe; each child lands its own verified PR
  and one failure strands nothing.
- **The same playbook runs unattended.** An issue label starts it; a weekly
  schedule finds the work nobody filed. Interactive and automated runs execute
  the identical procedure.
