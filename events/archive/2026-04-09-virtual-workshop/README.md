# Workshop: Security & QA — Virtual Workshop

## Event Details

| | |
|---|---|
| **Date** | 2026-04-09 |
| **Location** | Virtual |
| **Duration** | ~5 hours per track (5 labs + breaks) |
| **Event Site** | TBD |
| **Tracks** | Track A: Security & Vulnerability / Track B: QA & Quality Engineering |

## Getting the Most from This Workshop

> **This is a hands-on workshop.** Every lab gives you a prompt to paste into Devin. Devin runs autonomously on its own machine — once you kick off a session, you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

A few tips:

- **Start sessions early, review later.** Kick off the Devin session first, then use the wait time for Ask Devin exploration or reading. Devin works in the background.
- **Try parallel sessions.** You can run multiple Devin sessions at once — this mirrors real enterprise usage where Devin handles repetitive work across many repos.
- **Leave PR comments to steer Devin.** After Devin opens a PR, you can leave comments directly and Devin will wake up and address them. The PR Review agent and CI checks add automatic feedback on top.
- **Build up Devin's knowledge.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that makes Devin smarter over time.

---

## Choose Your Track

| | Track A: Security & Vulnerability | Track B: QA & Quality Engineering |
|---|---|---|
| **For** | AppSec engineers, DevSecOps, security-conscious developers | QA engineers, SDETs, test automation engineers |
| **You'll learn** | How Devin finds and fixes vulnerabilities, hardens CI, and remediates autonomously | How Devin writes tests, migrates frameworks, and monitors quality continuously |
| **Tools** | OWASP Dependency-Check, SonarQube, gitleaks, Trivy, GitHub Advanced Security | Jest/Vitest, Playwright, Cucumber/Gherkin, coverage tools |
| **Primary repos** | uc-cve-remediation-regulatory-compliance, app_timesheet | app_timesheet, app_petclinic-angular, uc-bdd-test-generation-rest-api |
| **Story arc** | Find → Fix → Prevent → Automate → Autonomous pipeline | Assess → Automate → Migrate → Generate → Continuous QA |

All tools in both tracks are free and run locally on Devin's VM — no commercial licenses required.

---

# Track A — Security & Vulnerability

> **What you'll see:** Devin scans a codebase for vulnerabilities, fixes them, proves the fix with a re-scan, and eventually runs the entire find-fix-verify cycle autonomously. Each lab builds on the last.

### Before You Start: DeepWiki

Open the **DeepWiki** page for [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance) — this is the primary repo for Track A. It's a Spring Boot 2.6.3 application with 18+ known CVEs and pre-configured OWASP Dependency-Check and SonarQube plugins. Browse the architecture overview to understand what you'll be working with. You can reference DeepWiki at any point during the labs.

---

### Lab A1 — "Paste a Prompt, Get a PR": Dependency Audit (45 min)

**Value driver:** *Devin works autonomously. Give it a well-defined task, walk away, come back to a PR.*

