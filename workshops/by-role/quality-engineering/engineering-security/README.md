# Workshop: Quality Engineering & Security Vulnerability Remediation

## Overview

| | |
|---|---|
| **Focus** | Quality engineering (first half) + security vulnerability remediation (second half) |
| **Duration** | ~3 hours (flexible: 90 min per half + breaks) |
| **Audience** | QA/QE engineers, AppSec/DevSecOps practitioners, engineering leads responsible for test coverage or security compliance |
| **Key Modules** | [Linting & Static Analysis](../../../../labs/testing-qa/linting-static-analysis.md), [Unit Testing](../../../../labs/testing-qa/unit-testing.md), [Upgrade Dependencies](../../../../labs/security/upgrade-dependencies.md), [Remediate Vulnerabilities](../../../../labs/security/remediate-vulnerabilities.md) |

## Abstract

> Join us for a hands-on workshop exploring how AI-powered software engineering accelerates two critical disciplines: quality engineering and security vulnerability remediation.
>
> **First half — Quality Engineering:** Participants will use Devin to add linting rules, generate unit tests, build E2E test suites, and produce documentation — turning under-tested codebases into well-covered, maintainable projects.
>
> **Second half — Security Vulnerability Remediation:** Participants will run local SAST tools (OWASP Dependency-Check, SonarQube Community Edition) to identify dependency CVEs and code-level vulnerabilities, then use Devin to remediate findings, upgrade vulnerable dependencies, and add shift-left CI gating.
>
> **Who should attend:** QA/QE engineers, AppSec and DevSecOps practitioners, engineering leads responsible for test coverage or security compliance, and anyone curious about applying AI assistants to quality and security workflows.

## Getting the Most from This Workshop

> **Devin works asynchronously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

A few tips to maximize your hands-on time:

- **Start sessions early, review later.** Kick off the session first, then use the wait time for Ask Devin research or reading DeepWiki — Devin keeps working in the background.
- **Use Ask Devin to refine requirements.** The better-defined a task is, the better Devin's output. Ask Devin helps you think through the problem before Devin executes.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that compounds over time.
- **Leave PR comments to steer Devin.** After Devin opens a PR, you can leave comments and Devin will wake up and address them — this is the core feedback loop.
- **Try parallel sessions.** Running multiple sessions simultaneously mirrors real enterprise usage and demonstrates team-based operation at scale.

## Table of Contents

