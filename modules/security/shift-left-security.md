# Shift Left Security

## Table of Contents

- [Challenge](#challenge)
- [Quick Start](#quick-start)
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

Define or enhance the CI process to improve security posture. Automate Devin's ability to check its own work for vulnerabilities — creating a feedback loop where security scanning happens on every PR.

## Quick Start

Paste this prompt into Devin to get started immediately:

```
Review the existing security scanning workflows in
timesheet-app (.github/workflows/). Enhance them by
adding: SBOM generation in CycloneDX format, a
dependency-review step on PRs, and a Trivy container
scan if Dockerfiles exist. The workflow should fail
the PR on CRITICAL severity findings.
```

## Target Outcomes

- Security scanning workflow added to CI (Trivy, Snyk, OWASP, or SonarQube)
- SBOM generation configured (CycloneDX or SPDX format)
- PRs fail on CRITICAL or HIGH severity findings
- PR with workflow configuration

## What Participants Will Learn

- How Devin creates CI/CD workflows from scratch
- How to create a feedback loop where Devin checks its own security posture
- The concept of "Devin fixing what Devin (or CI) finds"

## Devin Features Exercised

- CI/CD authoring (GitHub Actions)
- Security tool configuration
- Automated feedback loops

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Has existing `sast-scan.yml` and `pr-checks.yml` workflows — good for studying and enhancing existing security CI.

### Step 1: Paste into Devin

```
Review the existing security scanning workflows in
timesheet-app (.github/workflows/). Enhance them by
adding: SBOM generation in CycloneDX format, a
dependency-review step on PRs, and a Trivy container
scan if Dockerfiles exist. The workflow should fail
the PR on CRITICAL severity findings.
```

### Step 2: Research with Ask Devin

- *"What security scanning is already configured in timesheet-app's CI? What gaps exist?"*
- *"How does the sast-scan.yml workflow trigger Devin to auto-fix issues? Can this pattern be extended to other tools?"*
- Study the existing automated remediation pattern to understand the Devin API integration

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the existing CI/CD setup and identify which security checks are missing.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the workflow triggers correct (on PR, on push to main)?
- **Leave a comment** asking Devin to add a pre-commit hook for secrets detection (gitleaks)
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- **Enhancing existing CI**: Building on existing workflows (`sast-scan.yml`, `pr-checks.yml`) is more practical than starting from scratch — it preserves team conventions and avoids duplication
- **SBOM as compliance artifact**: CycloneDX SBOM generation provides a machine-readable inventory of dependencies — increasingly required by regulatory frameworks
- **Fail-fast on CRITICAL**: Configuring CI to block PRs on CRITICAL findings shifts security enforcement from a manual review step to an automated gate
- **Self-healing loop**: The pattern of "CI finds → Devin fixes → CI re-checks" creates a closed-loop remediation cycle

---

## <a id="uc-cve-remediation-regulatory-compliance"></a>uc-cve-remediation-regulatory-compliance

**Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)

No CI currently (workflows were removed) — but has OWASP Dependency-Check and SonarQube Gradle plugins pre-configured for local scanning. Ideal for building security CI from scratch.

### Step 1: Paste into Devin

```
Create a GitHub Actions CI pipeline for
uc-cve-remediation-regulatory-compliance that: builds
with Gradle, runs ./gradlew dependencyCheckAnalyze,
fails the PR if any dependency has CVSS >= 7.0,
generates an SBOM in CycloneDX format, and uploads
the dependency check report as a build artifact.
```

### Step 2: Research with Ask Devin

- *"What's the best way to integrate OWASP Dependency-Check into GitHub Actions for a Gradle project?"*
- *"Should the CI also run SonarQube analysis? What's the simplest way to set that up without a hosted SonarQube instance?"*
- Use the plan to create a comprehensive security CI pipeline

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the build configuration and identify which Gradle plugins are already configured for security scanning.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the workflow cache the NVD database to speed up subsequent runs?
- **Leave a comment** asking Devin to add dependency-review-action for PR-level dependency diff analysis
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- **Building CI from scratch**: Creating a complete security pipeline in a repo with no existing CI demonstrates how Devin scaffolds infrastructure, not just application code
- **CVSS threshold enforcement**: Configuring a CVSS >= 7.0 failure threshold balances security rigor with developer workflow — blocking on the most critical findings without creating alert fatigue
- **Artifact preservation**: Uploading the dependency check report as a build artifact provides audit evidence and enables trend analysis across builds
- **Pre-configured plugins**: Leveraging the existing Gradle plugins (OWASP DC, SonarQube) means Devin works with what's already there rather than introducing new tooling

---

## Going Further

- **Webhook-driven automation**: Extend the CI pipeline with a webhook that triggers a Devin session whenever a PR fails on CRITICAL findings. Devin reads the failure report, remediates the issue, and pushes a fix to the same branch — creating an autonomous scan → fix → re-scan loop.
- **Divide and conquer with child sessions**: When rolling out security CI across an organization, use a parent Devin session to create a standardized workflow template (as a playbook), then spawn child sessions — one per repo — to apply the template across your service estate. Each child adapts the template to the repo's build system and opens a PR.
- **Scheduled recurring analysis**: Configure weekly scheduled Devin sessions to run SBOM generation and dependency checks. Over time, this builds a compliance-ready audit trail of your dependency posture — useful for SOC 2, ISO 27001, and PCI DSS reporting. Store the SBOM format and threshold configuration as Knowledge notes so every scan applies consistent organizational standards.
