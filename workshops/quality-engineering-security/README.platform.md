# Workshop: Quality Engineering & Security Vulnerability Remediation

**Other variants:** [Cloud only](README.md)

## Overview

| | |
|---|---|
| **Focus** | Quality engineering (first half) + security vulnerability remediation (second half) |
| **Duration** | ~3 hours (flexible: 90 min per half + breaks) |
| **Audience** | QA/QE engineers, AppSec/DevSecOps practitioners, engineering leads responsible for test coverage or security compliance |
| **Delivery Surface** | **Devin Desktop + Devin Cloud** — participants use Desktop as their primary interface, delegating tasks to Cloud and reviewing results in-editor |
| **Key Modules** | [Linting & Static Analysis](../../modules/testing-qa/linting-static-analysis.md), [Unit Testing](../../modules/testing-qa/unit-testing.md), [Upgrade Dependencies](../../modules/security/upgrade-dependencies.md), [Remediate Vulnerabilities](../../modules/security/remediate-vulnerabilities.md) |

## Abstract

> This workshop explores how AI-powered software engineering accelerates quality engineering and security vulnerability remediation — using **Devin Desktop** as your primary interface with **Devin Cloud** handling autonomous execution.
>
> **First half — Quality Engineering:** Use Cascade or Devin Local in Desktop to explore codebases, then delegate linting fixes, unit test generation, E2E test suites, and documentation tasks to Devin Cloud. Monitor progress in the Agent Command Center and review PRs with one-click checkout — without leaving the editor.
>
> **Second half — Security Vulnerability Remediation:** Explore SAST scan results locally, then delegate CVE remediation, dependency upgrades, and shift-left CI workflows to Cloud. Track multiple remediation sessions in parallel from the Kanban board.
>
> **Who should attend:** QA/QE engineers, AppSec and DevSecOps practitioners, engineering leads responsible for test coverage or security compliance, and anyone curious about applying AI assistants to quality and security workflows.

## Platform Context

This workshop uses **Devin Desktop + Devin Cloud**. You will use Desktop as your primary interface — exploring code locally, delegating tasks to Cloud, and reviewing results without leaving the editor.

The typical workflow for each lab:

1. **Create a Space** in Desktop for this workshop to organize sessions, PRs, and files in one view
2. **Explore with Cascade or Devin Local** — use local agents for code exploration and requirement refinement (replaces Ask Devin research steps from the Cloud-only variant)
3. **Delegate to Devin Cloud** — send implementation tasks to Cloud from Desktop with one click. Cloud works autonomously on its own VM while you continue locally
4. **Monitor in the Agent Command Center** — the Kanban board shows all your agents (local and cloud) organized by status. See at a glance what is in flight, what is blocked, and what is ready for review
5. **Review PRs in-editor** — when Devin Cloud opens a PR, check it out directly in Desktop with one click. No browser switching, no manual `git fetch`

> **Tip:** Desktop supports multiple agents via the Agent Client Protocol (ACP). Alongside Devin Local, you can run third-party agents (Codex CLI, Claude Agent, Gemini CLI, and others) in the same environment.

## Getting the Most from This Workshop

> **Devin Cloud works asynchronously on its own machine.** Once you delegate a task from Desktop, Devin Cloud runs independently — you don't need to watch it. Continue exploring code locally, start the next lab, or review a previous PR while Cloud works in the background.

A few tips to maximize your hands-on time:

