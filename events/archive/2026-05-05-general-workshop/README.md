# Workshop: May 5 General Workshop

## Event Details

| | |
|---|---|
| **Date** | 2026-05-05 |
| **Location** | Virtual |
| **Based On** | [workshops/general](../../../workshops/general/) |
| **Duration** | Flexible (3 tracks, 3 labs each) |
| **Audience** | Development teams, engineering leaders, platform teams evaluating Devin across multiple use cases |
| **Tracks** | **Track A:** Security & Issue Triage · **Track B:** Modernization · **Track C:** Feature Development & Testing |
| **Event Site** | TBD |

## Workshop Overview

This is a hands-on general workshop covering the three most common categories of engineering work: keeping systems secure and stable, modernizing legacy technology, and building new features with quality. Each track is self-contained — participants can follow all three in sequence for a full-day experience, or focus on one or two tracks in a shorter session.

This event supplements the [standard general workshop](../../../workshops/general/) with **industry-inspired spotlight prompts** — bonus activities drawn from real-world enterprise patterns that participants can try alongside or in place of the standard labs. These spotlight prompts highlight how Devin handles tasks like API-spec-driven code generation, automated data anomaly detection, security report generation, API contract validation, and codebase knowledge extraction.

## Getting the Most from This Workshop

> **Devin works autonomously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

A few tips to maximize your hands-on time:

- **Start sessions early, review later.** Each lab has a "Paste into Devin" step and a separate "Review & Give Feedback" step. Kick off the session first, then use the wait time for Ask Devin research or reading DeepWiki — Devin will keep working in the background.
- **Try parallel sessions.** Several labs suggest running multiple Devin sessions at once. This mirrors real enterprise usage where Devin handles repetitive work across many services.
- **Use Ask Devin to refine requirements before creating a session.** The better-defined a task is, the better Devin's output. Ask Devin helps you think through the problem first so Devin can execute with less back-and-forth.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item during a session, accept it — this is how teams build a shared context layer that makes Devin smarter over time. You can also create Knowledge manually for project conventions and standards.
- **Leave PR comments to steer Devin.** After Devin opens a PR, the PR Review agent and CI checks provide automatic feedback loops. You can also leave comments directly on the PR and Devin will wake up and address them — this is the core workflow for iterating with Devin in production.
- **Try the spotlight prompts.** Alongside each track, you'll find industry-inspired bonus prompts that show Devin working on patterns common in enterprise environments. These are optional but recommended if you want to see Devin tackle more advanced, cross-cutting scenarios.

---

## Agenda

| Time | Activity | Track |
|------|----------|-------|
| 0:00 | Welcome, Devin overview, setup | All |
| 0:15 | **Track A: Security & Issue Triage** — Labs A1–A3 | A |
| 1:45 | Break | — |
| 2:00 | **Track B: Modernization** — Labs B1–B3 | B |
| 3:30 | Break | — |
| 3:45 | **Track C: Feature Development & Testing** — Labs C1–C3 | C |
| 5:15 | Wrap-up, discussion, spotlight prompt showcase | All |