- **Module:** [Upgrade Dependencies](../../../modules/security/upgrade-dependencies.md)
- **Repos:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance), [app_timesheet](https://github.com/Cognition-Partner-Workshops/app_timesheet)

This is your first Devin session. Paste a prompt, Devin scans for CVEs, upgrades dependencies, and opens a PR. You don't need to watch — move on to Ask Devin while it works.

#### Give Devin a task

Pick one (or run both in parallel):

**Security scan — Spring Boot (uc-cve-remediation-regulatory-compliance):**
> Upgrade uc-cve-remediation-regulatory-compliance from Spring Boot 2.6.3 to the latest stable 2.7.x or 3.x, updating all transitive dependencies. Run `./gradlew dependencyCheckAnalyze` before and after to document which CVEs are resolved. Verify the build still passes. Open a PR with the upgrade and before/after scan evidence.

**Security scan — Node.js (app_timesheet):**
> Resolve this GitHub Issue: https://github.com/Cognition-Partner-Workshops/app_timesheet/issues/2 — audit the npm dependencies for known vulnerabilities, upgrade all vulnerable packages to their latest secure versions, ensure the build and tests still pass, and open a PR.

#### While Devin works: try Ask Devin

Open **Ask Devin** and explore the repo:
- *"What are the known CVEs in uc-cve-remediation-regulatory-compliance's dependencies? Which are CRITICAL?"*
- *"What's the safest upgrade path for Spring Boot 2.6.3 — 2.7.x first or straight to 3.x?"*
- Use what you learn to plan a second session targeting additional vulnerabilities.

#### Review the PR

When Devin opens a PR:
- Did it handle breaking changes from major version upgrades?
- Does the PR include before/after vulnerability counts?
- Do all tests still pass?

**Try the feedback loop:** Leave a comment like *"Also add `npm audit` to the CI pipeline so vulnerabilities are caught on every PR"* and watch Devin respond with a follow-up commit.

**Key Takeaways:**
- A clear, well-defined prompt ("upgrade from 2.6.3, run the scan before and after") produces focused output
- Devin interprets CVE databases, understands CVSS scores, and handles breaking changes during upgrades
- The real workflow: paste, walk away, review the PR later

---

### Lab A2 — "Devin Runs Your Tools": SAST Scanning with SonarQube (60 min)

**Value driver:** *Devin has its own machine with Docker. It can run any tool you'd run locally — SonarQube, OWASP DC, Trivy — and interpret the results.*

- **Module:** [Remediate Vulnerabilities](../../../modules/security/remediate-vulnerabilities.md)
- **Repo:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)

This lab goes beyond dependency scanning. Devin sets up SonarQube locally via Docker, runs both dependency and code-level SAST scans, and remediates findings. You can watch Devin set up SonarQube and navigate its dashboard in the Desktop tab.

#### Give Devin a task

> Run `./gradlew dependencyCheckAnalyze` on uc-cve-remediation-regulatory-compliance to identify dependency CVEs. Remediate the top 5 most critical findings (CVSS >= 7.0) — start with Spring Boot 2.6.3, SnakeYAML 1.29, and sqlite-jdbc 3.36.0.3.
>
> Then set up SonarQube locally using the included `docker-compose.sonarqube.yml` — run `docker compose -f docker-compose.sonarqube.yml up -d`, wait for SonarQube to be ready on port 9000, create a project and token, then run `./gradlew sonar -Dsonar.token=<TOKEN>`. Review the SonarQube dashboard for security hotspots and vulnerabilities.
>
> Re-run OWASP DC to verify the dependency fixes. Create a `SECURITY_REMEDIATION.md` documenting the before/after results from both tools and open a PR.

#### While Devin works: explore the tools

Open **Ask Devin** and dig deeper:
- *"What's the difference between what OWASP Dependency-Check finds (dependency CVEs) and what SonarQube finds (code-level issues)?"*
- *"Beyond dependency vulnerabilities, are there any code-level security issues (SQL injection, XSS) in this repo?"*

**Watch the Desktop tab** — you can see Devin running Docker, navigating the SonarQube dashboard, and interpreting scan results in real time.

#### Review the PR

- Are the top 5 CRITICAL/HIGH CVEs resolved? Check before/after OWASP DC reports
- Were any code-level SonarQube hotspots addressed?
- Does the `SECURITY_REMEDIATION.md` document both tools' findings?

**Key Takeaways:**
- Devin runs Docker, sets up SonarQube, and interprets multi-tool SAST results on its own VM
- OWASP DC + SonarQube together cover the same ground as commercial tools — dependency scanning plus code analysis, zero licensing cost
- The scan → fix → re-scan pattern: Devin doesn't just find issues, it fixes them and proves the fix

---

### Lab A3 — "Guardrails That Prevent Future Problems": Secrets Detection (45 min)

**Value driver:** *Devin doesn't just fix the current problem — it sets up guardrails (pre-commit hooks, CI steps) that prevent it from recurring.*

