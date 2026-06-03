# Event-Driven SAST Remediation — Polyglot Monorepo Demo

A single-thread demo showing Devin operating as a background security agent on
the OtterWorks polyglot monorepo. Two scanner paths — Trivy (dependency CVEs)
and SonarCloud (code quality gate) — both feed into the Devin v3 API for
autonomous remediation across Go, Rust, Python, Java, Node.js, Kotlin, Ruby,
C#, and Scala services. The full scan → fix → re-scan loop runs hands-free.

<a id="toc"></a>
## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Architecture](#architecture)
- [Part 1 — Set Up the Pipeline](#part-1)
- [Part 2 — Trigger It Live (Trivy Path)](#part-2)
- [Part 3 — SonarCloud Quality Gate Path](#part-3)
- [Part 4 — Scale with Child Sessions](#part-4)
- [Key Takeaways](#key-takeaways)

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
  polyglot monorepo with 10 backend services, 2 frontends, and pre-existing
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
## Part 1 — Set Up the Pipeline

### Step 1: Review existing security scanning

OtterWorks already runs Trivy, Semgrep, and Gitleaks on every PR via
`.github/workflows/security-scan.yml`. The new workflow extends this by adding
the Devin API integration for automated remediation via two scanner paths.

Existing scan infrastructure:

| Scanner | Scope | Config |
|---------|-------|--------|
| Trivy | Dependency CVEs across all services | `security/scanning/trivy-config.yaml` |
| Semgrep | OWASP Top 10 + security audit rules | Inline config |
| Gitleaks | Secrets in git history | Default rules |
| SonarCloud | Code quality gate (security hotspots, bugs, code smells) | `sonar-project.properties` |

### Step 2: Build the event-driven workflow

Paste this prompt into Devin:

```
Analyze .github/workflows/security-scan.yml in the
otterworks repo. It runs Trivy, Semgrep, and Gitleaks on
PRs. Extend this pattern by creating a new workflow called
sast-auto-remediate.yml that supports two paths:

PATH 1 — Trivy (pull_request trigger):
(1) triggers on pull_request (opened, synchronize) to main,
(2) skips PRs where the author is devin-ai-integration[bot],
(3) runs Trivy fs scan with --severity CRITICAL,HIGH
    --format json, skipping services/report-service and
    respecting .trivyignore,
(4) parses the JSON to extract per-service findings summary,
(5) posts findings as a PR comment,
(6) if findings exist and attempts < 2, calls the Devin v3
    API (POST https://api.devin.ai/v3/organizations/
    {ORG_ID}/sessions) with a prompt for branch checkout,
    dependency upgrades, test runs, and push,
(7) if findings persist after 2 attempts, opens a GitHub
    Issue labeled "security" and "needs-human-review".

PATH 2 — SonarCloud (check_run trigger):
(1) listens for check_run completed from sonarqubecloud app,
(2) when conclusion is failure, validates PR is open and
    not authored by devin-ai-integration[bot],
(3) checks for existing Devin fix comment (one-time guard),
(4) calls the Devin v3 API with SonarCloud dashboard link
    and remediation instructions,
(5) posts a PR comment with the Devin session link.

Also create sonar-project.properties and write
docs/EVENT_DRIVEN_SECURITY.md documenting the full
architecture.
```

### Step 3: Understand what Devin built

While Devin works, review the planted vulnerabilities that the scan will find:

| Service | Language | Vulnerability | Manifest |
|---------|----------|---------------|----------|
| collab-service | Node.js | `lodash` 4.17.20 — command injection | `package.json` |
| search-service | Python | `urllib3` 1.26.5 — ReDoS | `requirements.txt` |
| admin-service | Ruby | `activestorage` CVEs | `Gemfile` |

Also review `.trivyignore` — it contains an overly broad `CVE-2021-*` glob
pattern that silently suppresses real findings.

---

<a id="part-2"></a>
## Part 2 — Trigger It Live (Trivy Path)

### Step 4: Open a PR as a human

Create a branch with a minor change (e.g., a README edit) and open a PR against
`main`. This triggers the Trivy scan path as a non-bot author.

```bash
git checkout -b workshop-test-sast main
echo "# Security test" >> README.md
git add README.md
git commit -m "docs: trigger SAST pipeline"
git push origin workshop-test-sast
# Open PR via GitHub UI or gh CLI
```

### Step 5: Watch the scan → fix → re-scan loop

1. The `sast-scan` job runs Trivy and finds HIGH/CRITICAL CVEs
2. A PR comment appears with the findings summary, grouped by service
3. The `trigger-devin` job calls the Devin v3 API — a new session starts
4. Devin checks out the branch, upgrades `lodash`, `urllib3`, and the
   `activestorage` gems, runs each service's tests, and pushes
5. The push triggers the `synchronize` event → the workflow re-runs
6. If all findings are resolved, the scan passes and CI goes green

### Step 6: Review the polyglot fixes

Devin's PR commits typically touch 3+ languages in a single remediation cycle:

- `services/collab-service/package.json` — `lodash` bumped to 4.17.21+
- `services/search-service/requirements.txt` — `urllib3` bumped to 2.x
- `services/admin-service/Gemfile` — Rails/ActiveStorage updated

Each fix targets the specific manifest file for that service's ecosystem. Devin
runs the per-service tests (`npm test`, `pytest`, `bundle exec rspec`) before
pushing to avoid breaking changes.

---

<a id="part-3"></a>
## Part 3 — SonarCloud Quality Gate Path

### Step 7: Observe SonarCloud analysis on the same PR

When the PR from Step 4 is pushed, the SonarCloud GitHub App also analyzes it.
If the quality gate fails (security hotspots, bugs, or code smells exceed
thresholds), the `check_run` event fires with `conclusion: failure`.

The `sonarcloud-trigger-devin` job:
1. Confirms the check_run is from `sonarqubecloud` and the conclusion is `failure`
2. Validates the PR is open and not authored by Devin
3. Checks that no prior Devin fix comment exists on the PR
4. Calls the Devin v3 API with the SonarCloud dashboard URL for the PR
5. Posts a comment linking to the Devin session

### Step 8: Compare the two scanner paths

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

<a id="part-4"></a>
## Part 4 — Scale with Child Sessions

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

- **Devin as a background agent**: Devin is not a tool someone opens — it
  responds to CI events automatically. Developers commit code; Devin handles
  security remediation.
- **Dual-scanner architecture**: Trivy catches dependency CVEs while SonarCloud
  catches code-level security hotspots — both feed into the same Devin v3 API
  for unified autonomous remediation.
- **Polyglot remediation**: A single pipeline covers Go, Rust, Python, Java,
  Node.js, Kotlin, Ruby, C#, and Scala — Devin reads the right manifest for
  each ecosystem.
- **Closed-loop verification**: The same CI that found the problem re-scans
  after Devin's fix. No manual verification needed for dependency upgrades.
- **Bot-loop prevention**: Author filtering, attempt counting, and comment-based
  guards prevent infinite remediation cycles across both scanner paths.
- **Escalation policy**: When automation is not enough, the pipeline opens an
  issue for human review rather than looping forever.
- **Scanner-agnostic**: The architecture works with any scanner that produces
  parseable output or GitHub check_run events. Add Snyk by swapping the scan
  step; the Devin API integration and escalation logic stay the same.