> **Flexible format:** See the [Suggested Formats](#suggested-formats) section for shorter variants. The spotlight prompts work with any format — they can substitute for or supplement any standard lab.

---

## Track A: Security & Issue Triage

Track A demonstrates Devin as a security and reliability agent. Participants will run SAST scans and remediate findings, investigate and fix bugs with root cause analysis, and set up automated scheduled sessions for ongoing dependency hygiene.

> Follow the full lab instructions in the [general workshop guide](../../../workshops/general/README.md#track-a-security--issue-triage). The labs below are summarized with links — see the general guide for complete step-by-step walkthroughs.

### Lab A1 — SAST Scans & Vulnerability Remediation

- **Modules:** [Remediate Vulnerabilities](../../../labs/security/remediate-vulnerabilities.md) + [Shift Left Security](../../../labs/security/shift-left-security.md)
- **Repositories:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance), [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)
- **Objective:** Run SAST tools to identify vulnerabilities, remediate the most critical findings, and add security scanning to CI

See [general workshop Lab A1](../../../workshops/general/README.md#lab-a1--sast-scans--vulnerability-remediation) for full instructions.

### Lab A2 — Bug Fixes & Root Cause Analysis

- **Modules:** [Fix Runtime Bug](../../../labs/application-development/fix-runtime-bug.md) + [Cross-Service Bug Investigation](../../../labs/migration-modernization/cross-service-bug-investigation.md)
- **Repositories:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app), [quickapp-microservices](https://github.com/Cognition-Partner-Workshops/quickapp-microservices)
- **Objective:** Find and fix bugs in running applications, perform root cause analysis

See [general workshop Lab A2](../../../workshops/general/README.md#lab-a2--bug-fixes--root-cause-analysis) for full instructions.

### Lab A3 — Scheduled Dependency Hygiene

- **Modules:** [Upgrade Dependencies](../../../labs/security/upgrade-dependencies.md)
- **Repositories:** [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance), [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)
- **Objective:** Set up recurring Devin scheduled sessions for automated dependency upgrades

See [general workshop Lab A3](../../../workshops/general/README.md#lab-a3--scheduled-dependency-hygiene) for full instructions.

### Spotlight: Comprehensive Security Reporting

> *Inspired by: Automated security remediation with SAST, DAST, and SCA analysis*

This spotlight goes beyond single-tool scanning. Instead of running one SAST tool, have Devin produce a unified security posture report combining multiple analysis techniques — the kind of report a security team would present to leadership.

#### Paste into Devin

```
Perform a comprehensive security assessment of uc-cve-remediation-regulatory-compliance. Run three types of analysis:

1. **Dependency scanning (SCA):** Run `./gradlew dependencyCheckAnalyze` to identify known CVEs in third-party libraries. Categorize findings by CVSS severity.
2. **Static code analysis (SAST):** Review the source code for security antipatterns — hardcoded credentials, SQL injection risks, insecure deserialization, missing input validation, and unsafe cryptographic usage.
3. **Configuration review:** Check application.properties, Dockerfiles (if any), and CI workflows for security misconfigurations — exposed ports, debug mode in production, missing security headers, overly permissive CORS.

Produce a unified `SECURITY_POSTURE_REPORT.md` that includes:
- Executive summary with overall risk rating (Critical / High / Medium / Low)
- Findings table organized by category (SCA, SAST, Configuration) with severity, description, and recommended fix
- A prioritized remediation roadmap — what to fix first and why
- Fix the top 3 most critical findings and re-verify

Open a PR with the report and the fixes.
```

#### Why Try This

- Shows Devin doing multi-dimensional security analysis, not just running a single tool
- The unified report format mirrors real security review workflows
- Participants see how Devin reasons about prioritization — which findings matter most?
- Demonstrates that Devin can produce executive-ready documentation, not just code changes

- **Key Takeaways:**
  - **"Multi-tool analysis"** — Devin combines dependency scanning, code review, and configuration audit into one workflow
  - **"Prioritized remediation"** — Devin doesn't just list findings, it recommends what to fix first based on risk
  - **"Executive-ready output"** — the report format is useful for compliance, audits, and leadership visibility

---

## Track B: Modernization

Track B demonstrates Devin handling large-scale structural changes to codebases. Participants will extract microservices from a monolith, upgrade end-of-life frameworks, and translate legacy code to a modern language.

> Follow the full lab instructions in the [general workshop guide](../../../workshops/general/README.md#track-b-modernization).

### Lab B1 — Rearchitecting Monolith to Microservice

- **Module:** [Containerization & Microservice Extraction](../../../labs/migration-modernization/containerization-microservice-extraction.md)
- **Repositories:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)
- **Objective:** Analyze domain boundaries in a monolith, extract a bounded context as a standalone microservice

See [general workshop Lab B1](../../../workshops/general/README.md#lab-b1--rearchitecting-monolith-to-microservice) for full instructions.

### Lab B2 — Upgrading EOL Systems to LTS Versions

- **Modules:** [Framework Upgrade](../../../labs/migration-modernization/framework-upgrade.md) + [Repetitive Framework Upgrades](../../../labs/migration-modernization/repetitive-framework-upgrades.md)
- **Repositories:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction), [petclinic-angular](https://github.com/Cognition-Partner-Workshops/petclinic-angular), [ts-angular-realworld](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld)
- **Objective:** Run parallel Devin sessions upgrading frameworks across multiple repos

See [general workshop Lab B2](../../../workshops/general/README.md#lab-b2--upgrading-eol-systems-to-lts-versions) for full instructions.

### Lab B3 — Language Translation

- **Repositories:** [ts-angular-realworld](https://github.com/Cognition-Partner-Workshops/ts-angular-realworld), [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)
- **Objective:** Translate code between languages (e.g., Java to Kotlin, Ruby to Java) while maintaining functional parity

See [general workshop Lab B3](../../../workshops/general/README.md#lab-b3--language-translation) for full instructions.

### Spotlight: API-Spec-Driven Microservice Generation

> *Inspired by: Microservice code generation from API specifications with full component scaffolding and test coverage*

Instead of extracting a service from an existing monolith (Lab B1), this spotlight starts from a clean API specification and has Devin generate a complete, production-ready microservice from scratch — controllers, services, validation, configuration, and tests targeting 90% coverage.

#### Paste into Devin

```
Generate a complete Spring Boot microservice from the following API specification. The service manages "Notifications" for a messaging platform.

API contract:
- POST /api/notifications — create a notification (fields: recipientId, channel [EMAIL|SMS|PUSH], subject, body, priority [LOW|NORMAL|HIGH|URGENT], scheduledAt [optional ISO datetime])
- GET /api/notifications — list notifications with pagination, filtering by channel, priority, and status
- GET /api/notifications/{id} — get notification by ID
- PUT /api/notifications/{id} — update a notification (only if status is PENDING)
- DELETE /api/notifications/{id} — soft-delete a notification
- POST /api/notifications/{id}/send — trigger immediate delivery
- GET /api/notifications/stats — aggregated counts by channel, priority, and status

Generate all components following Spring Boot conventions:
1. **Controller** with proper REST mappings, request validation (@Valid), and error handling (@ControllerAdvice)
2. **Service layer** with business logic, status state machine (PENDING → SENT / FAILED / CANCELLED), and validation rules
3. **DTOs and mappers** — separate request/response DTOs from the JPA entity, with MapStruct or manual mapping
4. **JPA Entity** with audit fields (createdAt, updatedAt), soft-delete support, and proper indexing annotations
5. **Repository** with custom query methods for filtering and stats aggregation
6. **Configuration** — application.yml with profiles (dev/test/prod), Flyway migration scripts for the schema
7. **Exception handling** — global handler with RFC 7807 Problem Details responses
8. **JUnit tests** — aim for 90%+ line coverage. Include unit tests for service logic, integration tests for the controller layer (@WebMvcTest), and repository tests (@DataJpaTest)

Use an H2 in-memory database for dev/test. Structure the project with a clean package layout. Open a PR.
```

#### Why Try This

- Mirrors a common enterprise workflow: "We have an API spec from the architects — now generate the service"
- Shows Devin scaffolding a complete, layered microservice — not just a controller, but the full production stack
- The 90% test coverage target demonstrates Devin's ability to generate meaningful tests, not boilerplate
- Participants can compare the generated service structure against their own team's conventions

- **Key Takeaways:**
  - **"Spec-to-service in one session"** — Devin generates a fully layered microservice from an API contract, complete with validation, error handling, and database migrations
  - **"Convention over configuration"** — Devin follows Spring Boot best practices for project structure, naming, and separation of concerns
  - **"Test coverage is built in"** — generating tests alongside the code ensures quality from the start, not as an afterthought

### Spotlight: Domain Decomposition Analysis

> *Inspired by: Code breakup based on business capability and gap analysis against industry standards*

Before extracting microservices, teams need to understand their domain boundaries. This spotlight has Devin analyze a codebase and produce a decomposition strategy aligned to business capabilities — the planning step that usually takes architects weeks.

#### Paste into Devin

```
Analyze uc-spring-boot-upgrade-microservice-extraction and produce a domain decomposition analysis. This Spring Boot monolith implements a social blogging platform.

Deliverables:
1. **Business Capability Map** — identify each bounded context (e.g., User Management, Content Publishing, Social Graph, Content Discovery) and map the current codebase packages, entities, and APIs to each capability
2. **Coupling Analysis** — for each bounded context pair, document the coupling points: shared database tables, direct method calls, shared models, and transitive dependencies. Rate each coupling as Low/Medium/High
3. **Extraction Priority Matrix** — rank each bounded context by extraction readiness (test coverage, coupling score, business value, team ownership alignment). Recommend which to extract first and why
4. **API Contract Gaps** — compare the existing REST API endpoints against RESTful design best practices (consistent naming, proper HTTP methods, pagination, error format, versioning). Document gaps and recommendations
5. **Target Architecture Diagram** — describe (in text/markdown) the target microservice architecture: which services exist, how they communicate (sync HTTP vs. async events), and what shared infrastructure they need

Write the analysis to `docs/DECOMPOSITION_ANALYSIS.md`. Open a PR.
```

#### Why Try This

- Shows Devin doing architectural analysis, not just code changes
- The business capability mapping is useful for teams evaluating microservice transitions
- The coupling analysis and priority matrix help teams make data-driven decomposition decisions
- Participants see that Devin can reason about architecture, not just implement features

- **Key Takeaways:**
  - **"Architecture before extraction"** — Devin analyzes domain boundaries, coupling, and readiness before writing any extraction code
  - **"Data-driven decomposition"** — the coupling analysis and priority matrix replace gut-feel decisions with structured assessment
  - **"API gap analysis"** — Devin evaluates existing APIs against standards, identifying inconsistencies that should be fixed before or during decomposition

---

## Track C: Feature Development & Testing

Track C demonstrates Devin as a day-to-day development partner. Participants will build features, add test coverage, and run E2E tests that discover and fix bugs.

> Follow the full lab instructions in the [general workshop guide](../../../workshops/general/README.md#track-c-feature-development--testing).

### Lab C1 — Add a Feature + PR Review Feedback

- **Module:** [New Feature Development](../../../labs/application-development/new-feature-development.md)
- **Repositories:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app), [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)
- **Objective:** Build a new feature from requirements, see PR Review in action, and iterate via comments

See [general workshop Lab C1](../../../workshops/general/README.md#lab-c1--add-a-feature--pr-review-feedback) for full instructions.

### Lab C2 — Add Test Coverage

- **Modules:** [Unit Testing](../../../labs/testing-qa/unit-testing.md) + [BDD Test Generation](../../../labs/testing-qa/bdd-test-generation.md)
- **Repositories:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction), [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app), [uc-bdd-test-generation-cucumber](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-cucumber)
- **Objective:** Increase test coverage with meaningful unit and BDD tests

See [general workshop Lab C2](../../../workshops/general/README.md#lab-c2--add-test-coverage) for full instructions.

### Lab C3 — Perform E2E Tests & Fix Issues

- **Modules:** [End-to-End Testing](../../../labs/testing-qa/end-to-end-testing.md) + [Fix Runtime Bug](../../../labs/application-development/fix-runtime-bug.md)
- **Repositories:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app), [uc-bdd-test-generation-cucumber](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-cucumber)
- **Objective:** Write and run E2E tests, discover issues through testing, and fix them

See [general workshop Lab C3](../../../workshops/general/README.md#lab-c3--perform-e2e-tests--fix-issues) for full instructions.

### Spotlight: Automated Data Anomaly Detection & Triage

> *Inspired by: Auto-detecting data anomalies, creating issues, performing root cause analysis, and fixing ingestion pipelines*

This spotlight shows Devin playing the role of a data reliability engineer — analyzing application data for anomalies, documenting findings as structured issues, performing root cause analysis, and fixing the underlying pipeline logic.

#### Paste into Devin

```
Analyze the data layer in uc-data-source-migration-jdbc-normalization for data quality anomalies. This Spring Boot service reads from legacy CDW (Corporate Data Warehouse) tables with known quality issues.

1. **Anomaly Detection:** Query the legacy seed data in `src/main/resources/data-legacy.sql` and the schema in `src/main/resources/schema-legacy.sql`. Identify anomalies: null values in required fields, date format inconsistencies, invalid status codes, orphaned records (foreign key violations), numeric values stored as strings with parsing risks, and duplicate or near-duplicate records.

2. **Issue Documentation:** For each anomaly found, create a structured issue entry in `docs/DATA_ANOMALY_REPORT.md` with: title, severity (Critical/High/Medium/Low), affected table and column, example bad records, business impact, and recommended fix.

3. **Root Cause Analysis:** For the top 3 most critical anomalies, trace through the code (LoanService.java, the repository layer, and the column mappings in `data/mappings/column_mappings.md`) to identify where the anomaly would cause a runtime failure or incorrect API response. Document the root cause.

4. **Fix:** Implement data validation in the service layer that catches these anomalies at ingestion time — add input validation, type coercion with error handling, and fallback defaults where appropriate. Add tests that verify the validation catches each anomaly type.

Open a PR with the anomaly report, root cause analysis, validation code, and tests.
```

#### Why Try This

- Shows Devin reasoning about data quality — a real concern in any system that ingests external or legacy data
- The anomaly detection → issue documentation → RCA → fix cycle mirrors how data engineering teams actually work
- Participants see Devin trace data quality problems through multiple layers (schema → mapping → service → API)
- The structured issue format demonstrates Devin producing actionable documentation, not just code

- **Key Takeaways:**
  - **"Detect → document → trace → fix"** — Devin follows the full data quality workflow, from finding anomalies to implementing validation that prevents them
  - **"Data issues have code root causes"** — Devin traces data quality problems through the application layers to find where they originate
  - **"Structured issue reporting"** — the anomaly report format is immediately useful for team triage and prioritization

### Spotlight: Codebase Knowledge Extraction & Gap Analysis

> *Inspired by: Generating application knowledge bases, performing gap analysis against standards, and producing implementation roadmaps*

This spotlight demonstrates Devin as a technical analyst — reading an entire codebase, producing a structured knowledge base, and identifying gaps against engineering best practices. This is the kind of assessment that typically takes a senior engineer a week; Devin can draft it in a session.

#### Paste into Devin

```
Perform a comprehensive technical assessment of timesheet-app. Produce the following deliverables:

1. **Application Knowledge Base** (`docs/KNOWLEDGE_BASE.md`):
   - Architecture overview (frontend framework, backend framework, database, key libraries)
   - Data model documentation (entities, relationships, key fields)
   - API surface map (all endpoints with methods, request/response shapes, authentication requirements)
   - Key business logic inventory (calculation rules, validation logic, state machines)
   - Integration points (external services, environment dependencies)
   - Build and deployment pipeline summary

2. **Engineering Standards Gap Analysis** (`docs/GAP_ANALYSIS.md`):
   Compare the codebase against these engineering best practices and document gaps:
   - **Code organization:** Consistent project structure, separation of concerns, no circular dependencies
   - **Error handling:** Centralized error handling, consistent error response format, proper HTTP status codes
   - **Testing:** Unit test coverage, integration tests, E2E tests, test isolation
   - **Security:** Input validation, authentication/authorization patterns, secrets management, dependency vulnerabilities
   - **API design:** RESTful conventions, pagination, filtering, versioning, documentation
   - **Observability:** Logging, health checks, metrics endpoints
   - **Documentation:** README completeness, API docs, inline code documentation

   For each gap, rate severity (Critical/High/Medium/Low) and estimate effort to remediate (Small/Medium/Large).

3. **Remediation Roadmap** (`docs/REMEDIATION_ROADMAP.md`):
   Prioritize the gaps into a phased plan: Phase 1 (quick wins — high impact, low effort), Phase 2 (important — high impact, medium effort), Phase 3 (polish — lower impact improvements). Include sample Devin prompts for each remediation item so the team can immediately start fixing gaps.

Open a PR with all three documents.
```

#### Why Try This

- Shows Devin producing the kind of technical documentation that accelerates onboarding and decision-making
- The gap analysis format is immediately useful for teams evaluating technical debt or preparing for audits
- The remediation roadmap with ready-to-use Devin prompts creates a self-service backlog
- Participants see Devin reasoning about engineering quality holistically, not just one dimension at a time

- **Key Takeaways:**
  - **"Instant technical knowledge base"** — Devin reads the entire codebase and produces structured documentation that would take days to write manually
  - **"Standards-based gap analysis"** — Devin evaluates the codebase against engineering best practices and quantifies the gaps
  - **"Actionable roadmap"** — the remediation plan includes effort estimates and ready-to-use Devin prompts, turning the analysis into immediate next steps

---

## Additional Challenges

Participants who finish early or want to explore further can try any challenge from the full [module catalog](../../../labs/). Recommended extras:

| Challenge | Module | Repo | Track | Difficulty |
|-----------|--------|------|-------|------------|
| Data Source Migration | [Data Source Migration](../../../labs/data-engineering/data-source-migration.md) | uc-data-source-migration-jdbc-normalization | B | Intermediate |
| Data Quality Validation | [Data Quality & Validation](../../../labs/data-engineering/data-quality-validation.md) | uc-data-source-migration-jdbc-normalization | B | Intermediate |
| Event-Driven SAST Pipeline | [Event-Driven SAST Remediation](../../../labs/security/event-driven-sast-remediation.md) | uc-cve-remediation-regulatory-compliance | A | Advanced |
| Monolith Decomposition (.NET) | [.NET Monolith Decomposition](../../../labs/migration-modernization/dotnet-monolith-decomposition.md) | modular-monolith-ddd | B | Advanced |
| Code Refactoring & Tech Debt | [Code Refactoring](../../../labs/architecture-design/code-refactoring-tech-debt.md) | Any | C | Intermediate |
| API Design Review | [API Design Review](../../../labs/architecture-design/api-design-review.md) | Any | C | Intermediate |
| API Documentation | [API Documentation](../../../labs/technical-documentation/api-documentation.md) | Any | C | Beginner |

## Suggested Formats

| Format | Recommended Approach |
|--------|---------------------|
| Full day | All 3 tracks: Track A (morning) + Track B (midday) + Track C (afternoon). Spotlight prompts as bonus activities during wait time |
| Half day | 2 tracks + 1-2 spotlight prompts that match audience interest |
| Short session | 1 full track + 1 spotlight prompt from another track |
| Sampler | Pick 1 lab from each track (e.g., A1 + B2 + C1) + 1 spotlight |
| Single lab | A1 (security) or C1 (feature dev) recommended for impact. Add a spotlight if time allows |

## Spotlight Prompts Summary

The spotlight prompts are industry-inspired bonus activities that can be used alongside or in place of standard labs:

| Spotlight | Track | Inspired By | Time |
|-----------|-------|-------------|------|
| [Comprehensive Security Reporting](#spotlight-comprehensive-security-reporting) | A | Automated SAST/DAST/SCA security analysis and remediation | 45 min |
| [API-Spec-Driven Microservice Generation](#spotlight-api-spec-driven-microservice-generation) | B | Microservice code generation from API specifications with 90% test coverage | 60 min |
| [Domain Decomposition Analysis](#spotlight-domain-decomposition-analysis) | B | Code breakup by business capability and gap analysis against standards | 45 min |
| [Automated Data Anomaly Detection & Triage](#spotlight-automated-data-anomaly-detection--triage) | C | Auto-detecting data anomalies, root cause analysis, and pipeline fixes | 60 min |
| [Codebase Knowledge Extraction & Gap Analysis](#spotlight-codebase-knowledge-extraction--gap-analysis) | C | Generating application knowledge bases, gap analysis, and remediation roadmaps | 45 min |

## Repos Required

### Track A (Security & Issue Triage)
- [ ] uc-cve-remediation-regulatory-compliance
- [ ] timesheet-app
- [ ] quickapp-microservices (optional, for Lab A2 Option B)

### Track B (Modernization)
- [ ] uc-spring-boot-upgrade-microservice-extraction
- [ ] petclinic-angular
- [ ] ts-angular-realworld

### Track C (Feature Development & Testing)
- [ ] timesheet-app
- [ ] uc-spring-boot-upgrade-microservice-extraction (optional, for Lab C1 Option B)
- [ ] uc-bdd-test-generation-cucumber (optional, for Lab C2 Option C / Lab C3 Option B)

### Spotlight Prompts (additional)
- [ ] uc-data-source-migration-jdbc-normalization (for Data Anomaly Detection spotlight)

## Use Case Inspiration Notes

The spotlight prompts in this event were inspired by the following enterprise patterns submitted by attendees. Some themes were too domain-specific for a general audience and were generalized or omitted:

| Submitted Theme | Included? | How It Appears |
|-----------------|-----------|----------------|
| Microservice code generation from API specs (controllers, services, mappers, validation, config, JUnit for 90% coverage) | Yes | **API-Spec-Driven Microservice Generation** spotlight in Track B |
| Auto-detect data anomalies, issue creation, RCA, pipeline fixes | Yes | **Automated Data Anomaly Detection & Triage** spotlight in Track C |
| Application knowledge base generation, gap analysis, implementation roadmaps | Yes (generalized) | **Codebase Knowledge Extraction & Gap Analysis** spotlight in Track C |
| Control/config file generation from feed files and copybooks | No | Too specialized (mainframe/COBOL domain) for a general audience. See the [COBOL modules](../../../labs/migration-modernization/cobol-to-java.md) for dedicated labs |
| Payment payload gap analysis vs. industry standard, business capability decomposition | Yes (generalized) | **Domain Decomposition Analysis** spotlight in Track B (business capability mapping). API contract validation is covered in the standard API Design Review module |
| Automated security remediation, SAST/DAST/SCA reports | Yes | **Comprehensive Security Reporting** spotlight in Track A |

## Context

- **Audience:** General engineering teams — this workshop is designed to be relevant regardless of tech stack specialty
- **Focus:** Breadth across security, modernization, and feature development — demonstrating Devin's versatility
- **Spotlight prompts:** Industry-inspired bonus activities that add depth without narrowing the audience
- **Devin value themes woven throughout:**
  - Autonomous, off-machine execution — kick off sessions and move on
  - Parallel sessions for scaling repetitive work across repos
  - Ask Devin for requirement gathering and task scoping before sessions
  - Knowledge and Playbooks for building reusable team context
  - PR Review agent + CI checks as automatic feedback loops
  - Scheduled sessions for continuous code hygiene
  - Long-running task handling — Devin works while you're in meetings

## Devin Features Checklist

Encourage participants to track their progress on the [Devin Features Appendix](../../../labs/devin-features/README.md) throughout the session.
