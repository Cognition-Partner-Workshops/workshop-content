# Portfolio-Scale Parallel Remediation Demo

A multi-repo demo showing Devin's parent-child orchestration for security
remediation at scale. A parent agent reads a consolidated vulnerability report
covering two repositories with different technology stacks, triages findings by
severity, and launches child agents — one per repository — to remediate in
parallel. Each child produces its own PR with fixes and a remediation report.
The parent aggregates results into a summary.

<a id="toc"></a>
## Table of Contents

- [Prerequisites](#prerequisites)
- [Repositories](#repositories)
- [Architecture](#architecture)
- [Part 1 — Build the Consolidated Report](#part-1)
- [Part 2 — Launch Parallel Remediation](#part-2)
- [Part 3 — Monitor Child Execution](#part-3)
- [Part 4 — Review the Aggregated Results](#part-4)
- [Part 5 — Add Shared Context for Consistency](#part-5)
- [Key Takeaways](#key-takeaways)

---

<a id="prerequisites"></a>
## Prerequisites

1. **Access to both repos:**
   - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) —
     Node.js/Express application with npm dependency vulnerabilities
   - [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance) —
     Spring Boot 2.6.3 / Gradle application with 18+ known CVEs

2. **Devin org access:** Both repos are connected to the Devin organization.

---

<a id="repositories"></a>
## Repositories

| Repo | Stack | Scanner | Findings profile |
|------|-------|---------|-----------------|
| timesheet-app | Node.js / Express | `npm audit`, Trivy | Dependency CVEs (axios, lodash, express transitive deps) |
| uc-cve-remediation-regulatory-compliance | Spring Boot / Gradle | OWASP Dependency-Check | Spring4Shell (CVE-2022-22965), SnakeYAML (CVE-2022-1471), sqlite-jdbc (CVE-2023-32697) |

Two different ecosystems, two different scanners, two different remediation
strategies — all coordinated by a single parent agent.

---

<a id="architecture"></a>
## Architecture

```
Parent Devin session
        ↓
  1. Scans both repos (npm audit + Gradle dependencyCheck)
  2. Creates consolidated SECURITY_BACKLOG.md
  3. Triages findings by severity (CRITICAL > HIGH)
  4. Launches child sessions:
        ↓
┌─────────────────────────┐  ┌─────────────────────────────────────┐
│ Child 1: timesheet-app  │  │ Child 2: uc-cve-remediation-        │
│                         │  │          regulatory-compliance       │
│ • npm dependency        │  │ • Gradle dependency upgrades         │
│   upgrades              │  │ • javax → jakarta migration          │
│ • ESLint security       │  │ • Spring Boot 2.6 → 3.2             │
│   violations            │  │ • Re-run OWASP DC to verify          │
│ • Re-run npm audit      │  │                                      │
│   to verify             │  │                                      │
└─────────────────────────┘  └─────────────────────────────────────┘
        ↓                              ↓
     PR opened                      PR opened
     REMEDIATION_REPORT.md          REMEDIATION_REPORT.md
        ↓                              ↓
        └──────────┬───────────────────┘
                   ↓
        Parent aggregates results
        → REMEDIATION_SUMMARY.md
```

Each child runs on its own VM with its own branch. The parent monitors
completion and produces the consolidated summary.

---

<a id="part-1"></a>
## Part 1 — Build the Consolidated Report

Paste this prompt into Devin to scan both repositories and produce a single
consolidated findings report:

```
You are coordinating a security remediation across 2
repositories. First, run security scans on both repos
to build a consolidated findings report:

Repo 1 — Cognition-Partner-Workshops/timesheet-app:
Run npm audit --json and capture the output. Also run
npx eslint . --format json to check for
security-related lint violations.

Repo 2 — Cognition-Partner-Workshops/
uc-cve-remediation-regulatory-compliance:
Run ./gradlew dependencyCheckAnalyze and capture the
OWASP Dependency-Check report.

Create a consolidated SECURITY_BACKLOG.md that lists
findings across both repos, organized by severity
(CRITICAL > HIGH > MEDIUM). For each finding, note:
the repo, the dependency or file, the CVE or rule ID,
and the CVSS score where available.
```

Devin clones both repos, runs the scanners, and produces `SECURITY_BACKLOG.md`.
The report groups findings by severity and annotates each with the affected
repository, enabling targeted delegation in the next step.

### What to observe

- Devin installs dependencies for both ecosystems (`npm install`, `./gradlew`)
- Each scanner produces findings in its native format (JSON, XML)
- The consolidated report normalizes findings into a single severity-ranked list
- CRITICAL findings (CVSS 9.0+) appear first: Spring4Shell, SnakeYAML, sqlite-jdbc

---

<a id="part-2"></a>
## Part 2 — Launch Parallel Remediation

Once the backlog is built, launch the full orchestration. Paste this prompt
to trigger parent-child remediation:

```
Using the SECURITY_BACKLOG.md you just created,
remediate all CRITICAL and HIGH findings across both
repositories in parallel.

Launch a child session for timesheet-app that:
- Upgrades vulnerable npm dependencies to fixed versions
- Fixes ESLint security violations
- Runs npm audit again to verify findings are resolved
- Creates a REMEDIATION_REPORT.md documenting each fix

Launch a child session for
uc-cve-remediation-regulatory-compliance that:
- Upgrades Spring Boot from 2.6.3 to 3.2+
- Upgrades SnakeYAML to 2.x
- Upgrades sqlite-jdbc to latest
- Handles javax → jakarta namespace migration
- Runs ./gradlew dependencyCheckAnalyze to verify
- Creates a REMEDIATION_REPORT.md with before/after
  CVSS scores

After both children complete, produce a consolidated
REMEDIATION_SUMMARY.md summarizing: total findings
addressed, per-repo status, and any findings that
could not be automatically resolved (for human
escalation).
```

The parent session:
1. Reads the backlog
2. Partitions findings by repository
3. Launches two child sessions in parallel — each with scoped instructions
4. Monitors child completion
5. Aggregates results

### What to observe

- Two child session URLs appear in the parent session timeline
- Each child runs on its own isolated VM
- The children work simultaneously — no sequential bottleneck
- Each child's instructions are scoped to its repository only

---

<a id="part-3"></a>
## Part 3 — Monitor Child Execution

While children run, observe the parallel execution:

**Child 1 (timesheet-app):**
- Runs `npm audit` to identify affected packages
- Upgrades packages in `package.json` / `package-lock.json`
- Runs `npm test` to verify no regressions
- Re-runs `npm audit` — findings count drops

**Child 2 (uc-cve-remediation-regulatory-compliance):**
- Upgrades Spring Boot in `build.gradle` (2.6.3 → 3.2+)
- Handles cascading changes: `javax.servlet` → `jakarta.servlet`,
  `javax.persistence` → `jakarta.persistence`
- Upgrades SnakeYAML and sqlite-jdbc
- Runs `./gradlew build` to verify compilation
- Re-runs `./gradlew dependencyCheckAnalyze` — CVEs resolved

Each child opens its own PR:
- timesheet-app PR: dependency bumps + ESLint fixes
- uc-cve-remediation-regulatory-compliance PR: major version upgrade with
  namespace migration

### Timing

Both children typically complete within 15–20 minutes. The Node.js repo
(straightforward dependency bumps) finishes faster than the Spring Boot repo
(major version migration with breaking changes). The parent waits for both
before producing the summary.

---

<a id="part-4"></a>
## Part 4 — Review the Aggregated Results

When both children complete, the parent produces `REMEDIATION_SUMMARY.md`:

```markdown
# Remediation Summary

## Overview
- Repositories scanned: 2
- Total CRITICAL/HIGH findings: N
- Findings remediated: N
- Findings requiring human review: N

## Per-Repository Status

### timesheet-app
- PR: #XX
- Findings fixed: N of N
- Method: npm dependency upgrades
- Verification: npm audit clean

### uc-cve-remediation-regulatory-compliance
- PR: #XX
- Findings fixed: N of N
- Method: Spring Boot major version upgrade
- Verification: OWASP DC report clean
- Note: javax → jakarta migration applied

## Escalations
- [any findings that couldn't be auto-fixed]
```

This consolidated report is the audit artifact: one document covering
remediation status across the entire portfolio, produced automatically.

### Review the PRs

Open both PRs and compare:

- **timesheet-app PR:** Straightforward `package.json` bumps. Tests pass.
  Small diff — Devin targeted only the vulnerable packages.
- **uc-cve-remediation-regulatory-compliance PR:** Larger diff — Spring Boot
  major upgrade touches imports across multiple files. Build passes.
  `REMEDIATION_REPORT.md` shows before/after CVSS scores.

---

<a id="part-5"></a>
## Part 5 — Add Shared Context for Consistency

For production use, configure the shared context layer so that every
remediation session applies the same organizational standards. This makes
parallel execution consistent — each child agent follows the same methodology
regardless of which repository it targets.

### Knowledge notes

Create a Knowledge note in Devin Settings → Knowledge:

```
Name: Security Remediation Standards
Trigger: When remediating security vulnerabilities

Contents:
- Always prefer direct dependency upgrades over transitive
  pinning
- Run the full test suite after any dependency change
- If a major version upgrade is required, check for
  breaking changes in the changelog before upgrading
- Never downgrade a dependency to resolve a CVE
- If a finding cannot be resolved without architectural
  changes, document it in REMEDIATION_REPORT.md and
  mark it for human escalation
- Include before/after CVSS scores in remediation reports
```

### Playbooks

Create a playbook for the remediation pattern:

```
Name: Security Remediation
Trigger: Manual or via scheduled session

Steps:
1. Run the repo's configured scanner
2. Triage findings by severity (CRITICAL first)
3. For each finding: identify the fix, apply it, run tests
4. Re-run the scanner to verify
5. Produce REMEDIATION_REPORT.md
6. If findings remain after 2 attempts, escalate
```

When a parent session launches children with a playbook reference, every child
applies the same methodology. This eliminates variation — whether the repo is
Node.js, Java, Python, or Go, the remediation process follows the same
organizational standard.

---

<a id="key-takeaways"></a>
## Key Takeaways

1. **Parent-child orchestration for scale** — one parent session coordinates
   the entire portfolio. Child sessions run in parallel on isolated VMs, each
   targeting a specific repository. No sequential bottleneck.

2. **Technology-agnostic coordination** — the parent doesn't need to know how
   to fix Spring Boot CVEs or npm vulnerabilities. It delegates to children
   with scoped instructions. Each child handles its own ecosystem.

3. **Consolidated audit trail** — `SECURITY_BACKLOG.md` (before) and
   `REMEDIATION_SUMMARY.md` (after) provide a single-pane view of portfolio
   security posture for compliance and audit.

4. **Shared context layer as consistency mechanism** — Knowledge notes and
   playbooks ensure every child agent applies the same remediation methodology.
   This scales to 10, 50, or 100 repos without degrading quality.

5. **Timeline compression** — what would be weeks of sequential manual work
   (scan, triage, fix, test, repeat — per repo) becomes a single coordinated
   session producing parallel PRs. The elapsed time is the longest single
   child, not the sum of all children.

6. **Human escalation built in** — findings that cannot be auto-fixed are
   documented and escalated. The system knows its limits and routes
   appropriately.

---

For the event-driven version of this pattern — where CI scans automatically
trigger remediation sessions — see
[Event-Driven SAST Remediation](event-driven-sast-remediation-demo.md).

For the single-repo variant with a polyglot monorepo, see the
[General Security Demo](../general.md).