- [Structure](#structure)
- [Featured Labs](#featured-labs)
  - [Lab 1 — Linting & Unit Testing](#lab-1--linting--unit-testing-45-min)
  - [Lab 2 — E2E Testing & Documentation](#lab-2--e2e-testing--documentation-45-min)
  - [Lab 3 — CVE Remediation with Local SAST Tools](#lab-3--cve-remediation-with-local-sast-tools-60-min)
  - [Lab 4 — Shift Left & Security Antipatterns](#lab-4--shift-left--security-antipatterns-45-min)
- [Repos Required](#repos-required-on-devins-machine)

---

## Structure

Two halves, each with 2 structured labs:

1. **First Half — Quality Engineering (~90 min):** Labs 1 and 2 cover linting, unit testing, E2E testing, and documentation
2. **Break (15 min)**
3. **Second Half — Security Vulnerability Remediation (~90 min):** Labs 3 and 4 cover SAST scanning, CVE remediation, dependency upgrades, and shift-left CI

---

## Featured Labs

### Lab 1 — Linting & Unit Testing (45 min)
- **Modules:** [Linting & Static Analysis](../../../../labs/testing-qa/linting-static-analysis.md) + [Unit Testing](../../../../labs/testing-qa/unit-testing.md)
- **Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)
- **Objective:** Start by resolving linting issues (GitHub Issue #3), then improve unit test coverage and generate a coverage report
- **Target Outcomes:**
  - All ESLint/Prettier violations resolved
  - Unit test coverage increased (target: 80%+)
  - Coverage report generated
  - PR with linting fixes and new tests

### Lab 2 — E2E Testing & Documentation (45 min)
- **Modules:** [End-to-End Testing](../../../../labs/testing-qa/end-to-end-testing.md) + [Inline Documentation](../../../../labs/technical-documentation/inline-documentation.md)
- **Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)
- **Alternative E2E repo:** [calcom](https://github.com/Cognition-Partner-Workshops/calcom) (for participants wanting a more complex target)
- **Objective:** Write and run E2E tests against the locally running application, then improve inline documentation across the codebase
- **Target Outcomes:**
  - E2E test suite created (Playwright or Selenium)
  - Devin screen recording of test execution as evidence
  - JSDoc/inline documentation added to key modules
  - PR with tests and documentation

### Lab 3 — CVE Remediation with Local SAST Tools (60 min)
- **Modules:** [Upgrade Dependencies](../../../../labs/security/upgrade-dependencies.md) + [Remediate Vulnerabilities](../../../../labs/security/remediate-vulnerabilities.md)
- **Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- **Objective:** Use pre-configured local SAST tools to scan for vulnerabilities, remediate the most critical findings, and upgrade outdated dependencies
- **Target Outcomes:**
  - OWASP Dependency-Check report generated (before and after remediation)
  - SonarQube analysis completed with security hotspots reviewed
  - High/critical vulnerabilities patched or upgraded (Spring Boot 2.6.3 upgraded)
  - `SECURITY_REMEDIATION.md` with before/after evidence
  - PR with all remediations documented

#### Pre-configured SAST Tools (both local — no cloud costs)

| Tool | What It Scans | Command |
|------|--------------|---------|
| [OWASP Dependency-Check](https://owasp.org/www-project-dependency-check/) | Dependencies against the NVD for known CVEs | `./gradlew dependencyCheckAnalyze` |
| [SonarQube Community Edition](https://www.sonarsource.com/open-source-editions/sonarqube-community-edition/) | Source code for vulnerabilities, code smells, bugs | `docker compose -f docker-compose.sonarqube.yml up -d` then `./gradlew sonar` |

### Lab 4 — Shift Left & Security Antipatterns (45 min)
- **Modules:** [Shift Left Security](../../../../labs/security/shift-left-security.md) + [Security Antipatterns](../../../../labs/security/security-antipatterns.md)
- **Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance) or [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)
- **Objective:** Add CI workflows that gate on security policy violations, then identify and fix security antipatterns in application code
- **Target Outcomes:**
  - GitHub Actions workflow added with security scanning (Trivy, OWASP DC, or SonarQube)
  - CI fails on HIGH/CRITICAL findings
  - SBOM generated (CycloneDX or SPDX)
  - Security antipatterns identified (OWASP Top 10 review)
  - PR with CI workflow and antipattern fixes

---

## Additional Challenges

Participants who finish early may attempt any challenge from the full [module catalog](../../../../labs/). Recommended extras:

| Challenge | Module | Repo | Difficulty | Time |
|-----------|--------|------|-----------|------|
| New Feature Development | [New Feature Development](../../../../labs/application-development/new-feature-development.md) | timesheet-app | Intermediate | 60 min |
| Fix UI Bug | [Fix UI Bug](../../../../labs/application-development/fix-ui-bug.md) | timesheet-app | Intermediate | 45 min |
| CI/CD Pipeline | [CI/CD Pipeline](../../../../labs/devops-cicd/cicd-pipeline.md) | timesheet-app | Intermediate | 60 min |

---

## Repos Required on Devin's Machine

- [ ] timesheet-app
- [ ] uc-cve-remediation-regulatory-compliance
- [ ] calcom (optional, for Lab 2 alternative)

## Runtime Resources

- **timesheet-app:** Backend on port 3001, frontend on port 5173 (needed for Lab 2 E2E testing)
- **SonarQube:** `docker compose -f docker-compose.sonarqube.yml up -d` on port 9000 (needed for Lab 3)

If hosted instances are available, refer to [runtime-resources.md](../../../../shared/runtime-resources.md) for URLs and credentials.

## Repo Duplication Notes

- `uc-cve-remediation-regulatory-compliance` originates from `spring-boot-realworld-example-app` (Cluster C1 in [catalog](../../../../catalog/repos.md)). It is an isolated copy for security exercises — participants can safely modify it without affecting other labs.
- `timesheet-app` (renamed from `client-timesheet-app`) is a standalone repo used across QE and security challenges.

## Context

- **Audience:** QA/QE engineers, AppSec/DevSecOps practitioners, engineering leads
- **Flow:** The workshop progresses from quality foundations (linting, testing) to security remediation (SAST, CVE patching, shift-left CI) — showing how quality and security practices reinforce each other
- **Key narrative:** Devin handles the tedious work (running scanners, interpreting reports, generating tests) so engineers can focus on evaluating and verifying results
- **SAST tools are local:** All security scanning runs on the participant's machine via Gradle plugins and Docker — no cloud accounts or paid licenses required

## Devin Features Checklist

Encourage participants to track their progress on the [Devin Features Appendix](../../../../labs/devin-features/README.md) throughout the session. Key activities for this variant:

- [ ] Resolve a GitHub Issue (Lab 1 — linting)
- [ ] Generate unit tests and review coverage reports (Lab 1)
- [ ] Use Devin's browser for E2E testing (Lab 2)
- [ ] Record a screen recording of test execution (Lab 2)
- [ ] Run local SAST tools and interpret scan reports (Lab 3)
- [ ] Create a `SECURITY_REMEDIATION.md` with before/after evidence (Lab 3)
- [ ] Author a GitHub Actions CI workflow (Lab 4)
- [ ] Review a Devin PR via GitHub (after any lab)
- [ ] Provide feedback to steer Devin's behavior mid-session

## Post-Event

- [ ] Collect participant feedback
- [ ] Archive any event-specific branches
- [ ] Update challenge modules if issues were discovered
- [ ] Share session recordings/artifacts with participants
