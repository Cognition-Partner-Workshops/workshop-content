# Mass Security Backlog Remediation

## Table of Contents

- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Architecture](#architecture)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [timesheet-app](#timesheet-app)
- [uc-cve-remediation-regulatory-compliance](#uc-cve-remediation-regulatory-compliance)
- [Going Further](#going-further)

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-cve-remediation-regulatory-compliance](#uc-cve-remediation-regulatory-compliance)

---

## Challenge

Execute a mass security backlog remediation across multiple repositories using Devin's agent orchestration capabilities. A single **parent agent** reads a consolidated SAST scan report covering 2 repositories, triages findings by severity, and launches **child agents** — one per repository — to remediate findings in parallel. This demonstrates enterprise-scale security remediation where a backlog of hundreds of findings across dozens of services needs to be addressed systematically.

This is an **agent orchestration pattern** — it demonstrates Devin coordinating parallel work streams, not just doing a single task.

## Quick Start

Paste this prompt into Devin to get started immediately:

```
You are coordinating a mass security remediation across
2 repositories. First, run security scans on both repos
to build a consolidated findings report:

Repo 1 — timesheet-app: Run npm audit --json and capture
the output. Also run npx eslint . --format json to check
for security-related lint violations.

Repo 2 — uc-cve-remediation-regulatory-compliance: Run
./gradlew dependencyCheckAnalyze and capture the OWASP
DC report.

Create a consolidated SECURITY_BACKLOG.md that lists
findings across both repos, organized by severity
(CRITICAL > HIGH > MEDIUM). For each finding, note: the
repo, the dependency/file, the CVE/rule ID, and the
CVSS score.

Then remediate the CRITICAL and HIGH findings in
timesheet-app: upgrade vulnerable npm dependencies, fix
ESLint security violations, and verify with a re-scan.
Open a PR in timesheet-app with the fixes and a
REMEDIATION_REPORT.md documenting what was fixed.
```

## Architecture

```
Facilitator provides consolidated SAST report
(covering timesheet-app + uc-cve-remediation-regulatory-compliance)
        ↓
Parent Devin session:
  1. Reads the consolidated report
  2. Triages findings by repo and severity
  3. Creates a remediation plan (which repo gets which fixes)
  4. Launches Child Session 1 → timesheet-app
  5. Launches Child Session 2 → uc-cve-remediation-regulatory-compliance
        ↓
Child sessions run in parallel:
  - Each reads its portion of the remediation plan
  - Each remediates findings for its assigned repo
  - Each opens a PR with fixes and a REMEDIATION_REPORT.md
        ↓
Parent session:
  - Monitors child session completion
  - Produces a consolidated REMEDIATION_SUMMARY.md
  - Reports overall status
```

## Target Outcomes

- Parent Devin session that reads a SAST report and creates a remediation plan
- 2 child Devin sessions launched in parallel (one per repo)
- Each child opens a PR with security fixes and a per-repo `REMEDIATION_REPORT.md`
- Consolidated `REMEDIATION_SUMMARY.md` from the parent summarizing changes across repos
- Evidence of parallel execution: both child sessions running simultaneously

## What Participants Will Learn

- How to use Devin for multi-repo, coordinated work (agent orchestration)
- Parent/child session patterns: planning → delegation → aggregation
- Enterprise-scale security remediation strategy (triage, prioritize, parallelize)
- How Devin handles context boundaries (each child session gets scoped context)
- The value of a consolidated report for audit/compliance purposes

## Devin Features Exercised

- Agent orchestration (parent/child sessions)
- Parallel session execution
- Multi-repo work (each child session targets a different repo)
- SAST report interpretation and triage
- Consolidated reporting across multiple work streams
- PR creation in multiple repos simultaneously

## Difficulty

Advanced

## Estimated Time

90 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express application. The child agent for this repo will focus on npm dependency vulnerabilities, ESLint security rules, and secrets detection.

### Step 1: Paste into Devin (Parent Session Prompt)

```
You are coordinating a mass security remediation across
2 repositories. First, run security scans on both repos
to build a consolidated findings report:

Repo 1 — timesheet-app: Run npm audit --json and capture
the output. Also run npx eslint . --format json to check
for security-related lint violations.

Repo 2 — uc-cve-remediation-regulatory-compliance: Run
./gradlew dependencyCheckAnalyze and capture the OWASP
DC report.

Create a consolidated SECURITY_BACKLOG.md that lists
findings across both repos, organized by severity
(CRITICAL > HIGH > MEDIUM). For each finding, note: the
repo, the dependency/file, the CVE/rule ID, and the
CVSS score.

Then remediate the CRITICAL and HIGH findings in
timesheet-app: upgrade vulnerable npm dependencies, fix
ESLint security violations, and verify with a re-scan.
Open a PR in timesheet-app with the fixes and a
REMEDIATION_REPORT.md documenting what was fixed.
```

### Step 2: Research with Ask Devin

- *"What are the most critical npm vulnerabilities in timesheet-app? Are any of them in direct dependencies vs. transitive dependencies?"*
- *"What's the fastest way to remediate transitive dependency vulnerabilities — override in package.json or upgrade the parent package?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the dependency tree and which packages are most critical to the application's security posture.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — did the child session fix the CRITICAL findings? Are there any regressions (broken tests, changed behavior)?
- **Leave a comment** asking Devin to also generate an SBOM (CycloneDX) and add it to the PR

### Key Takeaways

- **Parent-child orchestration**: The parent session triages and delegates; child sessions execute in parallel — each on its own VM, branch, and PR
- **Consolidated visibility**: The `SECURITY_BACKLOG.md` and `REMEDIATION_SUMMARY.md` give a single-pane view across repos
- **Scoped context**: Each child session receives only its portion of the remediation plan, keeping scope manageable
- **Parallel execution**: Both repos are remediated simultaneously, collapsing what would be sequential manual work into concurrent agent work

---

## <a id="uc-cve-remediation-regulatory-compliance"></a>uc-cve-remediation-regulatory-compliance

**Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)

Spring Boot 2.6.3 / Gradle application with 18+ known CVEs. The child agent for this repo will focus on dependency upgrades (Spring Boot, SnakeYAML, SQLite JDBC) and Gradle configuration changes.

### Step 1: Paste into Devin (Child Session Prompt)

```
Remediate the CRITICAL and HIGH CVEs in
uc-cve-remediation-regulatory-compliance based on the
following prioritized findings from the OWASP
Dependency-Check scan:

1. Spring Boot 2.6.3 (CVE-2022-22965 Spring4Shell,
   CVSS 9.8) — upgrade to Spring Boot 3.2+
2. SnakeYAML 1.29 (CVE-2022-1471, CVSS 9.8) —
   upgrade to 2.x
3. sqlite-jdbc 3.36.0.3 (CVE-2023-32697, CVSS 9.8) —
   upgrade to latest
4. Spring Security (multiple CVEs) — addressed by
   Spring Boot upgrade
5. Jackson Databind (CVE-2022-42003, CVSS 7.5) —
   upgrade to 2.15+

For each fix: upgrade the dependency in build.gradle,
handle any breaking API changes (especially
javax → jakarta for Spring Boot 3), and verify the
build passes. Re-run ./gradlew dependencyCheckAnalyze
to confirm remediation. Create a REMEDIATION_REPORT.md
with before/after CVSS scores.
```

### Step 2: Research with Ask Devin

- *"What's the dependency chain for the Spring4Shell vulnerability in this repo? Which transitive dependencies also need to be updated when upgrading Spring Boot?"*
- *"What breaking changes should I expect when upgrading from Spring Boot 2.6.3 to 3.2?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the dependency graph and application architecture. Plan the upgrade order to minimize breakage.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the Spring Boot 3 migration complete (javax→jakarta)? Do tests pass?
- **Leave a comment** asking Devin to also add a GitHub Actions workflow that runs OWASP DC on every PR to prevent future regression

### Key Takeaways

- **Cascading upgrades**: Upgrading Spring Boot resolves multiple transitive CVEs (Spring Security, Tomcat Embed) in a single change
- **Breaking change handling**: The javax → jakarta namespace migration is a non-trivial side effect of major version upgrades — Devin handles it end-to-end
- **Before/after evidence**: The `REMEDIATION_REPORT.md` with CVSS score comparisons provides auditable proof of remediation
- **Re-scan verification**: Running `./gradlew dependencyCheckAnalyze` after fixes confirms that CVEs are actually resolved, not just claimed

---

## Going Further

- **Webhook-driven automation**: Connect your SAST scanner to Devin via webhooks so that new findings automatically trigger a parent orchestration session. The parent triages, spawns child agents per repo, and produces a consolidated remediation summary — no human has to read the scan report or assign work.
- **Divide and conquer with child sessions**: The 2-repo scope in this module is intentionally small for a workshop. In production, the same pattern scales to 10+ repos: the parent agent reads the consolidated report, partitions findings by repository, and spawns a child agent for each. Each child follows the same playbook — ensuring consistent remediation methodology across the organization.
- **Scheduled recurring analysis**: Configure a weekly scheduled Devin session that runs SAST scans across your critical repos, triages new findings, and remediates them before they accumulate into a backlog. Combine with Knowledge notes (encoding your team's remediation preferences) and playbooks (standardizing the fix methodology) so that each scheduled run applies the same organizational standards.