- **Module:** [Secrets Management & Detection](../../../modules/security/secrets-management-detection.md)
- **Repos:** [app_timesheet](https://github.com/Cognition-Partner-Workshops/app_timesheet), [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)

Devin scans for hardcoded secrets, migrates them to environment variables, and installs preventive controls so no one can accidentally commit secrets again.

#### Give Devin a task

Pick one:

**Node.js (app_timesheet):**
> Install and run gitleaks on app_timesheet to scan for hardcoded secrets and credentials. Migrate any findings to environment variables using a `.env.example` file (without actual secret values). Add a pre-commit hook using husky that runs gitleaks on staged files. Add a GitHub Actions step that fails PRs introducing new secrets. Open a PR.

**Spring Boot (uc-cve-remediation-regulatory-compliance):**
> Install and run gitleaks on uc-cve-remediation-regulatory-compliance to scan the full git history for leaked secrets. Review `application.properties` and `application.yml` for hardcoded credentials. Migrate all sensitive configuration to environment variable placeholders (e.g., `${DB_PASSWORD}`). Add a GitHub Actions workflow step that runs gitleaks on every PR. Open a PR.

#### While Devin works

Use **Ask Devin** to understand the current state:
- *"Are there any hardcoded secrets, API keys, or database connection strings in app_timesheet?"*
- *"What's the difference between gitleaks (scans git history) and GitHub Advanced Security secret scanning (scans live pushes)?"*

#### Review the PR

- Are all hardcoded secrets externalized? Is `.env.example` complete?
- Is the pre-commit hook properly configured?
- Did Devin accidentally include actual secret values? (This is a real check — it shouldn't, but verify.)

**Key Takeaways:**
- Finding secrets is only half the job — pre-commit hooks and CI steps prevent new ones from entering the codebase
- Devin scans git history, not just current files. Secrets removed from code may still exist in old commits
- Devin sets up guardrails (hooks, CI) that protect the repo going forward

---

### Lab A4 — "Automate Everything in CI": Security Pipeline (60 min)

**Value driver:** *Devin creates CI/CD workflows from scratch. The scanning tools from Labs A1–A3 now run automatically on every PR.*

- **Module:** [Shift Left Security](../../../modules/security/shift-left-security.md)
- **Repos:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance), [app_timesheet](https://github.com/Cognition-Partner-Workshops/app_timesheet)

This lab ties everything together into an automated CI pipeline. Devin writes the GitHub Actions workflow that scans every PR for vulnerabilities and blocks merges on critical findings.

#### Give Devin a task

Pick one:

**Build from scratch (uc-cve-remediation-regulatory-compliance):**
> Create a GitHub Actions CI pipeline for uc-cve-remediation-regulatory-compliance that: builds with Gradle, runs `./gradlew dependencyCheckAnalyze`, fails the PR if any dependency has CVSS >= 7.0, generates an SBOM in CycloneDX format, and uploads the dependency check report as a build artifact. Open a PR with the workflow file.

**Enhance existing workflows (app_timesheet):**
> Review the existing security scanning workflows in app_timesheet (.github/workflows/). Enhance them by adding: SBOM generation in CycloneDX format, a dependency-review step on PRs, and a Trivy container scan if Dockerfiles exist. The workflow should fail the PR on CRITICAL severity findings. Open a PR with the enhanced workflows.

#### While Devin works

Use **Ask Devin** to study the existing patterns:
- *"What security scanning is already configured in app_timesheet's CI? What gaps exist?"*
- *"How does the sonar-devin-fix.yml workflow in app_timesheet trigger Devin to auto-fix issues?"*

Study the existing `sonar-devin-fix.yml` — this is the foundation pattern for Lab A5's autonomous pipeline.

#### Review the PR

- Does the workflow run on PRs and pushes to main?
- Does it correctly block merges on CRITICAL/HIGH findings?
- Are scan reports uploaded as build artifacts for review?

**Try the feedback loop:** *"Add NVD database caching to speed up subsequent OWASP DC runs"* or *"The workflow should also run gitleaks for secrets detection."*

**Key Takeaways:**
- GitHub Actions authoring is a well-defined task where Devin excels
- CI is the enforcement layer — the manual scans from Labs A1–A3 now run automatically on every PR
- SBOM generation is increasingly required for compliance — Devin adds it as part of the pipeline

---

### Lab A5 — "Devin as a Background Agent": Autonomous SAST Remediation (60 min)

**Value driver:** *Devin isn't just a tool you open manually. It can be triggered by CI events and remediate findings autonomously — scan, fix, verify, all without human intervention.*

- **Module:** [Event-Driven SAST Remediation](../../../modules/security/event-driven-sast-remediation.md)
- **Repos:** [app_timesheet](https://github.com/Cognition-Partner-Workshops/app_timesheet), [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)

This is the capstone. Participants build a closed-loop pipeline: CI scans for vulnerabilities → triggers Devin via API → Devin remediates and pushes a fix → CI re-scans to verify. This is the enterprise pattern: Devin as a team member that handles security findings autonomously.

#### Give Devin a task

Pick one:

**Extend existing pattern (app_timesheet):**
> Analyze the existing `.github/workflows/sonar-devin-fix.yml` in app_timesheet. This workflow already triggers Devin to fix SonarQube findings. Extend this pattern to create a new workflow called `sast-auto-remediate.yml` that: (1) triggers on PRs opened by users other than `devin-ai-integration[bot]`, (2) runs `npm audit --json` and Trivy container scan, (3) if HIGH or CRITICAL findings are found, posts a PR comment summarizing the findings and triggers a Devin session via the Devin API to remediate them on the same branch, (4) includes a re-scan step that verifies the fix. Document the architecture in an `ARCHITECTURE.md`. Open a PR.

**Build from scratch (uc-cve-remediation-regulatory-compliance):**
> Create a complete event-driven security remediation pipeline for uc-cve-remediation-regulatory-compliance. Build a GitHub Actions workflow `sast-auto-remediate.yml` that: (1) triggers on pull_request events from non-bot authors, (2) runs `./gradlew dependencyCheckAnalyze` and captures the report, (3) parses the report for findings with CVSS >= 7.0, (4) if critical findings exist, calls the Devin API to create a session with the prompt "Remediate the CRITICAL and HIGH CVEs found in the dependency check report. Push fixes to branch [branch-ref]. Re-run ./gradlew dependencyCheckAnalyze to verify.", (5) includes a verification step that re-runs the scan after Devin's commit. Add author filtering to prevent bot loops. Document the full architecture in `EVENT_DRIVEN_SECURITY.md`. Open a PR.

#### While Devin works

Use **Ask Devin** to understand the Devin API integration:
- *"How does the existing sonar-devin-fix.yml workflow in app_timesheet trigger Devin? What API endpoint and parameters does it use?"*
- *"What's the best way to prevent an infinite loop where Devin's fix commit triggers another scan that triggers another Devin session?"*

This is also a good time to think about **Scheduled Sessions** — the same scan-and-fix pattern can run weekly across all repos.

#### Review the PR

- Does the workflow correctly filter out PRs from `devin-ai-integration[bot]` to prevent infinite loops?
- Is the Devin API call properly configured?
- Does the pipeline re-scan after Devin's fix to confirm remediation?

**Try the feedback loop:** *"Add an escalation path: if Devin's fix doesn't resolve all findings after 2 attempts, open a GitHub Issue and assign it to a human reviewer."*

**Key Takeaways:**
- Devin API enables programmatic integration — Devin can be triggered from any CI/CD pipeline, webhook, or automation tool
- The closed loop (scan → fix → re-scan) runs without human intervention. You just review the resulting PR
- Circuit breakers (author filtering, attempt limits) make this production-safe
- Combine with Scheduled Sessions for weekly security scans across all repos

---

# Track B — QA & Quality Engineering

> **What you'll see:** Devin assesses test quality, writes tests, migrates frameworks, generates BDD scenarios, and eventually monitors quality continuously as a background team member. Each lab builds on the last.

### Before You Start: DeepWiki

Open the **DeepWiki** page for [app_timesheet](https://github.com/Cognition-Partner-Workshops/app_timesheet) — this is the primary repo for Track B. It's a React + Node.js full-stack application. Browse the architecture, testing patterns, and component structure. You can reference DeepWiki at any point during the labs.

---

### Lab B1 — "Paste a Prompt, Get a PR": Test Coverage Audit (45 min)

**Value driver:** *Devin works autonomously. Give it a well-defined task, walk away, come back to a PR.*

- **Module:** [Unit Testing](../../../modules/testing-qa/unit-testing.md)
- **Repos:** [app_timesheet](https://github.com/Cognition-Partner-Workshops/app_timesheet), [ts-java-spring-boot-realworld-example-app](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-realworld-example-app)

This is your first Devin session. Paste a prompt, Devin analyzes test coverage, writes tests, and opens a PR. You don't need to watch.

#### Give Devin a task

Pick one (or run both in parallel):

**Full-stack coverage audit (app_timesheet):**
> Analyze the test coverage of app_timesheet. Run the existing test suites for both the backend (Node.js/Express) and frontend (React) and generate coverage reports. Identify the 5 modules or files with the lowest coverage.
>
> For each of the 3 worst-covered backend files, write unit tests that bring coverage above 80%. Follow the existing test patterns in the codebase (testing framework, assertion style, mock patterns). For the frontend, write React Testing Library tests for the 2 least-covered components.
>
> Create a `COVERAGE_REPORT.md` summarizing: current coverage baseline, files targeted, tests added, and new coverage numbers. Open a PR.

**Spring Boot coverage audit (ts-java-spring-boot-realworld-example-app):**
> Analyze the test coverage of ts-java-spring-boot-realworld-example-app. Run `./gradlew test jacocoTestReport` and identify classes with the lowest line coverage. Focus on the service layer and API controllers.
>
> Write JUnit 5 tests for the 5 least-covered classes, following the existing test patterns (MockMvc for controllers, Mockito for services). Ensure each new test class covers happy path, error cases, and edge cases. Open a PR with the new tests and an updated coverage report.

#### While Devin works: try Ask Devin

Open **Ask Devin** and explore:
- *"What testing patterns does app_timesheet use — what assertion library, mocking framework, and test structure should new tests follow?"*
- *"Which parts of the backend have the most complex business logic but the least test coverage?"*
- Use what you learn to plan a second session targeting different files.

#### Review the PR

- Do the tests verify meaningful behavior, or are they trivial assertions?
- Did coverage actually improve for the targeted files?
- Do the new tests follow the same patterns as existing tests?

**Try the feedback loop:** *"This test mocks too many dependencies — use the existing test database setup instead"* or *"Add edge case tests for null/empty inputs."*

**Key Takeaways:**
- A clear prompt ("bring the 5 worst-covered files above 80%") produces focused, measurable output
- Devin analyzes existing test patterns before writing — its tests fit the codebase
- The real workflow: paste, walk away, review the PR later

---

### Lab B2 — "Devin Has Its Own Machine": E2E Browser Testing (60 min)

**Value driver:** *Devin has a full VM with a browser. It starts the app, navigates the UI, writes Playwright tests, and takes screenshots as evidence. You can watch it work in the Desktop tab.*

- **Module:** [End-to-End Testing](../../../modules/testing-qa/end-to-end-testing.md)
- **Repo:** [app_timesheet](https://github.com/Cognition-Partner-Workshops/app_timesheet)

This lab showcases a unique capability: Devin runs the app on its own machine, opens a real browser, and interacts with the running UI.

#### Give Devin a task

> Start the app_timesheet application locally (both backend and frontend). Once the app is running, write Playwright end-to-end tests for the core user workflows:
>
> 1. **Authentication flow:** Register a new user, log in, verify the dashboard loads
> 2. **Client management:** Create a new client, edit the client name, verify the update persists
> 3. **Time entry workflow:** Create a new work entry for a client, verify it appears in the list, verify the hours total is correct
> 4. **Report generation:** Navigate to reports, verify the data matches the entries created
>
> For each test, take a screenshot at key checkpoints as visual evidence. Run all tests and ensure they pass against the running application. Create a Playwright configuration file if one doesn't exist. Open a PR with the test files, configuration, and screenshots.

#### While Devin works

**Watch the Desktop tab** — you can see Devin navigate the application in real time as it writes and runs tests. This is a great way to understand how Devin interacts with running applications.

Use **Ask Devin** to explore:
- *"What is the app_timesheet login flow — is it email/password or OAuth?"*
- *"What are the most critical user journeys that should have E2E coverage?"*

#### Review the PR

- Are the tests resilient to timing issues? Do they use proper Playwright locators?
- Do the screenshots show the expected UI state at each checkpoint?
- Could a new team member understand the app's workflows just by reading the test descriptions?

**Key Takeaways:**
- Devin has its own machine — it starts apps, opens browsers, and interacts with running UIs
- The Desktop tab lets you watch Devin work in real time
- E2E tests double as executable documentation of your app's workflows
- The VM persists between sessions — a follow-up session can add more tests without restarting the environment

---

### Lab B3 — "Repetitive Work at Scale": Test Framework Migration (60 min)

**Value driver:** *Devin excels at repetitive, file-by-file work. The same conversion pattern applied consistently across dozens of files. You can fan out into parallel sessions for speed.*

- **Module:** [Test Framework Migration](../../../modules/testing-qa/test-framework-migration.md)
- **Repos:** [app_petclinic-angular](https://github.com/Cognition-Partner-Workshops/app_petclinic-angular), [ts-angular-realworld-example-app](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld-example-app) (reference)

Migrate app_petclinic-angular from deprecated test frameworks (Karma + Jasmine + Protractor) to modern replacements (Jest/Vitest + Playwright). This is tedious for humans but ideal for Devin.

#### Give Devin a task

> Analyze the test infrastructure in app_petclinic-angular. The project currently uses Karma + Jasmine for unit tests and Protractor for E2E tests — both are deprecated in the Angular ecosystem.
>
> Phase 1 — Unit test migration:
> Replace Karma + Jasmine with Jest (or Vitest). Update the test configuration, convert all `.spec.ts` files to use the new test runner's API, and ensure all unit tests pass. Remove the Karma dependencies and configuration files.
>
> Phase 2 — E2E test migration:
> Replace Protractor with Playwright. Convert any existing E2E tests (or create new ones if none exist) to use Playwright's API. Add a Playwright configuration file and ensure the E2E tests can run against the dev server.
>
> Use ts-angular-realworld-example-app as a reference — it already uses Vitest + Playwright. Create a `MIGRATION_RUNBOOK.md` documenting patterns converted, manual interventions needed, and common gotchas. Open a PR.

#### While Devin works

Try running **parallel sessions** — one for Phase 1 (unit tests) and one for Phase 2 (E2E). Each session gets its own VM.

Use **Ask Devin** to plan:
- *"What Karma/Jasmine patterns in app_petclinic-angular will be hardest to convert to Jest?"*
- *"How does ts-angular-realworld-example-app structure its Vitest + Playwright setup? Can we use it as a reference?"*

After the migration succeeds, consider asking Devin to **create a Playbook** capturing the migration steps. Then any team member can reuse the same pattern for other Angular repos with one click.

#### Review the PR

- Do the migrated tests still test the same behavior?
- Are all `.spec.ts` files converted? Any leftover Karma/Protractor references?
- Is the `MIGRATION_RUNBOOK.md` useful enough that another team could follow it?

**Key Takeaways:**
- The same conversion pattern applied across dozens of files — tedious for humans, ideal for Devin
- Parallel sessions save calendar time — unit test migration and E2E migration run simultaneously
- Multi-repo workspace: Devin references a working example (ts-angular-realworld-example-app) while migrating another repo
- **Playbooks turn one-off work into repeatable processes** — capture the runbook so the next migration is a one-click operation

---

### Lab B4 — "The PR Feedback Loop": BDD Test Generation (60 min)

**Value driver:** *Devin reads existing framework patterns and generates consistent output. When you leave PR comments, Devin wakes up and addresses them — this is the core workflow for iterating with Devin.*

- **Module:** [BDD Test Generation](../../../modules/testing-qa/bdd-test-generation.md)
- **Repo:** [uc-bdd-test-generation-rest-api](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-rest-api)

Give Devin a Spring Boot + Cucumber BDD framework and prompt it to generate new test scenarios, build a new API resource, and produce executable Cucumber tests. Then practice the PR feedback loop.

#### Give Devin a task

> Review the uc-bdd-test-generation-rest-api codebase. This is a Spring Boot + Cucumber BDD framework with pre-built step definitions for REST API testing. Run `mvn test` to see the 16 existing scenarios pass.
>
> Then add new Gherkin feature files that test edge cases for the existing Users API:
> - `src/test/resources/features/users-edge-cases.feature` covering:
>   - Creating a user with missing required fields (expect 400)
>   - Creating a user with duplicate ID (expect 409 or appropriate error)
>   - Pagination and sorting of the users list
>   - Input validation (invalid age values, empty name fields)
>
> Also create a new `OrderController` in the test application with endpoints for managing orders (linked to users). Write corresponding Gherkin feature files that test the order lifecycle (create, read, update, delete) and cross-resource relationships (e.g., get orders for a specific user). Open a PR.

#### While Devin works

Use **Ask Devin** to explore:
- *"What Cucumber best practices should be followed for REST API testing?"*
- *"How can WireMock be used to test failure modes — what happens when the API returns 500 or times out?"*

#### Review the PR — then practice the feedback loop

This lab is specifically about the PR interaction pattern:
1. **Review the diff** — are the Gherkin scenarios readable by non-developers?
2. **Leave a comment** — try: *"Add data-driven scenarios using Cucumber Scenario Outlines with Examples tables"*
3. **Watch Devin respond** — it will wake up, read your comment, and push a fix
4. **Leave another comment** — try: *"Add WireMock failure mode tests for API timeout and 500 responses"*

This back-and-forth is how real teams work with Devin in production.

**Key Takeaways:**
- Devin reads existing patterns and generates consistent, framework-aware output
- The PR feedback loop is the core workflow — review, comment, Devin responds, iterate
- PR Review agent adds automatic feedback on top of your human review
- New resources with matching tests — Devin builds the API and its test suite together

---

### Lab B5 — "Devin as a Team Member": Continuous Quality Engineering (45 min)

**Value driver:** *Devin isn't just a tool you use once. Scheduled Sessions, Playbooks, and Knowledge turn Devin into an ongoing team member that monitors quality automatically.*

- **Module:** [Continuous Quality Engineering](../../../modules/testing-qa/continuous-quality-engineering.md)
- **Repos:** [app_timesheet](https://github.com/Cognition-Partner-Workshops/app_timesheet), [uc-bdd-test-generation-rest-api](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-rest-api)

This is the capstone. Set up Devin as an ongoing quality engineering team member using Scheduled Sessions, Playbooks, and Knowledge.

#### Start with Ask Devin

Before creating a session, plan the QA strategy with Ask Devin:
- *"What is the current test coverage of app_timesheet? Which areas have the weakest coverage?"*
- *"What recurring QA tasks would benefit from weekly automation — coverage checks, dependency audits, test smell detection?"*
- *"What test quality standards should we encode in a Playbook?"*

#### Give Devin a task

> Set up a continuous quality engineering practice for app_timesheet.
>
> Part 1 — Test coverage baseline:
> Run the existing test suite and generate a coverage report. Identify the 5 files/modules with the lowest coverage. Write tests to bring the worst offender above 80% coverage.
>
> Part 2 — Flaky test detection:
> Run the test suite 10 times in sequence. Log pass/fail results for each test across all runs. Identify any tests that produce inconsistent results. For each flaky test, diagnose the root cause and propose a fix.
>
> Part 3 — Test smell audit:
> Scan the test suite for common anti-patterns: tests that depend on execution order, tests with no assertions, tests that mock too many dependencies, tests with hardcoded dates/timestamps. Document findings in a `TEST_QUALITY_REPORT.md`.
>
> Open a PR with coverage improvements, flaky test fixes, and the quality report.

#### Set up the ongoing practice

After reviewing Devin's output:

1. **Create a Playbook** — go to Devin Settings and create a "Weekly QA Audit" Playbook encoding the methodology: run tests, check coverage against baseline, detect flaky tests, scan for anti-patterns, open a PR with improvements.

2. **Set up a Scheduled Session** — configure a weekly session (e.g., every Monday at 6 AM) that runs the Weekly QA Audit Playbook against app_timesheet. This is the "Devin as team member" story — continuous quality monitoring without human initiation.

3. **Create Knowledge items** — based on what you learned in Labs B1–B4, create Knowledge items like:
   - "All API tests must include both happy path and error cases"
   - "Use React Testing Library's userEvent over fireEvent"
   - "New code must have >80% line coverage"

These Knowledge items automatically inform all future Devin sessions.

**Key Takeaways:**
- Scheduled Sessions turn one-shot tasks into continuous monitoring
- Playbooks encode your team's methodology as executable instructions anyone can trigger
- Knowledge compounds over time — every item makes future sessions smarter
- This is the enterprise story: Devin as an ongoing team member, not a tool you open manually

---

## Additional Challenges

Participants who finish early can try any challenge from the full [module catalog](../../../modules/). Recommended extras:

### Security Extras

| Challenge | Module | Difficulty | Time |
|-----------|--------|-----------|------|
| Security Antipatterns | [Security Antipatterns](../../../modules/security/security-antipatterns.md) | Intermediate | 45 min |
| Mass Security Backlog Remediation | [Mass Security Backlog Remediation](../../../modules/security/mass-security-backlog-remediation.md) | Advanced | 90 min |

### QA Extras

| Challenge | Module | Difficulty | Time |
|-----------|--------|-----------|------|
| Linting & Static Analysis | [Linting & Static Analysis](../../../modules/testing-qa/linting-static-analysis.md) | Beginner | 30 min |
| Performance Testing | [Performance Testing](../../../modules/testing-qa/performance-testing.md) | Intermediate–Advanced | 60 min |
| Visual Regression Testing | [Visual Regression Testing](../../../modules/testing-qa/visual-regression-testing.md) | Intermediate | 45 min |
| Mutation Testing | [Mutation Testing](../../../modules/testing-qa/mutation-testing.md) | Intermediate–Advanced | 60 min |
| Contract Testing | [Contract Testing](../../../modules/testing-qa/contract-testing.md) | Intermediate–Advanced | 60 min |

## Repos Required on Devin's Machine

### Track A (Security)

- [ ] uc-cve-remediation-regulatory-compliance
- [ ] app_timesheet

### Track B (QA)

- [ ] app_timesheet
- [ ] app_petclinic-angular
- [ ] ts-angular-realworld-example-app
- [ ] uc-bdd-test-generation-rest-api

#### Optional (for extended challenges)

- [ ] ts-java-spring-boot-realworld-example-app
- [ ] Online-Banking-System-using-Java
- [ ] uc-framework-upgrade-monolith-to-microservices
- [ ] app_petclinic-microservices
- [ ] cal.com

## Repo Duplication Notes

- `app_timesheet` appears in both tracks — it's used for security scanning (Track A) and test generation (Track B). Participants in both tracks may work on the same repo but target different aspects.
- `uc-cve-remediation-regulatory-compliance` is a Spring Boot 2.6.3 app with 18+ known CVEs, pre-configured with OWASP Dependency-Check and SonarQube Gradle plugins. It's the primary repo for Track A.
- `ts-angular-realworld-example-app` already uses modern Vitest + Playwright — it serves as a reference target for the test framework migration, not a migration source.

## Context

- **Audience:** First-time Devin users — security engineers, AppSec teams, QA engineers, SDETs, test automation engineers
- **Design philosophy:** Each lab leads with a Devin value driver (autonomous execution, local tool management, parallel sessions, PR feedback loops, background agent). The security/QA tasks are the vehicle, not the point — participants should walk away understanding why Devin is valuable.
- **Security narrative (Track A):** Find → Fix → Prevent → Automate → Autonomous pipeline
- **QA narrative (Track B):** Assess → Automate → Migrate → Generate → Continuous monitoring
- **All tools free and local:** OWASP Dependency-Check, SonarQube Community Edition, gitleaks, Trivy (Track A); Jest/Vitest, Playwright, Cucumber/Gherkin (Track B)
- **Value drivers woven through both tracks:**
  - **Lab 1 (both):** Paste a prompt, get a PR — autonomous execution
  - **Lab 2 (both):** Devin runs tools on its VM — Docker, browsers, scanners, test runners
  - **Lab 3 (both):** Repetitive work at scale — parallel sessions, guardrails, migration patterns
  - **Lab 4 (both):** The feedback loop — PR comments, CI integration, iterative refinement
  - **Lab 5 (both):** Devin as a background agent — Devin API, Scheduled Sessions, Playbooks, Knowledge

## Devin Features Checklist

Encourage participants to track their progress on the [Devin Features Appendix](../../../modules/devin-features/README.md) throughout the session.
