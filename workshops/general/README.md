# Workshop: General

## Overview

| | |
|---|---|
| **Focus** | Security triage, modernization, feature development, and testing — a broad hands-on tour of Devin's capabilities |
| **Duration** | Flexible (3 tracks, 3 labs each) |
| **Audience** | Development teams, engineering leaders, platform teams evaluating Devin across multiple use cases |
| **Tracks** | **Track A:** Security & Issue Triage · **Track B:** Modernization · **Track C:** Feature Development & Testing |

## Workshop Narrative

This workshop covers the three most common categories of engineering work: keeping systems secure and stable, modernizing legacy technology, and building new features with quality. Each track is self-contained — participants can follow all three in sequence for a full-day experience, or focus on one or two tracks in a shorter session.

The tracks are designed to show Devin working across different problem types:
- **Track A** shows Devin as a security and reliability agent — scanning, remediating, investigating, and automating ongoing hygiene
- **Track B** shows Devin handling large-scale structural changes — rearchitecting, upgrading, and translating entire codebases
- **Track C** shows Devin as a day-to-day development partner — building features, catching bugs through PR Review, adding test coverage, and fixing E2E failures

## Getting the Most from This Workshop

> **Devin works autonomously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

A few tips to maximize your hands-on time:

- **Start sessions early, review later.** Each lab has a "Paste into Devin" step and a separate "Review & Give Feedback" step. Kick off the session first, then use the wait time for Ask Devin research or reading DeepWiki — Devin will keep working in the background.
- **Try parallel sessions.** Several labs suggest running multiple Devin sessions at once. This mirrors real enterprise usage where Devin handles repetitive work across many services.
- **Use Ask Devin to refine requirements before creating a session.** The better-defined a task is, the better Devin's output. Ask Devin helps you think through the problem first so Devin can execute with less back-and-forth.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item during a session, accept it — this is how teams build a shared context layer that makes Devin smarter over time. You can also create Knowledge manually for project conventions and standards.
- **Leave PR comments to steer Devin.** After Devin opens a PR, the PR Review agent and CI checks provide automatic feedback loops. You can also leave comments directly on the PR and Devin will wake up and address them — this is the core workflow for iterating with Devin in production.

### Quick Start (experienced attendees)

Already comfortable with Devin basics? Jump straight to the labs:

