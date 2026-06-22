# Load Testing & Benchmarking

<a id="table-of-contents"></a>
## Table of Contents
- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [timesheet-app](#timesheet-app)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [Going Further](#going-further)

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

<a id="challenge"></a>
## Challenge

Create load test suites using k6 or Gatling, define performance baselines, and analyze results. Tests should cover key API endpoints and user flows. Load testing is frequently deferred because writing realistic scenarios is tedious — Devin analyzes the API surface and generates parameterized load scripts so engineers focus on interpreting results and setting thresholds.

<a id="quick-start"></a>
## Quick Start

Paste this into a new Devin session to get started immediately:

```
Create a k6 load test suite for timesheet-app. Write
load test scripts that cover: authentication flow, client
CRUD operations, work entry creation, and report
generation (including CSV/PDF export). Define realistic
load profiles with ramp-up, sustained load, and ramp-down
stages. Include performance thresholds (p95 latency
< 500ms, error rate < 1%). Document the baseline results.
```

<a id="target-outcomes"></a>
## Target Outcomes

- Load test suite covering critical API endpoints
- Performance baseline metrics documented
- Load test report with throughput, latency, and error rate analysis
- PR with load test scripts and configuration

<a id="what-participants-will-learn"></a>
## What Participants Will Learn

- How Devin analyzes an API surface to identify critical endpoints for load testing
- How Devin selects and configures performance testing tools for a given stack
- Devin's ability to author realistic load test scenarios with ramp-up patterns
- How to interpret load test results and set performance thresholds
- How scheduled load testing can establish and track performance baselines over time

<a id="devin-features-exercised"></a>
## Devin Features Exercised

- API surface analysis for load test targeting
- Performance test authoring (k6, Gatling)
- Results analysis and threshold configuration
- PR creation with baseline documentation

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express API with authentication, CRUD operations, and report generation — ideal for k6 load testing.

### Step 1: Paste into Devin

```
Create a k6 load test suite for timesheet-app. Write
load test scripts that cover: authentication flow, client
CRUD operations, work entry creation, and report
generation (including CSV/PDF export). Define realistic
load profiles with ramp-up, sustained load, and ramp-down
stages. Include performance thresholds (p95 latency
< 500ms, error rate < 1%). Document the baseline results.
```

### Step 2: Research with Ask Devin

- *"What are the critical API endpoints in timesheet-app that should be load tested? Which endpoints are likely to be the bottlenecks?"*
- *"What realistic load profile should we use? How many concurrent users and requests per second would stress this Express.js app?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the API routes and their dependencies. Pay attention to the report generation endpoints — CSV and PDF export involve I/O and may be performance-sensitive.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the load scenarios realistic? Do they handle authentication tokens correctly across virtual users?
- **Leave a comment** asking Devin to add a stress test scenario that pushes beyond the normal load profile
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- k6 scripts are JavaScript-based and version-controllable — they belong in the repo alongside the application code
- Realistic load profiles include ramp-up, sustained load, and ramp-down — not just peak traffic
- Performance thresholds (p95 latency, error rate) turn load tests into automated quality gates

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot monolith with REST and GraphQL APIs — suitable for Gatling or k6 load testing across both API surfaces.

### Step 1: Paste into Devin

```
Create a Gatling load test suite for
uc-spring-boot-upgrade-microservice-extraction. Write
simulation scripts that cover: user authentication,
article CRUD operations, comment creation, and tag
listing. Include both REST API and GraphQL endpoint
scenarios. Define load profiles with graduated ramp-up
(10 to 100 users over 2 minutes) and sustained load.
Set performance thresholds and document baseline results.
```

### Step 2: Research with Ask Devin

- *"What REST and GraphQL endpoints exist in this application? Which ones involve the most database queries and would be bottlenecks under load?"*
- *"Should we test REST and GraphQL endpoints separately or in a combined scenario that simulates realistic user behavior?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand both the REST and GraphQL API surfaces. Identify endpoints that trigger complex queries (e.g., article feed with pagination, user profile with articles).

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the Gatling simulations properly parameterized? Do they reuse authentication tokens efficiently?
- **Leave a comment** asking Devin to add a spike test scenario that simulates a sudden burst of traffic
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- Dual API surfaces (REST + GraphQL) need separate load profiles — GraphQL queries can be arbitrarily complex
- Gatling's Scala DSL produces readable simulation scripts that serve as performance documentation
- Baseline metrics establish the "normal" — future regressions are detected by comparing against the baseline

---

<a id="going-further"></a>
## Going Further

### Scheduled Performance Regression Detection

Configure a weekly Devin session to run load tests against the latest build and compare results to the established baseline:

- Run the load test suite and collect metrics
- Compare p95 latency, throughput, and error rates against the previous baseline
- If any metric regresses beyond the threshold, open a PR flagging the regression with diagnostic details
- Update the baseline if the new results represent an intentional change

### Event-Driven Load Testing in CI

Trigger load tests automatically when performance-sensitive code changes:

```
PR modifies database queries or API controllers
    → CI triggers a focused load test on affected endpoints
    → Devin session starts if thresholds are breached:
       "p95 latency increased 30% on /api/articles.
        Profile the endpoint and optimize."
```

### Devin Review for Performance Impact

Enable Devin Review to flag PRs that introduce potentially expensive operations (new database queries, removed caching, added loops) without corresponding load test updates.
