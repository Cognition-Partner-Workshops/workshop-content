# Event-Driven SAST Remediation — Polyglot Monorepo Demo

A single-thread demo showing Devin operating as a background security agent on
the OtterWorks polyglot monorepo. Two scanner paths — Trivy (dependency CVEs)
and SonarCloud (code quality gate) — both feed into the Devin v3 API for
autonomous remediation across Go, Rust, Python, Java, Node.js, Kotlin, Ruby,
C#, and Scala services. The full scan → fix → re-scan loop runs hands-free.

<a id="toc"></a>
## Table of Contents

- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Architecture](#architecture)
- [Part 1 — The Problem: A Real Vulnerability Backlog](#part-1)
- [Part 2 — The Burn-Down: Devin Triages and Fixes](#part-2)
- [Part 3 — The CI Gate: Trigger It Live](#part-3)
- [Part 4 — SonarCloud Quality Gate Path](#part-4)
- [Part 5 — The Closed Loop: Verify and Escalate](#part-5)
- [Part 6 — Scale with Child Sessions](#part-6)
- [Key Takeaways](#key-takeaways)

---

<a id="prerequisites"></a>
## Prerequisites

Before starting, confirm:

1. **OtterWorks is on SonarCloud:**
   [cognition-partner-workshops org](https://sonarcloud.io/organizations/cognition-partner-workshops/projects)
   → project `Cognition-Partner-Workshops_otterworks` is listed.

2. **Workflow is on `main`:**
   The `sast-auto-remediate.yml` workflow is active on the otterworks repo.

3. **GitHub secrets are configured:**
   `DEVIN_API_KEY`, `DEVIN_ORG_ID`, `DEVIN_CREATE_AS_USER_ID` in
   the otterworks repo settings.

---

<a id="quick-start"></a>
## Quick Start

Paste this into Devin to build the dual-scanner event-driven pipeline on OtterWorks:

```
Create a GitHub Actions workflow called
sast-auto-remediate.yml on the otterworks repo that
supports two trigger paths:

PATH 1 — Trivy (pull_request trigger):
(1) triggers on PRs opened by users other than
    devin-ai-integration[bot],
(2) runs a Trivy filesystem scan for HIGH and CRITICAL
    severity vulnerabilities (skip services/report-service,
    respect .trivyignore),
(3) parses the Trivy JSON output and posts a PR comment
    summarizing findings by service,
(4) if findings exist and fewer than 2 fix attempts have
    been made, calls the Devin v3 API to create a
    remediation session on the same branch,
(5) if findings persist after 2 attempts, opens a GitHub
    Issue for human review.

PATH 2 — SonarCloud (check_run trigger):
(1) listens for check_run completed events from the
    sonarqubecloud GitHub app,
(2) when the quality gate fails, validates the PR is
    open and not authored by devin-ai-integration[bot],
(3) checks that Devin hasn't already attempted a fix
    (one-time remediation),
(4) calls the Devin v3 API with SonarCloud dashboard
    link and remediation instructions,
(5) posts a PR comment with the Devin session link.

Also create sonar-project.properties for the polyglot
monorepo and write docs/EVENT_DRIVEN_SECURITY.md
documenting the architecture, bot-loop prevention, and
escalation policy.
```

---

<a id="repositories"></a>
## Repositories

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) —
  polyglot monorepo with 11 backend services, 2 frontends, and pre-existing
  Trivy/Semgrep/Gitleaks CI. Has planted CVEs across Node.js (`lodash`),
  Python (`urllib3`), and Ruby (`activestorage`) services. Onboarded to
  [SonarCloud](https://sonarcloud.io/organizations/cognition-partner-workshops/projects)
  for code quality gate analysis.

---

<a id="architecture"></a>
## Architecture

```
Developer opens PR
        ↓
GitHub Actions: sast-auto-remediate.yml
        ↓
┌──────────────────────┬───────────────────────────────┐
│ PATH 1: Trivy        │ PATH 2: SonarCloud            │
│ (pull_request)       │ (check_run)                   │
│                      │                               │
│ Trivy fs scan        │ SonarCloud GitHub App         │
│ HIGH + CRITICAL      │ analyzes PR automatically     │
│     ↓                │     ↓                         │
│ Findings?            │ Quality gate failed?          │
│   No → pass          │   No → pass                  │
│   Yes ↓              │   Yes ↓                       │
│                      │                               │
│ attempts < 2?        │ Already attempted fix?        │
│   No → escalate      │   Yes → skip (one-time)      │
│   Yes ↓              │   No ↓                        │
│                      │                               │
│ Post PR comment      │ Validate PR state             │
│     ↓                │     ↓                         │
├──────────────────────┴───────────────────────────────┤
│           Devin v3 API → remediation session         │
│                                                      │
│  Devin checks out branch, fixes vulnerabilities,     │
│  runs service tests, pushes fix commit               │
│           ↓                                          │
│  Push triggers re-scan (closed loop):                │
│  • Trivy: synchronize event                          │
│  • SonarCloud: new check_run                         │
└──────────────────────────────────────────────────────┘
```

**Bot-loop prevention:** The workflow checks the PR author — PRs from
`devin-ai-integration[bot]` are skipped entirely. For the Trivy path, a
secondary counter tracks Devin's commits and stops after `MAX_FIX_ATTEMPTS`.
For the SonarCloud path, a comment-based check prevents re-triggering.

**Closed-loop verification:** When Devin pushes a fix, the `synchronize` event
re-triggers Trivy, and SonarCloud re-analyzes via the GitHub App. If findings
are gone, CI passes and the PR is unblocked.

---

<a id="part-1"></a>
## Part 1 — The Problem: A Real Vulnerability Backlog

Open the SonarCloud dashboard:

```
https://sonarcloud.io/project/overview?id=Cognition-Partner-Workshops_otterworks
```

The project overview page shows the headline numbers:

| Metric | Count | What it means |
|--------|-------|---------------|
| Vulnerabilities | 29 | Hard-coded secrets, weak crypto, missing S3 bucket ownership checks |
| Security Hotspots | 51 | CSRF disabled, recursive COPY in Dockerfiles, ReDoS-prone regex |
| Bugs | 24 | Logic errors across services |
| Code Smells | 434 | Maintainability issues |
| Lines of Code | 33,857 | Across 11 backend services + 2 frontends |

OtterWorks is a polyglot monorepo with 11 backend services written in Go, Rust,
Python, Java, Kotlin, Scala, Ruby, C#, and Node.js, plus two TypeScript
frontends. SonarCloud has already analyzed it — 29 vulnerabilities, 51 security
hotspots, and 24 bugs across 9 languages.

### Drill down — specific findings

Click into the **Vulnerabilities** tab:

| Severity | Finding | File |
|----------|---------|------|
| BLOCKER | Hard-coded PostgreSQL password | `docker-compose.yml:365` |
| BLOCKER | Hard-coded token in auth service | `frontend/admin-dashboard/src/app/core/services/auth.service.ts:81` |
| BLOCKER | Bcrypt hash in migration seed | `services/auth-service/src/main/resources/db/migration/V1__create_users_table.sql:30` |
| CRITICAL | Weak bcrypt parameters | `scripts/seed.py:53` |

Click into the **Security Hotspots** tab:

| Risk | Finding | File |
|------|---------|------|
| HIGH | CSRF protection disabled | `services/search-service/app/main.py:58` |
| MEDIUM | ReDoS-prone regex | `frontend/web-app/src/components/documents/document-card.tsx:113` |
| MEDIUM | Recursive COPY in Dockerfile (×7) | Multiple service Dockerfiles |

These span 9 languages and touch services from the API gateway to the search
service. Manually triaging and fixing all of these is a multi-day effort for an
engineering team.

---

<a id="part-2"></a>
## Part 2 — The Burn-Down: Devin Triages and Fixes

Open Devin and paste this prompt to start remediating the backlog:

```
Review the SonarCloud dashboard for otterworks at
https://sonarcloud.io/project/issues?id=Cognition-Partner-Workshops_otterworks&types=VULNERABILITY&resolved=false

There are 29 open vulnerabilities across the polyglot
monorepo. Triage them by severity (BLOCKER first, then
CRITICAL, then MAJOR). For each one:

1. Read the SonarCloud issue detail to understand the fix.
2. Check out a branch from main.
3. Fix the vulnerability in the correct file.
4. Run the affected service's tests.
5. Push the fix.

Group related fixes per service into single PRs when
possible (e.g., all docker-compose.yml password issues
in one PR, all Dockerfile COPY issues in another).

Skip findings in test files — those are acceptable.
```

Switch back to the SonarCloud dashboard while Devin works. As Devin opens PRs,
the vulnerability count drops on the next analysis cycle.

Devin reads the SonarCloud API, understands each vulnerability, and fixes them
in the correct language and manifest. It knows that `docker-compose.yml`
passwords need environment variable references, that `seed.py` needs stronger
bcrypt rounds, and that Dockerfiles need `.dockerignore` or targeted COPY
statements.

Watch for in the Devin session:
- Devin reading the SonarCloud issue list
- Devin checking out a branch
- Devin editing the correct file in the correct service
- Devin running the service tests
- Devin opening a PR

One agent, 9 languages — no human triaging which file goes to which team.

---

<a id="part-3"></a>
## Part 3 — The CI Gate: Trigger It Live

The same pipeline prevents *new* vulnerabilities from shipping. Open a PR as a
human author and watch both scanners fire automatically.

### Open a PR to trigger the pipeline

```bash
cd otterworks
git checkout -b workshop-test-sast main
echo "# Security pipeline test" >> README.md
git add README.md
git commit -m "docs: trigger SAST pipeline"
git push origin workshop-test-sast
```

Open a PR against `main` via the GitHub UI or:

```bash
gh pr create --title "docs: trigger SAST pipeline" \
  --body "Testing event-driven security remediation" \
  --base main
```

### The GitHub Actions tab

Open the PR in GitHub. Navigate to the **Checks** tab. Two things happen
in parallel:

**Path 1 — Trivy (immediate):**
1. `sast-scan` job runs Trivy → finds HIGH/CRITICAL dependency CVEs
2. `comment-findings` job posts a PR comment with findings grouped by service
3. `trigger-devin` job calls the Devin v3 API → new remediation session

**Path 2 — SonarCloud (after ~60s):**
1. SonarCloud GitHub App analyzes the PR (runs automatically)
2. If the quality gate fails, a `check_run` event fires
3. `sonarcloud-trigger-devin` job validates the PR and calls Devin v3 API

### PR timeline

The PR comment timeline shows (in order):

```
[Bot] Trivy SAST Findings — 31 HIGH/CRITICAL vulnerabilities
      across 6 targets:
      • frontend/web-app: axios, next.js CVEs
      • services/admin-service: activestorage, jwt CVEs
      • services/collab-service: lodash, protobufjs CVEs
      • services/document-service: urllib3, mako CVEs
      • services/file-service: rustls-webpki CVEs
      • services/search-service: urllib3 CVEs

[Bot] Devin SAST Auto-Fix — Remediation In Progress
      Session: https://app.devin.ai/sessions/...
      This is fix attempt 1 of 2.
```

Nobody asked Devin to do this. The PR event triggered a Trivy scan, the scan
found vulnerabilities, and the workflow called the Devin API. Devin checks out
the branch and upgrades dependencies across Python, Node.js, Ruby, Rust, and
Go — all from a single pipeline.

### Review the polyglot fixes

Devin's PR commits typically touch 3+ languages in a single remediation cycle:

- `services/collab-service/package.json` — `lodash` bumped to 4.17.21+
- `services/search-service/requirements.txt` — `urllib3` bumped to 2.x
- `services/admin-service/Gemfile` — Rails/ActiveStorage updated

Each fix targets the specific manifest file for that service's ecosystem. Devin
runs the per-service tests (`npm test`, `pytest`, `bundle exec rspec`) before
pushing to avoid breaking changes.

### The workflow file

Open `.github/workflows/sast-auto-remediate.yml` and scroll through:

- **Lines 14–19:** Dual triggers — `pull_request` + `check_run`
- **Lines 27–28:** Bot-loop prevention — `if: user.login != 'devin-ai-integration[bot]'`
- **Lines 50–56:** Attempt counter with `?per_page=100` pagination
- **Lines 156–210:** Devin v3 API call with `jq`-escaped prompt
- **Lines 293–302:** SonarCloud `check_run` filter conditions

Two scanner paths, one workflow. Trivy catches dependency CVEs instantly.
SonarCloud catches code-level issues after analysis. Both route to Devin
through the same v3 API.

---

<a id="part-4"></a>
## Part 4 — SonarCloud Quality Gate Path

When the PR from Part 3 is pushed, the SonarCloud GitHub App also analyzes it.
If the quality gate fails (security hotspots, bugs, or code smells exceed
thresholds), the `check_run` event fires with `conclusion: failure`.

The `sonarcloud-trigger-devin` job:
1. Confirms the check_run is from `sonarqubecloud` and the conclusion is `failure`
2. Validates the PR is open and not authored by Devin
3. Checks that no prior Devin fix comment exists on the PR
4. Calls the Devin v3 API with the SonarCloud dashboard URL for the PR
5. Posts a comment linking to the Devin session

### Compare the two scanner paths

| Aspect | Trivy Path | SonarCloud Path |
|--------|-----------|-----------------|
| Trigger | `pull_request` (opened, synchronize) | `check_run` (completed) |
| Scanner | Trivy CLI (dependency CVEs) | SonarCloud GitHub App (code quality) |
| Findings | Known CVEs with fixed versions | Security hotspots, bugs, code smells |
| Loop guard | Commit counter (`MAX_FIX_ATTEMPTS`) | Comment-based one-time check |
| Escalation | GitHub Issue after max attempts | One-time attempt only |
| Re-scan | `synchronize` event re-triggers Trivy | New SonarCloud `check_run` |

Both paths use the same Devin v3 API endpoint and produce a session with tags
for filtering in the Devin dashboard.

---

<a id="part-5"></a>
## Part 5 — The Closed Loop: Verify and Escalate

When Devin pushes a fix commit, three things happen:

1. The `synchronize` event re-triggers Trivy. If findings are gone, CI goes
   green. The PR is ready to merge.

2. SonarCloud re-analyzes the PR. If the quality gate now passes, no new
   Devin session is created — the one-time guard prevents it.

3. If Devin's fix doesn't resolve everything, the pipeline runs again. After
   two failed attempts, it stops calling Devin and opens a GitHub Issue labeled
   `security` and `needs-human-review`. The right humans get notified; Devin
   doesn't loop forever.

### The escalation path

Navigate to the **Issues** tab in the otterworks repo and look for an issue
created by the escalation job. The format:

```
Title: SAST findings require manual review — PR #999
Labels: security, needs-human-review
Body:
  Devin attempted to remediate these findings 2 times
  without success. Manual review is required.

  [Full findings summary attached]
```

Automated remediation handles the 80% case — straightforward dependency upgrades
and well-documented fixes. The 20% that requires architectural decisions or
breaking changes gets escalated to humans. The system knows its limits.

---

<a id="part-6"></a>
## Part 6 — Scale with Child Sessions

For enterprise-scale remediation (many findings across many services), use
parent-child orchestration. The parent session triages findings and launches
one child session per service for parallel remediation.

```
You are coordinating a polyglot security remediation
across the otterworks monorepo.

Run make security-scan and capture the output. Create a
SECURITY_BACKLOG.md that lists all CRITICAL and HIGH
findings organized by service.

Then launch parallel child sessions — one per affected
service — with scoped instructions:
- Each child works only on its assigned service directory
- Each child upgrades the vulnerable dependency, runs
  the service tests, and pushes to the same branch
- After all children complete, summarize results in
  REMEDIATION_SUMMARY.md
```

This demonstrates the same event-driven pattern at organizational scale: one
scan produces a backlog, and multiple agents remediate in parallel.

---

<a id="key-takeaways"></a>
## Key Takeaways

1. **Devin as a background security agent** — not a tool you open, but a service
   that responds to CI events. Developers commit code; Devin handles security
   remediation.

2. **Dual-scanner architecture** — Trivy catches dependency CVEs (known
   vulnerabilities with fixed versions). SonarCloud catches code-level issues
   (hard-coded secrets, CSRF gaps, ReDoS patterns). Both feed into the same
   Devin API.

3. **Polyglot at scale** — one pipeline, 9 languages, 11 services. Devin reads
   the right manifest (`package.json`, `Gemfile`, `go.mod`, `Cargo.toml`,
   `pyproject.toml`, etc.) and runs the right test command for each ecosystem.

4. **Closed-loop verification** — the same CI that found the problem re-scans
   after Devin's fix. No manual verification step for dependency upgrades.

5. **Guardrails built in** — bot-loop prevention (author check + attempt
   counter), concurrency groups (no duplicate sessions), one-time remediation
   (SonarCloud path), and human escalation after max attempts.

6. **From backlog to CI gate** — Part 2 shows burning down existing tech debt.
   Part 3 shows preventing new debt from shipping. Together, this is a complete
   security posture: remediate the past and guard the future.

7. **Scanner-agnostic** — the architecture works with any scanner that produces
   parseable output or GitHub check_run events. Add Snyk by swapping the scan
   step; the Devin API integration and escalation logic stay the same.