1. Pick a track that matches your interest: [A (Security)](#track-a-security--issue-triage), [B (Modernization)](#track-b-modernization), or [C (Feature Dev)](#track-c-feature-development--testing)
2. Copy the prompt from Step 1 of any lab and paste it into a new Devin session
3. While Devin works, try the Ask Devin prompts in Step 2 to explore the codebase
4. Review the PR when Devin finishes, leave comments, and iterate

---

## Table of Contents

- [Track A: Security & Issue Triage](#track-a-security--issue-triage)
  - [Lab A1 — SAST Scans & Vulnerability Remediation](#lab-a1--sast-scans--vulnerability-remediation)
  - [Lab A2 — Bug Fixes & Root Cause Analysis](#lab-a2--bug-fixes--root-cause-analysis)
  - [Lab A3 — Scheduled Dependency Hygiene](#lab-a3--scheduled-dependency-hygiene)
- [Track B: Modernization](#track-b-modernization)
  - [Lab B1 — Rearchitecting Monolith to Microservice](#lab-b1--rearchitecting-monolith-to-microservice)
  - [Lab B2 — Upgrading EOL Systems to LTS Versions](#lab-b2--upgrading-eol-systems-to-lts-versions)
  - [Lab B3 — Language Translation](#lab-b3--language-translation)
- [Track C: Feature Development & Testing](#track-c-feature-development--testing)
  - [Lab C1 — Add a Feature + PR Review Feedback](#lab-c1--add-a-feature--pr-review-feedback)
  - [Lab C2 — Add Test Coverage](#lab-c2--add-test-coverage)
  - [Lab C3 — Perform E2E Tests & Fix Issues](#lab-c3--perform-e2e-tests--fix-issues)
- [Additional Challenges](#additional-challenges)
- [Suggested Formats](#suggested-formats)
- [Repos Required](#repos-required)
- [Context](#context)
- [Devin Features Checklist](#devin-features-checklist)

---

## Track A: Security & Issue Triage

Track A demonstrates Devin as a security and reliability agent. Participants will run SAST scans and remediate findings, investigate and fix bugs with root cause analysis, and set up automated scheduled sessions for ongoing dependency hygiene.

### Lab A1 — SAST Scans & Vulnerability Remediation

- **Modules:** [Remediate Vulnerabilities](../../modules/security/remediate-vulnerabilities.md) + [Shift Left Security](../../modules/security/shift-left-security.md)
- **Repositories:**
  - [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance) — Spring Boot 2.6.3 with known CVEs and pre-configured OWASP Dependency-Check
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — Node.js app with npm audit and Trivy scanning (alternative)
- **Objective:** Run SAST tools to identify vulnerabilities, remediate the most critical findings, and add security scanning to the CI pipeline so future PRs are automatically checked

This lab has two parts: (1) find and fix existing vulnerabilities, and (2) shift left by adding security scanning to CI so new vulnerabilities are caught automatically.

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

Run these as **parallel sessions** — one to fix existing vulnerabilities, one to add CI scanning:

**Session A — Scan & Remediate:**
```
Run `./gradlew dependencyCheckAnalyze` on uc-cve-remediation-regulatory-compliance to identify dependency CVEs. Remediate the top 5 most critical findings (CVSS >= 7.0) — start with Spring Boot 2.6.3, SnakeYAML 1.29, and sqlite-jdbc 3.36.0.3. Re-run the scan to verify the fixes. Create a `SECURITY_REMEDIATION.md` documenting the before/after results.
```

**Session B — Shift Left (CI Pipeline):**
```
Create a GitHub Actions CI pipeline for uc-cve-remediation-regulatory-compliance that: builds with Gradle, runs `./gradlew dependencyCheckAnalyze`, fails the PR if any dependency has CVSS >= 7.0, generates an SBOM in CycloneDX format, and uploads the dependency check report as a build artifact.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What are the known CVEs in uc-cve-remediation-regulatory-compliance's dependencies? Which ones are CRITICAL severity?"*
- *"What's the safest upgrade path for Spring Boot 2.6.3 — should we go to 2.7.x first or jump straight to 3.x?"*
- *"What SAST tools are available for Spring Boot applications? Compare Trivy, Snyk, and OWASP Dependency-Check."*
- Use the analysis to start a **second session** — try adding SonarQube scanning (the repo has a pre-configured `docker-compose.sonarqube.yml`) or pre-commit hooks for secrets detection

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the codebase architecture and dependency tree. Use what you learn to try different approaches:
- Have Devin add **SBOM generation** (CycloneDX) to the build
- Ask Devin to add **pre-commit hooks** for secrets detection (gitleaks)
- Try adding **SonarQube scanning** — the repo has a pre-configured `docker-compose.sonarqube.yml`
- Ask Devin to implement an **event-driven SAST pipeline** where CI scans trigger Devin to auto-remediate findings on the branch

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR from step 1, focus your review on the **remediation completeness**:
- **Scan results:** Did Devin address both CRITICAL and HIGH findings? Are there any it missed?
- **CI workflow:** Does the pipeline correctly fail on high-severity CVEs? Is the SBOM generated?
- **Verification:** Did Devin re-run the scan to prove the fixes work?

**Leave a feedback comment** and watch Devin respond:
- *"The SnakeYAML version still has CVE-2022-1471 — please upgrade to 2.x"*
- *"Add a CI workflow that fails the build on CRITICAL CVEs"*
- *"Generate a pre-commit hook configuration for gitleaks secrets detection"*

See the full challenge details for [Remediate Vulnerabilities](../../modules/security/remediate-vulnerabilities.md) and [Shift Left Security](../../modules/security/shift-left-security.md) for more ideas.

- **Key Takeaways:**
  - **"Scan → fix → re-scan"** — Devin runs local SAST tools, interprets CVE reports, remediates findings, and verifies the fix in a closed loop
  - **"Shift left"** — adding security scanning to CI catches new vulnerabilities before they reach production
  - **"Evidence-based compliance"** — SBOM, scan reports, and remediation documentation provide audit trails

- **Target Outcomes (any of these count):**
  - OWASP Dependency-Check report with critical CVEs remediated
  - `SECURITY_REMEDIATION.md` with before/after evidence
  - GitHub Actions CI workflow that fails on high-severity CVEs
  - SBOM generated in CycloneDX format
  - PR(s) with review comments and Devin's responses

---

### Lab A2 — Bug Fixes & Root Cause Analysis

- **Modules:** [Fix Runtime Bug](../../modules/application-development/fix-runtime-bug.md) + [Cross-Service Bug Investigation](../../modules/migration-modernization/cross-service-bug-investigation.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js full-stack application
  - [quickapp-microservices](https://github.com/Cognition-Partner-Workshops/quickapp-microservices) — decomposed .NET microservices with a planted cross-service bug (alternative)
- **Objective:** Find and fix bugs in running applications, perform root cause analysis, and demonstrate Devin's ability to trace issues across service boundaries

This lab shows two dimensions of bug investigation: (1) exploratory testing where Devin discovers and fixes bugs in a running app, and (2) cross-service debugging where a symptom in one service has its root cause in another.

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

Choose one or both:

**Option A — Exploratory Bug Hunting (timesheet-app):**
```
Start timesheet-app locally (backend: `cd backend && npm run dev`, frontend: `cd frontend && npm run dev`). Explore the application — create work entries, manage clients, try the reporting features. Find and document any bugs or unexpected behavior. Fix the most impactful bug you find. Take before/after screenshots. Write a `ROOT_CAUSE_ANALYSIS.md` explaining the bug, why it happened, and how you fixed it.
```

**Option B — Cross-Service Bug Investigation (.NET microservices):**
```
Order confirmation notification emails are showing wrong amounts after the microservice decomposition. A $149.99 order shows as $1.50 in the email preview. Investigate and fix this bug in quickapp-microservices. To reproduce: run the notification-service locally, POST to `http://localhost:5005/api/notification/events/order-placed` with `{"orderId": "11111111-1111-1111-1111-111111111111", "customerId": "22222222-2222-2222-2222-222222222222", "totalAmount": 149.99, "placedAt": "2026-03-17T12:00:00Z"}`, then open the preview URL — the total shows $1.50 instead of $149.99. Find the root cause, fix it, take before/after screenshots, and open a PR with your fix and root cause analysis.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What are the most common types of bugs in Express + React applications? What should I look for?"*
- *"Trace the data flow from when an OrderPlacedEvent is received to when the notification email is rendered. Where does the monetary amount get transformed?"*
- *"What does the OrderPlacedEvent.TotalAmount field represent — dollars or cents? Check the shared contract definition."*
- Use the analysis to refine your bug report or start a **second session** targeting a different area of the application

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the application architecture:
- **timesheet-app** — Understand the data flow and identify components that might have bugs (complex state management, API error handling, date/time logic)
- **quickapp-microservices** — Understand the event flow from Order service to Notification service, the `Shared.Contracts` library, and the `NotificationRenderer`

Try different approaches:
- Have Devin add a **regression test** for any bug it finds
- Ask Devin to check for **similar bugs** elsewhere in the codebase
- Try having Devin produce a **debugging narrative** that traces the issue from symptom to root cause

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR from step 1, focus your review on the **root cause analysis**:
- **Root cause:** Does the analysis explain *why* the bug happened, not just *what* was changed?
- **Fix quality:** Does the fix address the root cause or just the symptom?
- **Regression prevention:** Is there a test that will catch this bug if it's reintroduced?

**Leave a feedback comment** and watch Devin respond:
- *"Add a regression test for this bug"*
- *"Are there any other places in the codebase that make the same assumption?"*
- *"Add a unit test for FormatCurrency that verifies $149.99 renders as '$149.99' and not '$1.50'"*

See the full challenge details for [Fix Runtime Bug](../../modules/application-development/fix-runtime-bug.md) and [Cross-Service Bug Investigation](../../modules/migration-modernization/cross-service-bug-investigation.md) for more ideas.

- **Key Takeaways:**
  - **"Devin debugs like a developer"** — it reproduces bugs via the browser, traces data flows, reads logs, and identifies root causes
  - **"Cross-service tracing"** — Devin follows data across service boundaries and shared contracts to find root causes that live outside the symptom's service
  - **"Don't trust comments"** — the cross-service bug includes a misleading code comment. Devin learns to verify against the actual data contract
  - **"Screen recordings as evidence"** — Devin's before/after screenshots and recordings provide visual proof that the fix works

- **Target Outcomes (any of these count):**
  - Bug identified with reproduction steps
  - Root cause analysis documented
  - Fix implemented with before/after evidence (screenshots or screen recording)
  - Regression test added
  - PR with fix explanation and Devin's responses to review comments

---

### Lab A3 — Scheduled Dependency Hygiene

- **Modules:** [Upgrade Dependencies](../../modules/security/upgrade-dependencies.md)
- **Repositories:**
  - [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance) — Spring Boot app with Gradle
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — Node.js app with npm (alternative)
- **Objective:** Set up a recurring Devin scheduled session that automatically upgrades dependencies to the latest minor/patch versions on a weekly cadence — demonstrating Devin as an always-on maintenance agent

This lab introduces **Devin Scheduled Sessions** — recurring automated tasks that run without human intervention. Dependency version bumps are a perfect use case: low-risk, high-volume, and easy to verify via CI.

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

```
Check all dependencies in uc-cve-remediation-regulatory-compliance for available minor and patch version updates. Upgrade each dependency to the latest minor version (do not jump major versions). Run `./gradlew build` and `./gradlew test` to verify the build still passes after each upgrade. If any upgrade breaks the build, revert that specific upgrade and document it.md` listing what was upgraded, from which version to which version, and any upgrades that were skipped (with reasons). Title the PR "chore: weekly dependency version bump".
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What dependencies in uc-cve-remediation-regulatory-compliance are the most out of date? Which ones have the most available minor/patch updates?"*
- *"What's the difference between minor and patch version upgrades in terms of risk? When is it safe to auto-upgrade vs. requiring human review?"*
- *"How should a team handle dependency upgrades that break the build — auto-revert and flag, or block and notify?"*
- Consider creating a **Devin Knowledge item** capturing the dependency upgrade policy (e.g., "always upgrade patch versions, upgrade minor versions if tests pass, never auto-upgrade major versions")

#### Step 3 (Optional): Set Up a Scheduled Session

Once you're happy with the output from step 1, turn it into a recurring task. Open a new Devin session and ask it to create a schedule:

```
Create a Devin scheduled session that runs weekly on Monday mornings against uc-cve-remediation-regulatory-compliance. The schedule should use this prompt: "Check all dependencies for available minor and patch version updates. Upgrade to the latest minor versions. Run the full test suite and build to verify nothing is broken. If any upgrade breaks the build, revert that specific upgrade and note it.md."
```

This way Devin will automatically open a dependency bump PR every week without human intervention.

#### Step 4 (Optional): Extend to Multiple Repos

Try running the same pattern for **timesheet-app** with an npm-flavored prompt:

```
Check all npm dependencies in timesheet-app for available minor and patch version updates. Run `npm update` to upgrade to latest minor versions. Run `npm test` and `npm run build` to verify everything still works.md`.
```

This demonstrates how the same maintenance pattern scales across different tech stacks and repositories — each scheduled session runs independently on its own VM.

- **Key Takeaways:**
  - **"Automate the boring stuff"** — dependency upgrades are repetitive, low-risk, and high-volume. Devin handles them weekly without human intervention
  - **"Scheduled sessions = always-on maintenance"** — teams set the policy once, and Devin executes it on a cadence. No more "we'll get to it next sprint"
  - **"Safe by default"** — the prompt instructs Devin to verify builds, revert breaking upgrades, and document everything. The PR still requires human approval to merge
  - **"Scales across repos and tech stacks"** — the same pattern works for Gradle, npm, pip, cargo, etc. Set it up once per repo and forget about it

- **Target Outcomes (any of these count):**
  - Devin scheduled session configured for weekly dependency upgrades
  - PR with dependency upgrades and `DEPENDENCY_UPDATES.md`
  - Knowledge item capturing the team's dependency upgrade policy
  - Same schedule replicated across multiple repos/tech stacks

---

## Track B: Modernization

Track B demonstrates Devin handling large-scale structural changes to codebases. Participants will extract microservices from a monolith, upgrade end-of-life frameworks to LTS versions, and translate legacy code to a modern language.

### Lab B1 — Rearchitecting Monolith to Microservice

- **Module:** [Containerization & Microservice Extraction](../../modules/migration-modernization/containerization-microservice-extraction.md)
- **Repositories:**
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot 2.6.3 monolith with three clear bounded contexts (Articles, Users/Profiles, Comments)
  - [petclinic-microservices](https://github.com/Cognition-Partner-Workshops/petclinic-microservices) — Reference microservices architecture for comparison (optional)
- **Objective:** Analyze domain boundaries in a monolith, extract a bounded context as a standalone microservice with its own API, Dockerfile, and database, and wire the services together with Docker Compose

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

```
Analyze the domain boundaries in uc-spring-boot-upgrade-microservice-extraction. This Spring Boot monolith has three bounded contexts: Articles (CRUD, feed, favorites, tags), Users/Profiles (registration, authentication, following), and Comments (CRUD linked to articles).

Extract the Comments domain into a standalone Spring Boot microservice with its own database, Dockerfile, and REST API. The monolith should communicate with the comments microservice via HTTP. Create a docker-compose.yml that runs both services. Add integration tests that verify the monolith and microservice communicate correctly.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What are the domain boundaries in uc-spring-boot-upgrade-microservice-extraction? Which bounded context would be easiest to extract and which would be hardest?"*
- *"If I extract the Articles domain from this monolith, what shared code and database tables will need to be handled? What communication pattern should I use?"*
- *"What integration tests are needed when extracting a microservice from a monolith? How do we test the HTTP communication between the two services?"*
- Use the refined analysis to start a **second session** — try extracting a different bounded context (Articles is harder than Comments) and compare Devin's approaches

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the module structure, domain model, and dependency graph:
1. Identify which bounded contexts exist and how tightly coupled they are
2. Look at the test coverage — which domains have the best tests to serve as a safety net during extraction?
3. Compare with **petclinic-microservices** to see what a fully decomposed architecture looks like

Try different approaches:
- Extract **two bounded contexts in parallel** using separate Devin sessions
- Ask Devin to produce a **domain decomposition document** before doing any code changes
- Have Devin add **Kubernetes manifests** on top of the Docker setup

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR from step 1, focus your review on the **extraction quality**:
- **Clean boundaries:** Are there leftover dependencies on the monolith? Is the microservice truly standalone?
- **Communication:** Does the HTTP communication between services work correctly? Are there proper error handling and retry patterns?
- **Containerization:** Does the Dockerfile use a multi-stage build? Does docker-compose handle startup order?

**Leave a feedback comment** and watch Devin respond:
- *"The Dockerfile should use a multi-stage build to reduce image size"*
- *"Add health check endpoints to both services"*
- *"The integration test should verify the full request-response cycle, not just connectivity"*

See the full challenge details for [Containerization & Microservice Extraction](../../modules/migration-modernization/containerization-microservice-extraction.md) for more ideas.

- **Key Takeaways:**
  - **"Devin analyzes domain boundaries"** — it reads the codebase to identify bounded contexts, shared code, and coupling points before extracting
  - **"Extraction is more than copy-paste"** — the microservice needs its own database, API contract, Dockerfile, and inter-service communication
  - **"Docker Compose proves it works"** — running both services together with integration tests validates the decomposition end-to-end
  - **"Parallel extraction scales"** — multiple bounded contexts can be extracted simultaneously in separate Devin sessions

- **Target Outcomes (any of these count):**
  - One bounded context extracted as a standalone Spring Boot service
  - Dockerfile with multi-stage build for the extracted service
  - Docker Compose configuration running both services
  - Integration tests verifying inter-service communication
  - PR with extraction documentation and Devin's responses to review comments

---

### Lab B2 — Upgrading EOL Systems to LTS Versions

- **Modules:** [Framework Upgrade](../../modules/migration-modernization/framework-upgrade.md) + [Repetitive Framework Upgrades](../../modules/migration-modernization/repetitive-framework-upgrades.md)
- **Repositories:**
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot 2.6.3 / Java 11 (EOL) → Spring Boot 3.x / Java 17+ (LTS)
  - [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular) — Angular version upgrade
  - [ts-angular-realworld](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld) — Angular version upgrade (second repo for parallel comparison)
- **Objective:** Run parallel Devin sessions upgrading frameworks and language versions across multiple repos — demonstrating how Devin handles repetitive upgrade tasks at enterprise scale

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

Run these as **parallel sessions** to see how the same upgrade pattern scales across repos:

**Session A — Spring Boot + Java Upgrade:**
```
Upgrade uc-spring-boot-upgrade-microservice-extraction from Java 11 + Spring Boot 2.6.3 to Java 17 + Spring Boot 3.2. Handle the javax to jakarta namespace migration, update Gradle build configuration, fix any deprecations, and ensure all tests pass. Document every breaking change and how you resolved it in the PR description.
```

**Session B — Angular Upgrade (PetClinic):**
```
Upgrade petclinic-angular to the latest Angular version. Handle any breaking changes from the Angular update guide, update all dependencies, fix deprecated APIs, and ensure the app builds successfully. Document every breaking change encountered.
```

**Session C — Angular Upgrade (RealWorld, optional for comparison):**
```
Upgrade ts-angular-realworld to the latest Angular version. Handle any breaking changes, update dependencies, fix deprecated APIs, and ensure the app builds and tests pass.
```

#### Step 2: Research with Ask Devin

While Devin works on the upgrades, open **AskDevin** and explore:
- *"What are the biggest risks when upgrading from Spring Boot 2 to 3? Which javax to jakarta changes are most likely to break?"*
- *"What Angular version is petclinic-angular currently on? What are the breaking changes between that version and the latest?"*
- *"Compare the Angular upgrade paths for petclinic-angular and ts-angular-realworld — are the same breaking changes expected?"*
- Use the analysis to plan a **repeatable upgrade runbook** that could be applied across dozens of similar services
- Once you have a runbook you like, consider turning it into a **Devin Playbook** — a reusable set of instructions that any team member can trigger for future upgrades without re-engineering the prompt

#### Step 3 (Optional): Read the DeepWiki

Open each repo's **DeepWiki** page to understand the codebase before the upgrade:
1. **uc-spring-boot-upgrade-microservice-extraction** — Understand the build configuration, Spring Security setup, and dependency graph. These are the areas most affected by the Spring Boot 2 to 3 migration.
2. **petclinic-angular** — Understand the component hierarchy and module structure. Identify deprecated Angular patterns (NgModules vs. standalone components).
3. **ts-angular-realworld** — Compare with the PetClinic Angular app. Different codebases may hit different breaking changes for the same upgrade.

Try different approaches:
- Run both Angular upgrades in **parallel** and compare the upgrade PRs side-by-side
- Ask Devin to generate a **shared upgrade checklist** from both Angular upgrade experiences
- Have Devin create a **repeatable upgrade runbook** — then save it as a **Playbook** so any team member can reuse it
- Think about scheduling: framework upgrades are a great candidate for **Devin Scheduled Sessions** — e.g., run dependency version bumps weekly to catch issues early

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens PRs from the parallel sessions, compare the upgrade approaches:
- **Spring Boot PR:** Is the javax to jakarta migration complete? Does the build pass with all tests green?
- **Angular PRs:** Did both upgrades follow the Angular update guide? Are deprecated patterns fully removed?
- **Cross-PR comparison:** Did Devin encounter the same issues in both Angular repos? Were they resolved consistently?

**Leave a feedback comment** and watch Devin respond:
- *"This still uses javax.servlet — please update to jakarta.servlet"*
- *"Can you also migrate from NgModules to standalone components?"*
- *"Generate an upgrade report documenting all breaking changes encountered and how they were resolved"*

See the full challenge details for [Framework Upgrade](../../modules/migration-modernization/framework-upgrade.md) and [Repetitive Framework Upgrades](../../modules/migration-modernization/repetitive-framework-upgrades.md) for more ideas.

- **Key Takeaways:**
  - **"Same prompt, multiple repos"** — the same upgrade task applied consistently across different services demonstrates enterprise scale
  - **"Parallel sessions save calendar time"** — upgrades that would take weeks sequentially can run simultaneously, each on its own VM with no interference
  - **"Consistency across upgrades"** — Devin applies the same patterns and catches the same breaking changes across repos
  - **"Playbooks turn one-off work into repeatable processes"** — capture the upgrade runbook as a Playbook so the next round of upgrades is a one-click operation for any team member

- **Target Outcomes (any of these count):**
  - Spring Boot app builds and tests on Java 17+ / Spring Boot 3.x
  - Angular app(s) upgraded to latest version with build passing
  - Upgrade documentation listing all breaking changes and resolutions
  - Side-by-side comparison of upgrade PRs across repos
  - Playbook capturing the repeatable upgrade runbook
  - PR(s) with review comments and Devin's responses

---

### Lab B3 — Language Translation

- **Module:** [Language Translation](../../modules/migration-modernization/legacy-modernization-combined.md)
- **Repositories:**
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot (Java) RealWorld app — source language
  - [ts-angular-realworld](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld) — Angular (TypeScript) RealWorld app — reference for alternative target
- **Objective:** Translate a Java Spring Boot service layer into an equivalent Python (Flask/FastAPI) application, preserving API contracts and proving functional equivalence with parity tests. Both source and target compile and run on Ubuntu.

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

```
Translate the Articles API from uc-spring-boot-upgrade-microservice-extraction (Java/Spring Boot) into a Python FastAPI application. The Python version should expose the same REST endpoints: GET /api/articles, GET /api/articles/:slug, POST /api/articles, PUT /api/articles/:slug, DELETE /api/articles/:slug, and GET /api/articles/feed. Use SQLAlchemy for persistence and Pydantic for request/response models. Preserve the same JSON response shape so the API is a drop-in replacement. Write pytest tests that verify the Python endpoints return identical responses to the Java version for the same inputs. Document the translation decisions in a `MIGRATION_NOTES.md`.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What are the main API endpoints in uc-spring-boot-upgrade-microservice-extraction? What does each one do and what are the request/response shapes?"*
- *"What's the best Python web framework for translating a Spring Boot REST API — Flask, FastAPI, or Django REST Framework? Compare trade-offs."*
- *"What are the riskiest Java-to-Python translation patterns — type safety, null handling, dependency injection, ORM differences?"*
- Use the analysis to start a **second session** — try translating the same service to **TypeScript (Express/NestJS)** in parallel and compare the results

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the Java application architecture before translating:
1. Understand the layered architecture: API controllers → Application services → Domain entities → Repositories
2. Identify which service layer has the most complex business logic
3. Map Java patterns (Spring DI, JPA repositories, Bean Validation) to Python equivalents (FastAPI dependency injection, SQLAlchemy, Pydantic)

| Java Component | Python Equivalent | Notes |
|---------------|-------------------|-------|
| `@RestController` | FastAPI router | Route decorators |
| `@Service` | Plain class or FastAPI dependency | Business logic layer |
| JPA Repository | SQLAlchemy session | ORM queries |
| Bean Validation (`@Valid`) | Pydantic models | Request validation |
| `@Transactional` | SQLAlchemy session context manager | Transaction management |
| MyBatis XML mappers | SQLAlchemy Core queries | Complex queries |

Try different approaches:
- Translate **multiple API domains in parallel** (Articles, Users, Comments) using separate Devin sessions
- Ask Devin to produce a **translation mapping document** before writing any code
- Try targeting **two different Python frameworks** (FastAPI vs. Flask) and compare the output
- Have Devin create a **contract test suite** that hits both the Java and Python servers and asserts identical responses

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR from step 1, focus your review on the **translation fidelity**:
- **API contract:** Does the Python version return the exact same JSON shape as the Java version?
- **Business logic:** Are edge cases handled the same way (e.g., slug generation, pagination, authentication)?
- **Parity tests:** Do the pytest tests verify identical behavior for the same inputs?

**Leave a feedback comment** and watch Devin respond:
- *"The slug generation logic doesn't match — Java uses `String.toLowerCase().replaceAll()` but Python is using a different regex"*
- *"Add a contract test that starts both servers and compares responses for the same request"*
- *"The error responses don't match — Java returns `{errors: {body: [...]}}` but Python returns a flat string"*

See the full challenge details for [Language Translation](../../modules/migration-modernization/legacy-modernization-combined.md) for more ideas.

- **Key Takeaways:**
  - **"Same API, different language"** — Devin translates the business logic while preserving the API contract, so the new service is a drop-in replacement
  - **"Parity tests prove correctness"** — the Python version must return identical responses to the Java version for the same inputs. Tests are the proof.
  - **"Both compile on Ubuntu"** — unlike mainframe languages, both Java and Python run natively on Devin's machine, so Devin can build, run, and test both versions end-to-end
  - **"Migration notes capture decisions"** — the `MIGRATION_NOTES.md` documents how Java patterns map to Python equivalents and any trade-offs made

- **Target Outcomes (any of these count):**
  - Python (FastAPI) application implementing the same REST API as the Java source
  - pytest parity tests proving functional equivalence
  - `MIGRATION_NOTES.md` documenting translation decisions and pattern mappings
  - Both source and target building and running successfully on Ubuntu
  - PR with Python code, tests, and Devin's responses to review comments

---

## Track C: Feature Development & Testing

Track C demonstrates Devin as a day-to-day development partner. Participants will build a new feature, see PR Review automatically flag potential issues, add test coverage, and run E2E tests to find and fix issues.

### Lab C1 — Add a Feature + PR Review Feedback

- **Module:** [New Feature Development](../../modules/application-development/new-feature-development.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js full-stack application
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot RealWorld app (alternative)
- **Objective:** Build a new feature on an existing application, then observe how PR Review automatically flags potential bugs and issues in the implementation — and have Devin address the feedback

#### Step 1: Start with Ask Devin (recommended)

Before creating a session, try using **Ask Devin** to scope the feature. The more specific your requirements, the better Devin's output — and Ask Devin helps you think through the details before Devin starts writing code.

For example, ask: *"What existing patterns does timesheet-app use for CRUD features? What data model, API structure, and React component conventions should a new 'Projects' feature follow?"*

Then use what you learn to refine one of these prompts before pasting it into a Devin session:

**Option A — Full-Stack CRUD Feature (timesheet-app):**
```
Add a "Projects" management feature to timesheet-app. Users should be able to create, view, edit, and delete projects. Each project has a name, description, client assignment, start date, and status (active/completed/on-hold). Add both the backend API endpoints and the frontend UI page. Follow the existing patterns in the codebase for the data model, API structure, and React components. Write tests for the backend endpoints.
```

**Option B — API Feature (Spring Boot RealWorld app):**
```
Add an "article statistics" feature to uc-spring-boot-upgrade-microservice-extraction. Create a new endpoint GET /api/articles/:slug/stats that returns: view count, favorite count, comment count, and days since published. Add a GET /api/stats/trending endpoint that returns the top 10 most-favorited articles in the last 7 days. Write tests for both endpoints.
```

#### Step 2 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the existing feature patterns. Use what you learn to try different approaches:
- Have Devin add **validation rules** for the new feature (e.g., project dates must not overlap)
- Ask Devin to add **frontend tests** (React Testing Library) for the new UI components
- Try having Devin add **audit logging** for all CRUD operations on the new feature
- Ask Devin to generate **API documentation** (Swagger/OpenAPI) for the new endpoints

#### Step 3: Review PR & Observe PR Review Agent

Once Devin opens a PR from step 1, this is where the **PR Review feedback loop** comes in:
- **PR Review will automatically analyze the PR** and flag potential issues — missing validation, error handling gaps, security concerns, or logic bugs
- **Read the PR Review comments carefully** — they often catch real issues that would otherwise make it to production
- **Leave your own feedback** alongside the PR Review comments and watch Devin address both:
  - *"PR Review flagged that the project name field has no length validation — please add max length checking"*
  - *"Add error handling for the case where a client is deleted while it has active projects"*
  - *"The React component doesn't handle loading states — add a spinner while the API call is in flight"*

This demonstrates the production workflow: Devin writes code, PR Review catches issues, Devin fixes them, you approve.

See the full challenge details for [New Feature Development](../../modules/application-development/new-feature-development.md) for more ideas.

- **Key Takeaways:**
  - **"Devin follows existing patterns"** — it analyzes the codebase's conventions before implementing, producing code that fits the existing architecture
  - **"PR Review catches what humans miss"** — the automated review agent flags validation gaps, error handling issues, and potential bugs before you even look at the PR
  - **"The feedback loop is the workflow"** — Devin writes, PR Review flags, you comment, Devin fixes. This is how teams use Devin in production every day
  - **"Knowledge compounds over time"** — if Devin discovers project conventions during this session, save them as Knowledge items. Future sessions will automatically benefit

- **Target Outcomes (any of these count):**
  - New feature implemented following existing code conventions
  - Unit tests and/or integration tests for the new feature
  - Database schema changes with migration scripts
  - Frontend UI components for the new feature
  - PR Review comments identifying potential issues
  - Devin's responses to both PR Review and human feedback
  - PR with review iterations showing the feedback loop in action

---

### Lab C2 — Add Test Coverage

- **Modules:** [Unit Testing](../../modules/testing-qa/unit-testing.md) + [BDD Test Generation](../../modules/testing-qa/bdd-test-generation.md)
- **Repositories:**
  - [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) — Spring Boot app with existing JUnit infrastructure
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js app with Jest tests (alternative)
  - [uc-bdd-test-generation-cucumber](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-cucumber) — Spring Boot + Cucumber BDD framework (alternative)
- **Objective:** Analyze existing test coverage, generate meaningful tests for under-tested modules, and optionally create BDD test scenarios for REST APIs

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

Choose one or run multiple in parallel:

**Option A — Unit Test Coverage (Spring Boot):**
```
Analyze the current test coverage of uc-spring-boot-upgrade-microservice-extraction. Identify the modules with the lowest test coverage. Write JUnit tests for the top 5 least-tested modules, following the existing test patterns and framework. Aim for at least 80% line coverage on each module. Generate a JaCoCo coverage report.
```

**Option B — Unit Test Coverage (Node.js):**
```
Analyze the current test coverage of timesheet-app. Add missing Jest unit tests for the backend API routes and service layer. Generate a coverage report and fix any failing tests. Aim for at least 80% coverage on each tested module.
```

**Option C — BDD Test Generation (Cucumber):**
```
Review the uc-bdd-test-generation-cucumber codebase. This is a Spring Boot + Cucumber BDD framework with pre-built step definitions for REST API testing. Run `mvn test` to see the existing scenarios pass. Then add new Gherkin feature files that test edge cases for the existing Users API: creating a user with missing required fields (expect 400), creating a user with duplicate ID (expect 409 or appropriate error), pagination and sorting, and input validation. Also create a new `OrderController` with endpoints for managing orders and write corresponding Gherkin feature files.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What is the current test coverage breakdown by package? Which domain (articles, users, comments) has the weakest coverage?"*
- *"What testing patterns does the codebase use? JUnit with Mockito? TestContainers? What conventions should new tests follow?"*
- *"What Cucumber best practices should be followed for REST API testing — should scenarios be independent or can they share state?"*
- Use the analysis to start a **second session** — try generating property-based tests, mutation tests, or data-driven Cucumber Scenario Outlines

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand which service methods handle the most complex logic — these should be the priority for new tests. Use what you learn to try different approaches:
- Have Devin generate **data-driven scenarios** using Cucumber Scenario Outlines with Examples tables
- Ask Devin to add **negative test scenarios** for authentication failures and rate limiting
- Try having Devin add **mutation tests** to verify that the existing tests actually catch bugs
- Ask Devin to produce a **test coverage matrix** mapping tests to business requirements

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR from step 1, focus your review on **test quality**:
- **Meaningful assertions:** Are the tests checking behavior, or just padding coverage with trivial assertions?
- **Edge cases:** Do the tests cover error cases, boundary conditions, and null/empty inputs?
- **Independence:** Are tests isolated, or do they depend on each other or on database state?
- **Readability:** Are the Gherkin scenarios readable by non-developers? Do they describe business behavior rather than implementation details?

**Leave a feedback comment** and watch Devin respond:
- *"Add edge case tests for invalid date ranges and duplicate entries"*
- *"The step definitions should use more descriptive method names"*
- *"Add data-driven scenarios using Cucumber Scenario Outlines with Examples tables"*

See the full challenge details for [Unit Testing](../../modules/testing-qa/unit-testing.md) and [BDD Test Generation](../../modules/testing-qa/bdd-test-generation.md) for more ideas.

- **Key Takeaways:**
  - **"Devin writes meaningful tests"** — not just coverage padding. It analyzes the codebase to understand what needs testing and generates tests that check real behavior
  - **"Test generation from patterns, not templates"** — Devin reads the existing test framework and API patterns to generate tests that fit the project's conventions
  - **"BDD tests bridge dev and business"** — Gherkin scenarios are readable by non-developers, making them a communication tool as well as a testing tool
  - **"Quality engineering at scale"** — test generation is a great candidate for scheduled Devin sessions: as new code is added, schedule periodic test generation runs to maintain coverage

- **Target Outcomes (any of these count):**
  - Test coverage increased with meaningful test cases
  - JaCoCo or Istanbul coverage report generated
  - Gherkin feature files for existing or new API endpoints
  - Data-driven scenarios using Scenario Outlines
  - PR with test files and coverage evidence

---

### Lab C3 — Perform E2E Tests & Fix Issues

- **Module:** [End-to-End Testing](../../modules/testing-qa/end-to-end-testing.md) + [Fix Runtime Bug](../../modules/application-development/fix-runtime-bug.md)
- **Repositories:**
  - [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React + Node.js full-stack application
  - [uc-bdd-test-generation-cucumber](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-cucumber) — Spring Boot + Cucumber BDD framework (alternative)
- **Objective:** Write and run E2E tests against a running application, discover issues through testing, and fix them — demonstrating the full test-discover-fix cycle

This lab completes the testing story: after adding unit tests (Lab C2), now run the application end-to-end, write Playwright tests that exercise the full user workflow, discover issues through testing, and fix what you find.

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

**Option A — Playwright E2E Tests (timesheet-app):**
```
Set up and run timesheet-app locally (backend on port 3001, frontend on port 5173). Write Playwright E2E tests for the core user workflows: (1) Login flow, (2) Create a client, (3) Create a work entry for that client, (4) Verify the work entry appears in the list, (5) Edit the work entry, (6) Delete the work entry, (7) Check the reports page shows correct totals. Run the tests and take a screen recording. If any tests fail because of application bugs, fix the bugs too.
```

**Option B — API E2E Tests (BDD framework):**
```
Review the uc-bdd-test-generation-cucumber codebase. Run `mvn test` to verify the existing 16 scenarios pass. Then write new end-to-end Gherkin scenarios that test the full user lifecycle: create a user, verify they appear in the list, update their details, verify the update, then delete them and verify they're gone. Also test cross-resource relationships — create a user, create orders for that user, verify the orders appear when querying by user. Run all tests and fix any failures.
```

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What are the main user workflows in timesheet-app that would benefit from E2E tests?"*
- *"What Playwright best practices should be followed — proper selectors, waiting strategies, test isolation?"*
- *"What are the most common causes of flaky E2E tests and how can they be prevented?"*
- Use insights to write tests for additional workflows (client management, reporting, CSV/PDF export)

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the frontend routes, components, and API contracts. Use what you learn to try different approaches:
- Have Devin add **visual regression tests** with screenshot comparisons
- Ask Devin to add **accessibility tests** (axe-core integration with Playwright)
- Try having Devin add **performance assertions** (page load time < 2 seconds)
- Ask Devin to generate a **test report** with screenshots and timings for each test

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR from step 1, focus your review on **test robustness and bug fixes**:
- **Test quality:** Are the tests robust or will they be flaky? Do they use proper selectors and waiting strategies?
- **Bug fixes:** If Devin found and fixed bugs during testing, does the fix address the root cause?
- **Coverage completeness:** Do the E2E tests cover the critical user paths? Are there workflows missing?

**Leave a feedback comment** and watch Devin respond:
- *"Add a test for submitting the form with missing required fields — it should show validation errors"*
- *"The test uses sleep() instead of waiting for an element — please fix to avoid flakiness"*
- *"Add an E2E test for the CSV export feature on the reports page"*

See the full challenge details for [End-to-End Testing](../../modules/testing-qa/end-to-end-testing.md) and [Fix Runtime Bug](../../modules/application-development/fix-runtime-bug.md) for more ideas.

- **Key Takeaways:**
  - **"Test-discover-fix"** — E2E tests don't just validate existing behavior, they discover bugs. Devin fixes what it finds in the same session
  - **"Devin uses its browser"** — it interacts with the running application via its built-in browser, clicking through flows just like a human tester
  - **"Screen recordings as evidence"** — Devin's recordings show exactly what was tested and what was found, providing visual proof for stakeholders
  - **"E2E + unit tests = full coverage"** — combining Lab C2 (unit tests) with Lab C3 (E2E tests) gives comprehensive coverage at both the code and user-interaction levels

- **Target Outcomes (any of these count):**
  - Playwright E2E test suite covering core user workflows
  - Screen recording of test execution
  - Bug fixes discovered during E2E testing
  - Gherkin end-to-end scenarios for API lifecycle testing
  - PR with test files, bug fixes, and screen recording evidence

---

## Additional Challenges

Participants who finish early or want to explore further can attempt any challenge from the full [module catalog](../../modules/). Recommended extras:

| Challenge | Module | Repo | Track | Difficulty |
|-----------|--------|------|-------|------------|
| Data Source Migration | [Data Source Migration](../../modules/data-engineering/data-source-migration.md) | uc-data-source-migration-jdbc-normalization | B | Intermediate |
| Event-Driven SAST Pipeline | [Event-Driven SAST Remediation](../../modules/security/event-driven-sast-remediation.md) | uc-cve-remediation-regulatory-compliance | A | Advanced |
| Monolith Decomposition (.NET) | [.NET Monolith Decomposition](../../modules/migration-modernization/dotnet-monolith-decomposition.md) | modular-monolith-ddd | B | Advanced |
| Code Refactoring & Tech Debt | [Code Refactoring](../../modules/architecture-design/code-refactoring-tech-debt.md) | Any | C | Intermediate |
| API Documentation | [API Documentation](../../modules/technical-documentation/api-documentation.md) | Any | C | Beginner |

## Suggested Formats

| Format | Recommended Approach |
|--------|---------------------|
| Full day | All 3 tracks: Track A (morning) + Track B (midday) + Track C (afternoon) |
| Half day | 2 tracks: Choose any two tracks based on audience interest |
| Short session | 1 full track + 1 lab from another track |
| Sampler | Pick 1 lab from each track (e.g., A1 + B2 + C1) for breadth |
| Single lab | A1 (security) or C1 (feature dev) recommended for impact |

## Repos Required

### Track A (Security & Issue Triage)
- [ ] uc-cve-remediation-regulatory-compliance
- [ ] timesheet-app
- [ ] quickapp-microservices (optional, for Lab A2 Option B)

### Track B (Modernization)
- [ ] uc-spring-boot-upgrade-microservice-extraction
- [ ] petclinic-angular
- [ ] ts-angular-realworld (Lab B3 translation target reference; also useful for parallel comparison)

### Track C (Feature Development & Testing)
- [ ] timesheet-app
- [ ] uc-spring-boot-upgrade-microservice-extraction (optional, for Lab C1 Option B)
- [ ] uc-bdd-test-generation-cucumber (optional, for Lab C2 Option C / Lab C3 Option B)

## Repo Duplication Notes

- `uc-spring-boot-upgrade-microservice-extraction` and `uc-cve-remediation-regulatory-compliance` both originate from `spring-boot-realworld-example-app` (Cluster C1 in [catalog](../../catalog/repos.md)). They are intentionally separate repos so each use case gets an isolated starting point.

## Context

- **Audience:** General engineering teams — this workshop is designed to be relevant regardless of tech stack specialty
- **Focus:** Breadth across security, modernization, and feature development — demonstrating Devin's versatility
- **Devin value themes woven throughout:**
  - Autonomous, off-machine execution — kick off sessions and move on
  - Parallel sessions for scaling repetitive work across repos
  - Ask Devin for requirement gathering and task scoping before sessions
  - Knowledge and Playbooks for building reusable team context
  - PR Review agent + CI checks as automatic feedback loops
  - Scheduled sessions for continuous code hygiene (dependency bumps, test generation)
  - Long-running task handling — Devin works while you're in meetings

## Devin Features Checklist

Encourage participants to track their progress on the [Devin Features Appendix](../../modules/devin-features/README.md) throughout the session.
