# Security Scanning and Remediation on OtterWorks — Demo

A single linear demo for security and DevSecOps engineers, run against a real
polyglot monorepo. Devin runs the repo's own scanners, triages what comes back,
argues with the suppression list, remediates CRITICAL/HIGH dependency findings
across several ecosystems, re-scans to prove the finding is gone, and then hands
the same loop to CI so it runs without anyone opening a session.

**Platform-surface variants of this story with generic repos:**
[Security Remediation — General](general.md) |
[Desktop + Cloud](general.platform.md) |
[CLI](general.local.md). This file is the OtterWorks-specific concrete run.

## Table of Contents

- [Quick Start](#quick-start)
- [Repository](#repository)
- [Before, After, and the Verification Loop](#before-after)
- [Part 1 — Triage What the Scanners Actually Report](#part-1)
- [Part 2 — Audit the Suppression List](#part-2)
- [Part 3 — Remediate, Test, and Re-Scan](#part-3)
- [Part 4 — Hand the Loop to CI](#part-4)
- [Part 5 — Fan Out Across Services](#part-5)
- [Confirming Completion](#confirming-completion)
- [Where This Goes Next](#going-next)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

The scan gate for this demo is a single Make target at the repo root:

```bash
cd otterworks
make security-scan
```

It runs four scanner commands in sequence and prints their output in their
configured formats:

```text
=== Trivy Filesystem Scan ===
trivy fs --config security/scanning/trivy-config.yaml . || true
=== Node.js Audit (collab-service) ===
cd services/collab-service && npm audit 2>/dev/null || true
=== Python Audit (search-service) ===
cd services/search-service && pip-audit -r requirements.txt 2>/dev/null || true
=== Ruby Audit (admin-service) ===
cd services/admin-service && bundle-audit check 2>/dev/null || true
=== Report Service (skipped - legacy) ===
```

Two properties matter for the demo and are worth confirming in the recipe at
`Makefile:214` before you start:

- Every scan command ends in `|| true`, so **findings never fail the target**.
  Exit code 0 does not mean "clean." Someone has to read the output.
- `services/report-service` is skipped outright — the intentionally legacy Java 8
  service is excluded from scanning, not fixed.

Prerequisites: `trivy`, `npm`, `pip-audit`, and `bundle-audit` on the PATH. Any
missing scanner degrades silently for the same reason (`|| true`), which is
itself the first thing Devin reports in Part 1.

The live golden app is at [https://t-main.otterworks.app](https://t-main.otterworks.app)
(user SPA at `/`, admin dashboard at `/admin`, health at `/api/health`). It
tracks `main` and is the reference for "the platform still works after
remediation." Repository guidance says it must not receive chaos injection or
destructive changes.

---

<a id="repository"></a>
## Repository

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) —
  a collaborative file-storage and document-editing platform: 11 backend services
  (`api-gateway` Go, `auth-service` Java 17 / Boot 3, `file-service` Rust,
  `document-service` Python/FastAPI, `collab-service` Node/Yjs,
  `notification-service` Kotlin, `search-service` Python/Flask,
  `analytics-service` Scala, `admin-service` Ruby/Rails, `audit-service` C#, and
  `report-service` Java 8 / Boot 2.5 kept deliberately legacy), plus a React
  `frontend/client-app` and an Angular `frontend/admin-dashboard`, running on EKS
  with a multi-tenant demo platform.

The OtterWorks security surfaces the demo touches:

| Surface | Path | What it is |
|---|---|---|
| Scan gate | `Makefile` → `security-scan` | Trivy + `npm audit` + `pip-audit` + `bundle-audit` |
| Trivy settings | `security/scanning/trivy-config.yaml` | CRITICAL/HIGH, OS + library, table output, 10m timeout |
| Suppression list | `.trivyignore` | ~30 CVE entries, section comments, one wildcard |
| PR scan workflow | `.github/workflows/security-scan.yml` | Trivy diff vs. merge base, Gitleaks, Semgrep |
| Event-driven loop | `.github/workflows/sast-auto-remediate.yml` | Findings → PR comment → Devin session → escalation |
| Attendee lab | `workshops/otterworks/C1-security-sprint.md` | The hands-on version of this thread |

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

**Before**

- `make security-scan` prints CRITICAL/HIGH findings and exits 0.
- `.trivyignore` suppresses a year of CVEs with a wildcard.
- `report-service` is skipped.
- `main` contains dependency concerns documented in manifests and suppressions,
  hardcoded credentials in `etl/config.ini`, and a `.trivyignore` reference to
  a source directory that no longer exists.

**After**

- The same target is run again, with remediated findings absent from the output
  and the suppression list narrowed to entries that carry a reason.
- A branch carries dependency bumps, a rewritten `.trivyignore`,
  `SECURITY_BACKLOG.md`, and passing service tests; `main` remains untouched.

The verification loop is the point of the demo: a dependency bump is trusted only
when **the same scanner that reported the finding no longer reports it** and the
affected service's tests still pass. Nothing here relies on Devin asserting the
fix worked.

Two things about the before state that make it realistic rather than a toy:

1. The target is non-gating. Findings are printed, not enforced, so a successful
   target exit does not establish that the repository is clean.
2. The suppression list is where the real risk lives. `.trivyignore` ends with:

   ```text
   # Bulk ignore — revisit in Q4
   CVE-2021-*
   ```

   That pattern suppresses matching 2021 CVE IDs across the repository. The
   file does not provide a ticket, owner, or expiry for this entry, and it is
   unclear which Q4 the comment refers to.

Work on a branch named `demo-<id>` (for example `demo-secscan1`). Pushing that
branch triggers `.github/workflows/cd-tenant.yml`, which builds only the changed
services and deploys them to their own tenant at
`t-<id>.demo.otterworks.app` — a disposable copy of the platform for verifying
that a dependency bump did not break the running application. `t-main` stays on
`main` as the untouched reference.

---

<a id="part-1"></a>
## Part 1 — Triage What the Scanners Actually Report

Start by getting the raw output into a shape a security engineer can work from.
Paste this into Devin:

```
Using the Cognition-Partner-Workshops/otterworks repo, run
make security-scan from the repo root and triage the output.

The target is defined in Makefile around line 214 and runs
Trivy (config security/scanning/trivy-config.yaml), npm audit
in services/collab-service, pip-audit against
services/search-service/requirements.txt, and bundle-audit in
services/admin-service. Note that every scan command ends in
"|| true", so the target exits 0 even with findings, and that
services/report-service is skipped.

Produce SECURITY_BACKLOG.md at the repo root containing:

1. A table of every CRITICAL and HIGH finding: severity, CVE or
   advisory ID, package, installed version, fixed version, the
   exact manifest file that pins it, and which scanner reported
   it.
2. A section per affected service, ordered by how many
   CRITICAL/HIGH findings it carries.
3. A "Scan coverage gaps" section listing what this target does
   NOT cover — which scanners were unavailable on this machine,
   which ecosystems in the monorepo have no audit step at all
   (check each services/*/ manifest and both frontend/
   package.json files), and what the report-service exclusion
   hides.
4. A "Suppressed" section: which findings are absent from the
   output because .trivyignore suppressed them.

Report the scanner versions you used and quote the exit code of
make security-scan.
```

What comes back is more interesting than the finding count. Devin reports the
coverage gaps as first-class findings: the audit steps cover Node, Python, and
Ruby for exactly one service each, while `services/api-gateway` (Go),
`services/file-service` (Rust), `services/auth-service` and
`services/report-service` (Java), `services/audit-service` (C#),
`services/analytics-service` (Scala), `services/notification-service` (Kotlin),
and both frontends have no dedicated audit step — Trivy's filesystem scan is the
only thing looking at them.

The "Suppressed" section is where Part 2 begins.

---

<a id="part-2"></a>
## Part 2 — Audit the Suppression List

Overly broad suppressions are the highest-leverage finding in most repositories
and the one scanners structurally cannot report — a suppressed CVE produces no
output at all. Paste this:

```
Audit the .trivyignore file at the root of the
Cognition-Partner-Workshops/otterworks repo.

For every entry, determine:
- Which package and service it actually applies to today.
- Whether the stated reason in the section comment holds (for
  example, the "requires Angular 19+ upgrade" and "requires
  Rails 7.2+ upgrade" sections — check the real pinned versions
  in frontend/admin-dashboard/package.json and
  services/admin-service/Gemfile).
- Whether the entry suppresses more than its comment claims.
- Whether the referenced path still exists in the repo.

Two specific things to check and report on:
1. The "CVE-2021-*" wildcard entry commented "Bulk ignore —
   revisit in Q4". Enumerate what that single line suppresses
   across the monorepo by running Trivy with and without the
   ignore file and diffing the CRITICAL/HIGH results.
2. The "frontend/web-app" section header. Confirm whether that
   directory exists on main, and if not, name the directory
   that does.

Produce TRIVYIGNORE_AUDIT.md with a table of every entry marked
KEEP, NARROW, or REMOVE with a one-line reason, then rewrite
.trivyignore so that each remaining entry carries the affected
service, the reason it cannot be fixed now, and a review date.
Do not silently drop a suppression that is still load-bearing —
if removing an entry reintroduces a CRITICAL/HIGH finding, list
that finding in SECURITY_BACKLOG.md instead.
```

The diff-with-and-without-ignore-file step is what makes this concrete: instead
of arguing about whether a wildcard is bad practice, the session shows the exact
list of CRITICAL and HIGH findings that line was hiding. The stale
`frontend/web-app` header is a second, cheaper proof — the real directory is
`frontend/client-app`, so those suppression comments have been describing a
directory that does not exist. (The same stale path appears in `Makefile` build,
test, and lint targets, which Devin picks up while it is in there.)

---

<a id="part-3"></a>
## Part 3 — Remediate, Test, and Re-Scan

Now fix findings, with the re-scan as the gate. Paste this:

```
Using SECURITY_BACKLOG.md in the
Cognition-Partner-Workshops/otterworks repo, remediate the
CRITICAL and HIGH findings on a branch named demo-secscan1 cut
from main.

Work in this order and stop at the first finding you cannot fix
without a breaking change:

1. Dependency bumps to the fixed version in the correct
   manifest for that ecosystem — services/collab-service/
   package.json, services/search-service/requirements.txt,
   services/admin-service/Gemfile,
   services/document-service/pyproject.toml,
   services/file-service/Cargo.toml,
   services/api-gateway/go.mod. Update the corresponding lock
   file with the ecosystem's own tool, never by hand.
2. The hardcoded credentials in etl/config.ini (an AWS-style
   secret key, an "etl_pr0d_2019!" database password, and a
   MeiliSearch master key). Move them to environment variables
   following how other services read config, and confirm
   Gitleaks no longer reports them.

After each service:
- Run that service's tests (npm test, pytest, bundle exec
  rspec, cargo test, go test ./..., ./mvnw test as
  appropriate).
- Re-run make security-scan and show the specific finding is
  gone from the output.

Produce REMEDIATION_REPORT.md with a before/after table: CVE,
package, version before, version after, the test command you
ran with its result, and the re-scan line proving the finding
is absent. For anything you could not fix, record why (major
version bump, breaking API change) and add a narrowed
.trivyignore entry with the service, reason, and review date.
```

Watch for three specific behaviors in the session:

- **Ecosystem-correct edits.** A Node bump updates `package-lock.json` via `npm`;
  a Python bump updates the pin and, for `services/document-service`, goes
  through `poetry.lock` rather than a `requirements.txt` that does not exist for
  that service.
- **The re-scan as the gate.** The evidence in `REMEDIATION_REPORT.md` is a line
  of scanner output, not a claim.
- **An honest stop.** The `services/report-service` Guava `28.0-jre` finding on
  Java 8 (`services/report-service/pom.xml`) is not a dependency bump — it is the
  Java 8 → 17 upgrade covered by lab
  [A2](../../workshops/otterworks/A2-framework-upgrade.md). The correct outcome is a
  documented deferral with a narrowed suppression, not a forced bump.

### Confirm the platform still works

The findings are gone; the question is whether the application still runs. Push
the branch and let `.github/workflows/cd-tenant.yml` build the changed services
and deploy them to their own tenant:

```bash
git push origin demo-secscan1
# cd-tenant.yml: plan (changed services) -> build -> deploy
# tenant hostname: https://t-secscan1.demo.otterworks.app
```

Then compare the remediated tenant against the untouched golden app:

```bash
curl -s https://t-secscan1.demo.otterworks.app/api/health
curl -s https://t-main.otterworks.app/api/health   # reference, unchanged
```

Open both in a browser — the SPA at `/`, the admin dashboard at `/admin` — and
run the API flow suite against the tenant gateway:

```bash
OTTERWORKS_API_BASE_URL=https://api-t-secscan1.demo.otterworks.app \
  make test-api-flows
```

`t-main` is the control: it tracks `main`, so any behavior difference is
attributable to the remediation branch. Nothing in this demo modifies it.

---

<a id="part-4"></a>
## Part 4 — Hand the Loop to CI

Parts 1–3 are a human driving a session. The same loop already runs on this repo
without one. Two workflows on `main` do the work.

### The PR gate — `security-scan.yml`

```yaml
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 6 * * 1'
```

On a pull request it runs three jobs, all scoped to the diff:

`dependency-scan` uses Trivy `v0.71.0` with CRITICAL/HIGH severity,
`--skip-dirs services/report-service`, and `--ignorefile .trivyignore`. It
scans HEAD and the merge base, then fails **only on newly introduced** findings.

`secret-scan` uses Gitleaks `v8.21.2` with
`--log-opts "origin/$BASE_REF..HEAD"` for commits on this PR.

`sast` uses Semgrep with `p/default`, `p/owasp-top-ten`, and `p/security-audit`,
plus `--error --baseline-commit`.

The `full-scan-baseline` job runs on push to `main` and on the Monday cron with
full history and `set +e` — informational, non-gating. This split is deliberate
and worth stating plainly: **PRs are gated on the delta, the backlog is
reported.** That is what keeps a repo with an existing backlog mergeable while
still stopping new findings, and it is exactly why Parts 1–3 exist as a separate
burn-down thread.

### The remediation loop — `sast-auto-remediate.yml`

```text
PR opened / synchronized (author != devin-ai-integration[bot])
        ↓
Trivy fs scan, CRITICAL/HIGH -> trivy-results.json (uploaded, 7-day retention)
        ↓
Findings? --> no --> done
        ↓ yes
Count prior commits by devin-ai-integration[bot] on this PR
        ↓
attempts >= MAX_FIX_ATTEMPTS (2)?
   yes -> open issue "[Security] Unresolved SAST findings on PR #<n>"
           labels: security, needs-human-review
           + escalation PR comment
   no  -> post PR comment ":shield: SAST Scan — Findings Detected"
           + POST to the Devin Automation webhook
             (X-Webhook-Secret; payload: source, branch, pr_number,
              repository, findings_summary)
        ↓
Devin session remediates on the same branch and pushes
        ↓
synchronize event -> re-scan (closed loop)
```

A second path in the same workflow runs `SonarSource/sonarqube-scan-action@v5`,
polls for the quality gate, and on failure fires its own webhook — bounded to a
single attempt by checking the PR for an existing `Devin SAST Auto-Fix` comment.

Three mechanics are worth pointing at directly in the file:

- **Bot-loop prevention.** The author check skips PRs opened by
  `devin-ai-integration[bot]` entirely, and the attempt counter reads
  `pulls/<number>/commits?per_page=100` and counts commits authored by the bot.
  The loop is bounded by data on the PR, not by a timer.
- **The trigger is a webhook, not a bespoke API client.** The workflow POSTs to a
  [Devin Automation](https://docs.devin.ai/product-guides/automations) endpoint
  authenticated with `X-Webhook-Secret` and reads the session URL from `.url` in
  the response. The workflow delegates session creation to that automation
  endpoint rather than implementing session creation in YAML.
- **Escalation is the terminal state.** After two bot commits with findings still
  present, the workflow stops calling Devin and files a labeled issue. Bounded
  attempts plus a human handoff is what makes running this unattended
  defensible.

### Trigger it live

```bash
cd otterworks
git checkout -b demo-secloop main
# reintroduce a known-vulnerable pin in one manifest, e.g.
# services/collab-service/package.json
git commit -am "chore: pin dependency for scan demo"
git push origin demo-secloop
```

Open the PR against `main` as a human author and watch the **Checks** tab, then
the PR timeline for the `:shield: SAST Scan — Findings Detected` comment, the
fix commit from the bot, and the re-scan going green on `synchronize`. The
workflow summary contains the Devin session URL when the webhook returns one.

For the full walkthrough of this workflow, including the SonarCloud path and its
dashboard, see
[Event-Driven SAST Remediation](use-cases/event-driven-sast-remediation-demo.md).

---

<a id="part-5"></a>
## Part 5 — Fan Out Across Services

The backlog from Part 1 is partitioned by service, and the partitions are
independent — each has an ecosystem-specific manifest and test command, while
lockfile coverage varies. That makes it a fan-out. One parent session spawns a
child per service:

```
Act as the coordinator for a security remediation wave across
the Cognition-Partner-Workshops/otterworks monorepo, using
child Devin sessions to parallelize the work.

Read SECURITY_BACKLOG.md and group the remaining CRITICAL and
HIGH findings by service. Spawn one child session per affected
service. Give each child:
- its service directory and nothing outside it
- its own branch (demo-sec-<service>)
- the manifest and lock file for its ecosystem
- its test command (npm test, pytest, bundle exec rspec,
  cargo test, go test ./..., ./mvnw test, dotnet test,
  sbt test, ./gradlew test)
- the rule that a finding is fixed only when make
  security-scan no longer reports it and the service tests
  pass

Exclude services/report-service — its Java 8 findings need the
framework upgrade, not a dependency bump.

Monitor the children until each reports back, then write
REMEDIATION_SUMMARY.md with one row per service: findings
fixed, findings deferred with reasons, test result, and the
branch name. Call out any child whose dependency bump broke a
test and how it resolved it.
```

Nine language ecosystems, one backlog, one review bar applied in parallel
instead of in series. The same shape extends across repositories rather than
services — see
[Portfolio-Scale Remediation](use-cases/portfolio-scale-remediation-demo.md).

For the proactive counterpart — finding vulnerabilities in application code that
no scanner in this pipeline reports — see
[OtterWorks Application Threat Analysis](use-cases/otterworks.md).

---

<a id="confirming-completion"></a>
## Confirming Completion

Four things, in this order:

**1. The re-scan is the evidence.** Run `make security-scan` on the branch and
show whether the remediated CVEs are absent from the output. Compare against the
same command on `main` where useful. The scanner that reported the finding is
the thing that confirms the fix.

**2. The suppression list defends itself.** Open the rewritten `.trivyignore`.
Every remaining entry names its service, its reason, and a review date; the
`CVE-2021-*` wildcard is gone, and anything it was hiding is either fixed or
listed in `SECURITY_BACKLOG.md`. Suppressions are now a reviewable backlog rather
than silence.

**3. Tests pass and the platform runs.** Show the per-service test output in
`REMEDIATION_REPORT.md`, then the tenant at
`https://t-secscan1.demo.otterworks.app` serving the SPA and `/admin`, with
`make test-api-flows` green against its gateway.

**4. `main` and `t-main` are untouched.** The remediation lives on a branch and a
disposable tenant. [https://t-main.otterworks.app](https://t-main.otterworks.app)
still tracks `main` — which is what makes this safe to run repeatedly, and safe
to run concurrently with other sessions.

---

<a id="going-next"></a>
## Where This Goes Next

- **Close the coverage gaps Part 1 found.** Add audit steps for the ecosystems
  `make security-scan` skips today (Go, Rust, Java, C#, Scala, Kotlin, and both
  frontends), and decide deliberately whether `report-service` stays excluded.
- **Make the target fail.** Dropping the `|| true` on the scan commands turns a
  reporting target into a gate. Doing that after the burn-down, not before, is
  what makes it survivable.
- **Scheduled sessions.** The Monday `full-scan-baseline` cron writes scan
  summaries. A scheduled Devin session on the same cadence can triage that
  output and prepare the week's remediation changes.
- **Knowledge and playbooks.** Record the remediation conventions — which
  manifest per service, which test command, what a valid `.trivyignore` entry
  looks like — as a Knowledge note or playbook so future sessions and child
  sessions in Part 5 can use the same bar.
- **The attendee version.** `workshops/otterworks/C1-security-sprint.md` is the
  hands-on lab that walks this same ground at a keyboard.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **A passing scan is not a clean scan.** `make security-scan` ends every command
  in `|| true` and skips a service outright. The first thing Devin produced was a
  map of what the gate does not cover — the finding a scanner cannot report about
  itself.
- **Suppression lists are an important review surface of a security pipeline.**
  One `CVE-2021-*` line suppresses matching CVE IDs across nine language
  ecosystems with no owner or expiry in the file. Devin can quantify it by
  diffing Trivy with and without the ignore file, which turns a style argument
  into a list.
- **Remediation is verified by the scanner, not asserted.** A finding is fixed
  when the same scan no longer reports it and the service's tests still pass.
  Every row of `REMEDIATION_REPORT.md` carries both.
- **Polyglot is a scheduling problem, not a knowledge problem.** Nine language
  ecosystems are partitioned by service and can be run in parallel by child
  sessions, with `report-service` correctly deferred to a framework upgrade
  rather than forced.
- **The loop already runs without a human.** `sast-auto-remediate.yml` turns a
  finding into a PR comment, a webhook, and a bounded remediation session, then
  escalates to a labeled issue after two attempts. Guardrails live in the
  Automation; the workflow just supplies the event.
- **The gate is the delta; the backlog is a project.** PR jobs compare against
  the merge base so new findings are blocked while existing ones stay visible.
  That separation is what makes an incremental burn-down possible on a repo with
  real debt.
- **Verification against a running system, safely.** Remediation lands on a
  branch that gets its own disposable tenant, with the perpetual
  [t-main](https://t-main.otterworks.app) deployment as the untouched control.