- **Delegate early, review later.** Send the implementation task to Cloud first, then use the wait time for local exploration with Cascade or Devin Local — Cloud keeps working in the background.
- **Use Cascade to refine requirements.** Before delegating to Cloud, explore the codebase locally with Cascade. The better-defined a task is, the better Devin Cloud's output.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this shared context layer is available to both local and cloud agents and compounds over time.
- **Review PRs in-editor.** When Devin Cloud opens a PR, use one-click checkout in Desktop to review the diff, run tests locally, and leave comments — without switching to the browser.
- **Try parallel sessions.** Delegate multiple tasks to Cloud simultaneously and monitor them from the Agent Command Center. This mirrors real enterprise usage and demonstrates team-based operation at scale.
- **Organize with Spaces.** Create a Space for this workshop — new sessions opened within the Space inherit the project context automatically.

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
- **Modules:** [Linting & Static Analysis](../../modules/testing-qa/linting-static-analysis.md) + [Unit Testing](../../modules/testing-qa/unit-testing.md)
- **Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)
- **Objective:** Start by resolving linting issues (GitHub Issue #3), then improve unit test coverage and generate a coverage report

#### Desktop Workflow

1. **Explore locally:** Open the timesheet-app repo in Desktop and use Cascade or Devin Local to explore the linting configuration and identify existing ESLint/Prettier issues
2. **Delegate to Cloud:** Send the linting remediation task to Devin Cloud from Desktop — include the GitHub Issue reference (#3) and target coverage level in the prompt
3. **Monitor:** Track the Cloud session in the Agent Command Center while you explore the test structure locally
4. **Review:** When Devin Cloud opens the PR, use one-click checkout to review linting fixes and new tests directly in the editor

- **Target Outcomes:**
  - All ESLint/Prettier violations resolved
  - Unit test coverage increased (target: 80%+)
  - Coverage report generated
  - PR with linting fixes and new tests

### Lab 2 — E2E Testing & Documentation (45 min)
- **Modules:** [End-to-End Testing](../../modules/testing-qa/end-to-end-testing.md) + [Inline Documentation](../../modules/technical-documentation/inline-documentation.md)
- **Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)
- **Alternative E2E repo:** [calcom](https://github.com/Cognition-Partner-Workshops/calcom) (for participants wanting a more complex target)
- **Objective:** Write and run E2E tests against the locally running application, then improve inline documentation across the codebase

#### Desktop Workflow

1. **Explore locally:** Use Cascade to understand the application's UI flows and identify the critical user paths that need E2E coverage
2. **Delegate E2E tests to Cloud:** Send the E2E test generation task to Devin Cloud — Devin's VM can run the application and execute Playwright/Selenium tests with its built-in browser
3. **Delegate documentation to Cloud:** While E2E tests are running, send a separate Cloud session for inline documentation — track both in the Agent Command Center
4. **Review:** Check out each PR in Desktop as it arrives. Inspect generated test files, run them locally if desired, and review documentation changes in-editor

- **Target Outcomes:**
  - E2E test suite created (Playwright or Selenium)
  - Devin screen recording of test execution as evidence
  - JSDoc/inline documentation added to key modules
  - PR with tests and documentation

### Lab 3 — CVE Remediation with Local SAST Tools (60 min)
- **Modules:** [Upgrade Dependencies](../../modules/security/upgrade-dependencies.md) + [Remediate Vulnerabilities](../../modules/security/remediate-vulnerabilities.md)
- **Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- **Objective:** Use pre-configured local SAST tools to scan for vulnerabilities, remediate the most critical findings, and upgrade outdated dependencies

#### Desktop Workflow

1. **Explore locally:** Use Cascade to review the project's dependency tree and understand which components are most exposed. Explore the Gradle build configuration to understand the SAST tool setup
2. **Delegate remediation to Cloud:** Send remediation tasks to Devin Cloud — include specific CVE identifiers and target versions from your local exploration. Devin Cloud can run the SAST tools on its VM and generate before/after reports
3. **Monitor parallel sessions:** If remediating multiple vulnerability categories, delegate them as separate Cloud sessions and track progress on the Agent Command Center Kanban board
4. **Review:** Check out the remediation PR in Desktop. Review upgraded dependency versions, inspect the `SECURITY_REMEDIATION.md`, and verify scan reports

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
- **Modules:** [Shift Left Security](../../modules/security/shift-left-security.md) + [Security Antipatterns](../../modules/security/security-antipatterns.md)
- **Repository:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance) or [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)
- **Objective:** Add CI workflows that gate on security policy violations, then identify and fix security antipatterns in application code

#### Desktop Workflow

1. **Explore locally:** Use Cascade to review the existing CI configuration and identify gaps in security gating. Explore the codebase for common OWASP Top 10 antipatterns
2. **Delegate CI workflow to Cloud:** Send the shift-left CI task to Devin Cloud — specify the scanning tools (Trivy, OWASP DC, or SonarQube) and failure thresholds
3. **Delegate antipattern fixes to Cloud:** Send a separate Cloud session to identify and fix security antipatterns. Monitor both sessions from the Agent Command Center
4. **Review:** Check out the CI workflow PR in Desktop to inspect the GitHub Actions YAML. Verify that the pipeline fails on HIGH/CRITICAL findings by reviewing the CI output

- **Target Outcomes:**
  - GitHub Actions workflow added with security scanning (Trivy, OWASP DC, or SonarQube)
  - CI fails on HIGH/CRITICAL findings
  - SBOM generated (CycloneDX or SPDX)
  - Security antipatterns identified (OWASP Top 10 review)
  - PR with CI workflow and antipattern fixes

---

## Additional Challenges

Participants who finish early may attempt any challenge from the full [module catalog](../../modules/). Recommended extras:

| Challenge | Module | Repo | Difficulty | Time |
|-----------|--------|------|-----------|------|
| New Feature Development | [New Feature Development](../../modules/application-development/new-feature-development.md) | timesheet-app | Intermediate | 60 min |
| Fix UI Bug | [Fix UI Bug](../../modules/application-development/fix-ui-bug.md) | timesheet-app | Intermediate | 45 min |
| CI/CD Pipeline | [CI/CD Pipeline](../../modules/devops-cicd/cicd-pipeline.md) | timesheet-app | Intermediate | 60 min |

---

## Repos Required on Devin's Machine

- [ ] timesheet-app
- [ ] uc-cve-remediation-regulatory-compliance
- [ ] calcom (optional, for Lab 2 alternative)

## Runtime Resources

- **timesheet-app:** Backend on port 3001, frontend on port 5173 (needed for Lab 2 E2E testing)
- **SonarQube:** `docker compose -f docker-compose.sonarqube.yml up -d` on port 9000 (needed for Lab 3)

If hosted instances are available, refer to [runtime-resources.md](../../shared/runtime-resources.md) for URLs and credentials.

## Repo Duplication Notes

- `uc-cve-remediation-regulatory-compliance` originates from `spring-boot-realworld-example-app` (Cluster C1 in [catalog](../../catalog/repos.md)). It is an isolated copy for security exercises — participants can safely modify it without affecting other labs.
- `timesheet-app` (renamed from `client-timesheet-app`) is a standalone repo used across QE and security challenges.

## Context

- **Audience:** QA/QE engineers, AppSec/DevSecOps practitioners, engineering leads
- **Delivery surface:** Devin Desktop (primary) + Devin Cloud (autonomous execution)
- **Flow:** The workshop progresses from quality foundations (linting, testing) to security remediation (SAST, CVE patching, shift-left CI) — showing how quality and security practices reinforce each other
- **Key narrative:** Participants explore code locally with Cascade/Devin Local, delegate implementation to Devin Cloud, and review results in-editor — experiencing the full platform continuum from local exploration to cloud-scale execution
- **SAST tools are local:** Devin Cloud runs security scanners on its VM via Gradle plugins and Docker — no cloud accounts or paid licenses required

## Devin Features Checklist

Encourage participants to track their progress on the [Devin Features Appendix](../../modules/devin-features/README.md) throughout the session. Key activities for this variant:

- [ ] Use Cascade or Devin Local for code exploration (Labs 1–4)
- [ ] Delegate a task to Devin Cloud from Desktop (Labs 1–4)
- [ ] Monitor sessions in the Agent Command Center (Labs 1–4)
- [ ] Review a Devin Cloud PR with one-click checkout (after any lab)
- [ ] Create a Space for the workshop
- [ ] Run parallel Cloud sessions and track them on the Kanban board (Labs 2–3)
- [ ] Resolve a GitHub Issue via Cloud delegation (Lab 1 — linting)
- [ ] Generate unit tests and review coverage reports (Lab 1)
- [ ] Use Devin Cloud's browser for E2E testing (Lab 2)
- [ ] Record a screen recording of test execution (Lab 2)
- [ ] Run local SAST tools and interpret scan reports (Lab 3)
- [ ] Create a `SECURITY_REMEDIATION.md` with before/after evidence (Lab 3)
- [ ] Author a GitHub Actions CI workflow (Lab 4)
- [ ] Provide feedback to steer Devin's behavior mid-session

## Post-Event

- [ ] Collect participant feedback
- [ ] Archive any event-specific branches
- [ ] Update challenge modules if issues were discovered
- [ ] Share session recordings/artifacts with participants
