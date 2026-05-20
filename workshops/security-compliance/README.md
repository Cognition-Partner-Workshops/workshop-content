# Workshop: Security & Compliance

## Overview

| | |
|---|---|
| **Focus** | CVE remediation, vulnerability scanning, SAST, shift-left security practices |
| **Duration** | 1-2 hours |
| **Audience** | Security engineers, DevSecOps teams, compliance-focused developers |
| **Key Modules** | [Upgrade Dependencies](../../modules/security/upgrade-dependencies.md), [Remediate Vulnerabilities](../../modules/security/remediate-vulnerabilities.md), [Shift Left Security](../../modules/security/shift-left-security.md), [Security Antipatterns](../../modules/security/security-antipatterns.md), [Secrets Management & Detection](../../modules/security/secrets-management-detection.md), [Event-Driven SAST Remediation](../../modules/security/event-driven-sast-remediation.md), [Mass Security Backlog Remediation](../../modules/security/mass-security-backlog-remediation.md) |

## Workshop Narrative

Security remediation is often a backlog item that never gets prioritized. This workshop demonstrates how Devin can scan, remediate, and add automated compliance checks — turning a security backlog into an afternoon of focused work.

## Getting the Most from This Workshop

> **Devin works asynchronously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

A few tips to maximize your hands-on time:

- **Start sessions early, review later.** Kick off the session first, then use the wait time for Ask Devin research or reading DeepWiki — Devin keeps working in the background.
- **Use Ask Devin to refine requirements.** The better-defined a task is, the better Devin's output. Ask Devin helps you think through the problem before Devin executes.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that compounds over time.
- **Leave PR comments to steer Devin.** After Devin opens a PR, you can leave comments and Devin will wake up and address them — this is the core feedback loop.

## Table of Contents

- [Lab 1 — CVE Remediation & Dependency Upgrades](#lab-1--cve-remediation--dependency-upgrades)
- [Lab 2 — Shift-Left Security & SAST](#lab-2--shift-left-security--sast)
- [Repos Required](#repos-required)
- [Key Takeaways](#key-takeaways)

---

## Labs

### Lab 1 — CVE Remediation & Dependency Upgrades

- **Modules:** [Upgrade Dependencies](../../modules/security/upgrade-dependencies.md) + [Remediate Vulnerabilities](../../modules/security/remediate-vulnerabilities.md)
- **Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- **Objective:** Scan a Spring Boot app for dependency CVEs, remediate the critical findings, and verify the fixes
- **Duration:** 60 min

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

```
Run `./gradlew dependencyCheckAnalyze` on uc-cve-remediation-regulatory-compliance to identify dependency CVEs. Remediate the top 5 most critical findings (CVSS >= 7.0) — start with Spring Boot 2.6.3, SnakeYAML 1.29, and sqlite-jdbc 3.36.0.3. Re-run the scan to verify the fixes. Create a `SECURITY_REMEDIATION.md` documenting the before/after results.
```

#### Step 2: Research with Ask Devin

- *"What are the known CVEs in the dependencies? Which ones are CRITICAL severity?"*
- *"What's the safest upgrade path for Spring Boot 2.6.3 — go to 2.7.x first or jump to 3.x?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page. Try adding SBOM generation, GitHub Actions security scanning, SonarQube integration, or pre-commit hooks for secrets detection.

#### Step 4 (Optional): Review & Give Feedback

- Verify the right dependencies were upgraded
- Ask Devin to add a CI workflow that fails on CRITICAL CVEs

**Target Outcomes:** OWASP report with CVEs remediated, SBOM generated, CI gating workflow, remediation documentation

---

### Lab 2 — Shift-Left Security & SAST

- **Modules:** [Shift Left Security](../../modules/security/shift-left-security.md) + [Event-Driven SAST Remediation](../../modules/security/event-driven-sast-remediation.md)
- **Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- **Objective:** Add automated security scanning to the CI pipeline and remediate SAST findings
- **Duration:** 45 min

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

```
Add a GitHub Actions workflow to uc-cve-remediation-regulatory-compliance that runs Trivy vulnerability scanning on every PR. The workflow should fail the build if any CRITICAL or HIGH severity CVEs are found. Also add a pre-commit hook configuration for gitleaks secrets detection.
```

#### Step 2: Research with Ask Devin

- *"What SAST tools are available for Spring Boot applications? Compare Trivy, Snyk, and OWASP Dependency-Check."*
- *"What pre-commit hooks should a security-focused repo have?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page. Try adding SonarQube scanning (the repo has a pre-configured `docker-compose.sonarqube.yml`), or implement secrets scanning with gitleaks.

#### Step 4 (Optional): Review & Give Feedback

- Verify the CI workflow is correctly configured
- Ask Devin to add severity thresholds or exclusion lists for known false positives

**Target Outcomes:** CI security scanning workflow, pre-commit hooks, SAST integration

## Repos Required

- [ ] uc-cve-remediation-regulatory-compliance

## Key Takeaways

- **"Security backlog → afternoon of work"** — Devin systematically remediates CVEs that would take weeks manually
- **"Shift left"** — automated scanning in CI catches new vulnerabilities before they reach production
- **"Evidence-based compliance"** — SBOM, scan reports, and remediation documentation provide audit trails
