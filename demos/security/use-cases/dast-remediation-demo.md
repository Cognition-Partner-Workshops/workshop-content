# DAST Remediation — Attack the Running App, Prove the Fix

A single-thread demo showing Devin closing the dynamic application security
testing loop on the OtterWorks polyglot monorepo: attack the deployed
application, reproduce a real exploit, fix the control in the service that owns
it, redeploy, and re-run the same attack until it fails. Static analysis reads
the code; DAST proves what an attacker can actually do — and gives you a
repeatable check that the fix works.

<a id="toc"></a>
## Table of Contents

- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Part 1 — Orient: What Is Actually Exposed](#part-1)
- [Part 2 — Attack It: The First Scan](#part-2)
- [Part 3 — The Finding the Scanner Would Have Missed](#part-3)
- [Part 4 — Fix It and Prove It](#part-4)
- [Part 5 — Fan Out Across the Remaining Findings](#part-5)
- [Part 6 — Where the Confidence Comes From](#part-6)
- [Part 7 — Run It Unattended: CI, Schedules, Automations](#part-7)
- [Part 8 — Confirm It in the Dashboards](#part-8)
- [Key Takeaways](#key-takeaways)

---

<a id="prerequisites"></a>
## Prerequisites

1. **A scan target you own.** Either the local stack (`make up`) or your own
   tenant: `./scripts/deploy-tenant.sh <your-id>` →
   `https://api-t-<your-id>.demo.otterworks.app`. Never scan a tenant someone
   else is presenting from — every scan registers accounts and writes documents.
2. **The `!dast-remediation` playbook** is registered in your Devin organization.
   Its source is `.workshop/playbooks/dast-remediation.devin.md` on otterworks.
3. **The DAST suite is on `main`:** `security/dast/` and the `dast-*` targets in
   the `Makefile`.

---

<a id="quick-start"></a>
## Quick Start

Paste this into Devin to run the whole loop against your tenant:

```
!dast-remediation

Finding: whatever the DAST suite reports first at
critical or high severity.
Target: https://api-t-<your-id>.demo.otterworks.app
Repo: Cognition-Partner-Workshops/otterworks

Reproduce it, fix it in the service that owns the
control, redeploy the tenant, and re-run the probe
until it reports secure. Do not commit the fix to main.
```

---

<a id="repositories"></a>
## Repositories

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) —
  polyglot monorepo: 11 backend services across Go, Rust, Python, Java, Kotlin,
  Scala, Ruby, C#, and Node.js, plus two TypeScript frontends, all behind a Go
  API gateway. The DAST suite lives in `security/dast/`; the repo-specific
  mechanics are in `.agents/skills/dast-remediation/SKILL.md`, which Devin loads
  automatically when it works in this repo.

---

<a id="part-1"></a>
## Part 1 — Orient: What Is Actually Exposed

Start with a question no static scanner answers: what does this system expose to
the internet right now, and who is allowed to call it?

```
Read the OtterWorks repo and map its runtime attack
surface as an attacker would see it.

Which routes are reachable through the API gateway
without a token? Where does the gateway derive caller
identity, and what does it forward to the backends?
Which services enforce object ownership themselves
rather than trusting the gateway?

Answer with file references.
```

Devin reads the gateway middleware chain, the public-path list, and the
per-service handlers. Watch for the three things that shape everything after:

- `services/api-gateway/internal/middleware/jwt.go` — `DefaultPublicPaths()` and
  `DefaultPrefixPaths()` are the actual unauthenticated surface. `/health`,
  `/metrics`, and `/socket.io` are prefix-matched and open.
- `services/api-gateway/internal/proxy/router.go` — the gateway sets `X-User-ID`
  from validated JWT claims before forwarding. Every backend downstream trusts
  that header.
- `services/document-service/app/api/documents.py` — the document service reads
  ownership from the request, which means the gateway alone cannot enforce it.

That map is the difference between scanning a URL and testing a system. It is
also what a new engineer would spend a day assembling; Devin does it from the
code, with citations, in minutes.

---

<a id="part-2"></a>
## Part 2 — Attack It: The First Scan

```
Run the DAST suite against my tenant:

make dast-scan DAST_TARGET=https://api-t-<your-id>.demo.otterworks.app

Then walk me through security/dast/reports/dast-report.md:
which attacks succeeded, which failed, and which could
not be assessed. Do not fix anything yet.
```

The suite registers two throwaway identities — an attacker and a victim,
namespaced by a per-run id so concurrent scans never collide — and then runs 15
abuse cases through the gateway. The output is a verdict table and a
machine-readable report:

```
FAIL  DAST-MASS-ASSIGNMENT-OWNER     critical  attacker created a document owned by the victim
FAIL  DAST-CREDENTIAL-BRUTE-FORCE    high      12 consecutive failed logins accepted without throttling
FAIL  DAST-EXPOSED-TELEMETRY         medium    unauthenticated telemetry at /metrics
FAIL  DAST-MISSING-SECURITY-HEADERS  medium    missing content-security-policy, referrer-policy,
                                               x-content-type-options, x-frame-options
PASS  DAST-UNSIGNED-JWT              critical  all forged tokens were rejected
PASS  DAST-SQLI-ERROR-BASED          critical  no SQL errors surfaced from injected parameters
SKIP  DAST-BOLA-DOCUMENTS            critical  the owner is also refused; the read path rejects
                                               every caller, so cross-tenant access cannot be assessed
SKIP  DAST-IDENTITY-HEADER-SPOOF     critical  the owner is also refused, so the spoofed header
                                               cannot be assessed either
...
DAST gate FAILED: 4 finding(s) at or above medium
```

Three verdicts, not two. `vulnerable` means the attack ran and worked, with the
request and response captured as evidence. `secure` means the attack failed
**and** a control request confirmed the legitimate caller still succeeds.
`inconclusive` means no verdict was possible — and it never counts as a pass.
The exit code is what CI gates on: `1` for findings, and `3` when a focused
verification could not reach a verdict at all, so an unproven remediation can
never exit clean.

---

<a id="part-3"></a>
## Part 3 — The Finding the Scanner Would Have Missed

Look at `DAST-BOLA-DOCUMENTS` in that table. The attacker asked for the victim's
document and got `401`. A passive scanner records that as authorization working
correctly.

It is not. The suite made a second, boring request — *can the owner read their
own document?* — and the owner got `401` too. A route that refuses everyone is
not a route that is protecting anything, so the probe reported `inconclusive`
and sent us to look at the write path instead.

```
DAST-BOLA-DOCUMENTS came back inconclusive because the
owner is refused as well as the attacker. That means
the read-side check is not what is protecting these
documents.

Look at how a document gets its owner_id on create.
Can the attacker set it?
```

Devin finds that the create endpoint accepts `owner_id` from the request body.
The probe `DAST-MASS-ASSIGNMENT-OWNER` reproduces the consequence, using nothing
but the attacker's own valid token:

```http
POST /api/v1/documents/   Authorization: Bearer <attacker token>
{"title": "planted-by-attacker", "content": "...", "owner_id": "<victim id>"}

-> 201 {"id": "ae2524f6-…", "owner_id": "<victim id>", …}
```

The API returned `201` and echoed the **victim** as the owner. An attacker can
plant arbitrary content into any account, and every ownership check downstream
is enforcing a field the caller controls.

This is what DAST buys you over reading the code. Two things made it catchable:
the probe holds **two real identities**, so it can express "as A, act on B" —
which an unauthenticated crawler cannot — and the control request refused to let
a blanket `401` be recorded as a pass.

---

<a id="part-4"></a>
## Part 4 — Fix It and Prove It

```
!dast-remediation

Finding: DAST-MASS-ASSIGNMENT-OWNER
Target: https://api-t-<your-id>.demo.otterworks.app
Repo: Cognition-Partner-Workshops/otterworks

Fix it in the service that owns the control, redeploy
my tenant, and re-run the probe until it reports
secure. Keep main untouched — the fix goes on a branch.
```

The playbook drives the sequence; the repo Skill supplies the commands. Devin:

1. **Reproduces first** — confirms the finding fires before changing anything,
   and captures the request/response pair as the before-state.
2. **Places the fix by class** — this is object-property authorization, so it
   belongs in the document service's request schema, not the gateway. `owner_id`
   comes out of the create/update model and is set from the authenticated
   caller. Fixing it at the edge would have passed the probe and left the
   vulnerability reachable by another route.
3. **Redeploys the tenant** — the probe attacks a running system, so a green
   result against the old image means nothing.
4. **Re-runs the exact same attack**, with baseline suppression off so an
   accepted finding cannot mask its own check:

   ```
   make dast-verify FINDING=DAST-MASS-ASSIGNMENT-OWNER \
     DAST_TARGET=https://api-t-<your-id>.demo.otterworks.app
   ```

   ```
   PASS  DAST-MASS-ASSIGNMENT-OWNER  critical  owner_id was overridden to the caller, not the victim
   DAST gate PASSED: no unaccepted findings at or above info.
   ```

5. **Re-runs the full suite and the app's tests** — `make dast-scan` to catch
   regressions the fix introduced elsewhere, `pytest` and `make test-api-flows`
   to confirm legitimate users can still create documents. Access-control fixes
   are exactly the kind that pass the security check by refusing everybody.

The PR carries all of it: the attack that worked, the same attack now refused,
the full-suite result, and the tests. Devin Review comments on the diff, and the
review conversation happens on the PR like any other change — the security fix
goes through the same collaboration model as a feature.

---

<a id="part-5"></a>
## Part 5 — Fan Out Across the Remaining Findings

Three findings are left, in three different services, with nothing to do with
each other. One session working through them serially is the slow way.

```
Three DAST findings remain on my tenant:
DAST-CREDENTIAL-BRUTE-FORCE (auth-service, Java),
DAST-EXPOSED-TELEMETRY (api-gateway, Go), and
DAST-MISSING-SECURITY-HEADERS (api-gateway, Go).

Launch one child session per finding. Give each child
the !dast-remediation playbook, its own branch off main,
and its own tenant to scan so the scans do not collide.
Each child re-runs make dast-verify for its own finding
and opens its own PR.

Monitor them and report back with a table of finding,
session, PR, and verify result.
```

Each child gets its own VM, its own scoped credentials, and its own tenant
namespace. That isolation is what makes this safe: three sessions are
simultaneously registering attacker accounts, brute-forcing logins, and
redeploying services, and none of them can see or corrupt another's target. Per
run, per session, blast radius contained.

The two gateway findings show the other half of the placement rule. Missing
security headers and an exposed `/metrics` endpoint are **edge** controls — one
middleware registered in `services/api-gateway/cmd/server/main.go` fixes them for
all 11 backends at once, which is why they do not belong in any individual
service.

The parent tracks the children to green and reports the table. One agent
dividing and conquering a wave of work, each unit independently verified.

---

<a id="part-6"></a>
## Part 6 — Where the Confidence Comes From

Open `security/dast/harness/probes/access_control.py` and read the probe that
found the mass-assignment bug. It is about thirty lines: create a document as the
attacker naming the victim as owner, then assert on who the API says owns it.

That is the whole trick. The confidence in Part 4 does not come from the diff
looking sensible, or from a unit test the same author wrote, or from a reviewer's
judgment. It comes from an adversarial check that **failed before the change and
passes after it**, against the running system, on demand, by anyone.

The properties that make it hold up:

- **Stable finding IDs.** `DAST-MASS-ASSIGNMENT-OWNER` is the gate key, the
  baseline key, and the handle you re-verify with. It never gets renumbered.
- **Namespaced fixtures.** Every identity and document carries a per-run id, so
  CI, three children, and a presenter can scan at the same time.
- **A baseline, not a mute button.** `security/dast/baseline.json` holds
  knowingly-accepted findings so CI fails on *new* ones — and `dast-verify`
  ignores it entirely, so an accepted finding can never suppress its own
  remediation check.
- **Inconclusive is a first-class verdict.** A backend that is down does not make
  an attack fail.

---

<a id="part-7"></a>
## Part 7 — Run It Unattended: CI, Schedules, Automations

A scan you run by hand dates the moment you close the terminal. Three ways the
same suite keeps running:

**On every pull request.** `.github/workflows/dast-scan.yml` stands up the stack
with docker compose, waits for the gateway, runs `make dast-scan`, and publishes
the report to the job summary. It gates against `baseline.json`, so a PR fails
only on a finding it introduced.

**On a schedule.** The same workflow sweeps the deployed tenant nightly with the
probe suite plus the OWASP ZAP passive scan. Alongside it, a **scheduled Devin**
runs the same playbook on a cadence — the recurring maintenance sweep no one
volunteers for:

```
Every weekday at 07:00 UTC, deploy a disposable tenant from
main with scripts/deploy-tenant.sh, run
make dast-scan DAST_TARGET=<that tenant's api URL>
on Cognition-Partner-Workshops/otterworks, then tear the
tenant down. A scan registers accounts and writes documents,
so it never runs against a shared tenant.

If the gate fails on a finding that is not in
security/dast/baseline.json, post the report summary to
Slack with the finding IDs and the evidence.
```

**On an event.** A [Devin Automation](https://docs.devin.ai/product-guides/automations)
turns a red scan into a fix in progress rather than a ticket in a queue: when
the scheduled DAST job fails, start a session with `!dast-remediation` scoped to
the new finding, on a fresh tenant, opening a PR. This is the same pattern as the
[event-driven SAST remediation](./event-driven-sast-remediation-demo.md) flow —
the trigger is a runtime exploit instead of a static match, and because the
verification is an executable attack, the automation can prove its own fix
before a human ever looks at it.

---

<a id="part-8"></a>
## Part 8 — Confirm It in the Dashboards

Four places to confirm the loop actually closed:

| Where | What to open | What it proves |
|---|---|---|
| **GitHub Actions** → the PR's *Checks* tab | the `DAST Scan` job summary | the full report, rendered per run: verdicts, evidence, gating count. Green means no finding was introduced. |
| **The PR** → *Files changed* and *Conversation* | the diff plus Devin Review's comments | the control that changed, and the human/agent review loop around it. |
| **Actions** → *Artifacts* | `dast-report` (JSON + Markdown) | the machine-readable record — the JSON is what a dashboard or ticket tracker ingests. |
| **Devin** → the parent session | the child session table from Part 5 | each finding, its branch, its PR, and its `dast-verify` result in one view. |

The JSON report is the one to look at last. Each result carries the finding ID,
severity, OWASP and CWE classification, the owning service, the verdict, and the
captured request/response. That is a security finding with a reproduction
attached — which is what makes it possible to hand the whole loop to an agent in
the first place.

---

<a id="key-takeaways"></a>
## Key Takeaways

1. **DAST proves exploitability; SAST flags suspicion.** Static analysis says a
   line looks dangerous. A probe says an attacker created a document in someone
   else's account, and here is the request that did it.

2. **The re-run is the proof.** A finding is closed when the attack that
   reproduced it fails against a target running the new code — not when the diff
   looks right. Everything else in this demo is in service of that one sentence.

3. **A control request separates "secure" from "broken".** `401` for the attacker
   looks identical to `401` for everybody. The probe that checks whether the
   legitimate owner still succeeds is what turned a false pass into the critical
   finding in Part 3.

4. **Two identities beat a crawler.** Authenticated, stateful probes can express
   "as user A, act on user B's data" — the entire broken-object-authorization
   class that unauthenticated scanners structurally cannot reach.

5. **Fix at the layer that owns the control.** Edge concerns (headers, CORS, rate
   limiting, identity headers) go in the gateway once for all 11 services. Object
   ownership goes in the service that owns the data. Fixing at the wrong layer
   passes the probe and leaves the hole.

6. **Isolation makes parallelism safe.** Each child session gets its own VM,
   scoped credentials, and tenant namespace, so three agents can attack three
   copies of the app simultaneously without colliding — and a blown-up target is
   one namespace, not the environment.

7. **The playbook makes every run the same.** `!dast-remediation` is the
   procedure; the repo Skill supplies the commands and file map. The presenter,
   the child sessions, the scheduled sweep, and the CI-triggered automation all
   execute the identical loop.

8. **From scan to closed loop.** CI gates new findings on every PR, a schedule
   sweeps the deployed app, and an automation turns a red scan into a verified PR.
   The report is the artifact; the passing re-run is the evidence.
