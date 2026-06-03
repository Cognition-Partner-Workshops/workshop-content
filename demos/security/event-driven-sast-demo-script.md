# Event-Driven SAST Remediation — Demo Script

A step-by-step presenter script for demonstrating autonomous security
remediation on the OtterWorks polyglot monorepo. Two acts: burn down an
existing vulnerability backlog with Devin, then show the CI gate that
prevents new vulnerabilities from shipping.

<a id="toc"></a>
## Table of Contents

- [Prerequisites](#prerequisites)
- [Act 1 — The Problem: A Real Vulnerability Backlog](#act-1)
- [Act 2 — The Burn-Down: Devin Triages and Fixes](#act-2)
- [Act 3 — The CI Gate: Shift-Left on New Code](#act-3)
- [Act 4 — The Closed Loop: Verify and Escalate](#act-4)
- [Key Takeaways](#key-takeaways)

---

<a id="prerequisites"></a>
## Prerequisites

Before starting, confirm:

1. **OtterWorks is on SonarCloud:**
   [cognition-partner-workshops org](https://sonarcloud.io/organizations/cognition-partner-workshops/projects)
   → project `Cognition-Partner-Workshops_otterworks` is listed.

2. **Workflow is on `main`:**
   PR [#255](https://github.com/Cognition-Partner-Workshops/otterworks/pull/255)
   has been merged. The `sast-auto-remediate.yml` workflow is active.

3. **GitHub secrets are configured:**
   `DEVIN_API_KEY`, `DEVIN_ORG_ID`, `DEVIN_CREATE_AS_USER_ID` in
   the otterworks repo settings.

---

<a id="act-1"></a>
## Act 1 — The Problem: A Real Vulnerability Backlog

**Goal:** Show the audience a real polyglot monorepo with real security
findings — not a contrived example.

**Duration:** ~2 minutes

### What to show

Open the SonarCloud dashboard in a browser:

```
https://sonarcloud.io/project/overview?id=Cognition-Partner-Workshops_otterworks
```

**Visual cue — project overview page.** Point to the headline numbers:

| Metric | Count | What it means |
|--------|-------|---------------|
| Vulnerabilities | 29 | Hard-coded secrets, weak crypto, missing S3 bucket ownership checks |
| Security Hotspots | 51 | CSRF disabled, recursive COPY in Dockerfiles, ReDoS-prone regex |
| Bugs | 24 | Logic errors across services |
| Code Smells | 434 | Maintainability issues |
| Lines of Code | 33,857 | Across 11 backend services + 2 frontends |

### What to say

> "This is OtterWorks — a polyglot monorepo with 11 backend services written in
> Go, Rust, Python, Java, Kotlin, Scala, Ruby, C#, and Node.js, plus two
> TypeScript frontends. SonarCloud has already analyzed it. We have 29
> vulnerabilities, 51 security hotspots, and 24 bugs. This is our starting
> state."

### Drill down — show specific findings

Click into the **Vulnerabilities** tab. Point to a few:

| Severity | Finding | File |
|----------|---------|------|
| BLOCKER | Hard-coded PostgreSQL password | `docker-compose.yml:365` |
| BLOCKER | Hard-coded token in auth service | `frontend/admin-dashboard/src/app/core/services/auth.service.ts:81` |
| BLOCKER | Bcrypt hash in migration seed | `services/auth-service/src/main/resources/db/migration/V1__create_users_table.sql:30` |
| CRITICAL | Weak bcrypt parameters | `scripts/seed.py:53` |

Click into the **Security Hotspots** tab. Point to:

| Risk | Finding | File |
|------|---------|------|
| HIGH | CSRF protection disabled | `services/search-service/app/main.py:58` |
| MEDIUM | ReDoS-prone regex | `frontend/web-app/src/components/documents/document-card.tsx:113` |
| MEDIUM | Recursive COPY in Dockerfile (×7) | Multiple service Dockerfiles |

> "These span 9 languages and touch services from the API gateway to the search
> service. Manually triaging and fixing all of these is a multi-day effort for
> an engineering team. Let's have Devin do it."

---

<a id="act-2"></a>
## Act 2 — The Burn-Down: Devin Triages and Fixes

**Goal:** Show Devin autonomously reading the SonarCloud dashboard and fixing
vulnerabilities across the polyglot repo.

**Duration:** ~3 minutes (kick off the session; results arrive async)

### Open Devin and paste

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
5. Push and open a PR.

Group related fixes per service into single PRs when
possible (e.g., all docker-compose.yml password issues
in one PR, all Dockerfile COPY issues in another).

Skip findings in test files — those are acceptable.
```

### What to show while Devin works

Switch back to the SonarCloud dashboard. Keep it visible. As Devin opens PRs,
the audience can see the vulnerability count start to drop on the next analysis
cycle.

> "Devin is now reading the SonarCloud API, understanding each vulnerability,
> and fixing them in the correct language and manifest. It knows that
> `docker-compose.yml` passwords need environment variable references, that
> `seed.py` needs stronger bcrypt rounds, and that Dockerfiles need
> `.dockerignore` or targeted COPY statements."

### Show the Devin session

Point to:
- Devin reading the SonarCloud issue list
- Devin checking out a branch
- Devin editing the correct file in the correct service
- Devin running the service tests
- Devin opening a PR

> "One agent. Nine languages. No human triaging which file goes to which team."

---

<a id="act-3"></a>
## Act 3 — The CI Gate: Shift-Left on New Code

**Goal:** Show that the same pipeline prevents *new* vulnerabilities from
shipping. A human opens a PR; scanners fire automatically; Devin remediates
without being asked.

**Duration:** ~3 minutes

### Open a PR to trigger the pipeline

In a terminal (or have one pre-staged):

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

### What to show — the GitHub Actions tab

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

### Visual cue — PR timeline

The PR comment timeline will show (in order):

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

> "Nobody asked Devin to do this. The PR event triggered a Trivy scan,
> the scan found vulnerabilities, and the workflow called the Devin API.
> Devin is now checking out this branch and upgrading dependencies across
> Python, Node.js, Ruby, Rust, and Go — all from a single pipeline."

### Show the workflow file (briefly)

Open `.github/workflows/sast-auto-remediate.yml` and scroll through:

- **Lines 14–19:** Dual triggers — `pull_request` + `check_run`
- **Lines 27–28:** Bot-loop prevention — `if: user.login != 'devin-ai-integration[bot]'`
- **Lines 50–56:** Attempt counter with `?per_page=100` pagination
- **Lines 156–210:** Devin v3 API call with `jq`-escaped prompt
- **Lines 293–302:** SonarCloud `check_run` filter conditions

> "Two scanner paths, one workflow. Trivy catches dependency CVEs instantly.
> SonarCloud catches code-level issues after analysis. Both route to Devin
> through the same v3 API."

---

<a id="act-4"></a>
## Act 4 — The Closed Loop: Verify and Escalate

**Goal:** Show that the system is self-healing — Devin's fix triggers a re-scan,
and if the fix doesn't work, the pipeline escalates.

**Duration:** ~1 minute

### What happens next (narrate)

> "When Devin pushes a fix commit, three things happen:
>
> 1. The `synchronize` event re-triggers Trivy. If findings are gone, CI goes
>    green. The PR is ready to merge.
>
> 2. SonarCloud re-analyzes the PR. If the quality gate now passes, no new
>    Devin session is created — the one-time guard prevents it.
>
> 3. If Devin's fix doesn't resolve everything, the pipeline runs again. After
>    two failed attempts, it stops calling Devin and opens a GitHub Issue labeled
>    `security` and `needs-human-review`. The right humans get notified; Devin
>    doesn't loop forever."

### Visual cue — show the escalation path

If available, show an existing GitHub Issue created by the escalation job, or
describe the format:

```
Title: SAST findings require manual review — PR #999
Labels: security, needs-human-review
Body:
  Devin attempted to remediate these findings 2 times
  without success. Manual review is required.

  [Full findings summary attached]
```

> "This is the safety net. Automated remediation handles the 80% case —
> straightforward dependency upgrades and well-documented fixes. The 20%
> that requires architectural decisions or breaking changes gets escalated
> to humans. The system knows its limits."

---

<a id="key-takeaways"></a>
## Key Takeaways

Recap these points at the end:

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

6. **From backlog to CI gate** — Act 2 showed burning down existing tech debt.
   Act 3 showed preventing new debt from shipping. Together, this is a complete
   security posture: remediate the past and guard the future.
