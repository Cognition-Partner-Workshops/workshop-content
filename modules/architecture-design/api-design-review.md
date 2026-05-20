# API Design Review

## Table of Contents

- [Quick Start](#quick-start)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Repositories](#repositories)
  - [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
  - [timesheet-app](#timesheet-app)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

1. Pick a repository below
2. Paste the sample prompt into Devin
3. Observe how Devin audits the API surface and generates a review report with actionable findings
4. Review the PR and leave feedback — Devin will iterate on your comments

---

## Challenge

Evaluate REST and GraphQL APIs against design standards — covering naming conventions, versioning, error handling, pagination, and documentation — then produce a review report with actionable recommendations and implementation PRs for critical issues.

## Target Outcomes

- API design review report covering naming, versioning, error handling, and pagination
- List of API endpoints with conformance assessment
- Recommended fixes with implementation PRs for critical issues
- Updated or generated OpenAPI/Swagger documentation

## What Participants Will Learn

- How Devin maps and audits an API surface across REST and GraphQL
- How Devin evaluates API design against industry best practices
- Devin's ability to generate or update API documentation (OpenAPI/Swagger)
- How to use Devin for systematic design review workflows
- How the **PR feedback loop** works — reviewers comment, Devin iterates, CI re-runs (see [Collaboration Model](../../shared/general-themes/collaboration-model.md))

## Devin Features Exercised

- API surface analysis
- Design pattern evaluation
- Documentation generation
- PR creation with implementation fixes
- **Devin Review** — can be configured to check API design standards on every future PR (see [Platform Capabilities → Devin Review](../../shared/general-themes/platform-capabilities.md#devin-review))

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## Repositories

### <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot monolith exposing both REST controllers and a GraphQL API via the DGS framework — an ideal target for cross-style API design review.

#### Step 1: Paste into Devin

```text
Perform an API design review of uc-spring-boot-upgrade-microservice-extraction. This app exposes both REST endpoints and a GraphQL API (DGS framework). Audit the full API surface for: (1) REST naming conventions and HTTP method usage, (2) error handling consistency — are errors returned in a standard format across both REST and GraphQL? (3) pagination support, (4) input validation patterns, and (5) missing or outdated documentation. Produce a review report in `docs/api-review.md` with findings and severity ratings. Generate or update OpenAPI/Swagger documentation for the REST endpoints.
```

#### Step 2: Research with Ask Devin

- *"What REST endpoints does uc-spring-boot-upgrade-microservice-extraction expose, and do they follow RESTful naming conventions?"*
- *"How does the GraphQL schema compare to the REST API — are there capabilities available in one but not the other?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand both the REST controller structure and GraphQL schema. Identify any inconsistencies between the two API surfaces in terms of naming, input validation, or error handling.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the review report identify real design issues, or just stylistic preferences?
- **Leave a comment** asking Devin to implement a fix for the most critical API design issue identified in the report

---

### <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Express REST API with route handlers for timesheet management — a focused target for evaluating REST API design quality in a Node.js context.

#### Step 1: Paste into Devin

```text
Perform an API design review of timesheet-app. Audit all Express route handlers for: (1) RESTful naming conventions and HTTP method correctness, (2) error handling — are errors returned with consistent status codes and response shapes? (3) input validation — is request data validated before processing? (4) pagination and filtering support for list endpoints, and (5) API documentation. Produce a review report in `docs/api-review.md` with findings rated by severity. Generate an OpenAPI/Swagger spec for the existing endpoints.
```

#### Step 2: Research with Ask Devin

- *"What are all the API routes defined in timesheet-app? Do they follow consistent naming and HTTP method conventions?"*
- *"How does timesheet-app handle errors — is there a centralized error handler, or do individual routes handle errors differently?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the Express route structure and middleware chain. Identify whether there is centralized error handling or if each route handles errors independently.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the generated OpenAPI spec accurately describe the existing API?
- **Leave a comment** asking Devin to implement consistent error response formatting across all routes

---

## Key Takeaways

- Devin can systematically audit an API surface against design standards and produce structured review reports with severity ratings
- API documentation generation (OpenAPI/Swagger) is a natural byproduct of the review process
- The PR feedback loop lets reviewers direct Devin to fix specific findings from the review report
- Devin Review can be configured to enforce API design standards on every future PR, catching regressions early
- Cross-style reviews (REST vs. GraphQL) reveal inconsistencies that single-style reviews miss

---

## Going Further

- **Scheduled API audits** — run Devin on a weekly schedule to audit API surfaces for new inconsistencies, missing documentation, and design drift, then open remediation PRs (see [Platform Capabilities → Scheduled Sessions](../../shared/general-themes/platform-capabilities.md#scheduled-sessions))
- **Devin Review for API standards** — enable Devin Review with custom rules to check every PR for API design compliance (naming, error handling, pagination) automatically (see [Platform Capabilities → Devin Review](../../shared/general-themes/platform-capabilities.md#devin-review))
- **Parallel API reviews across services** — use child agents to audit API design across multiple microservices simultaneously, each producing a standardized review report (see [Platform Capabilities → Child Agents](../../shared/general-themes/platform-capabilities.md#child-agents-divide-and-conquer))
