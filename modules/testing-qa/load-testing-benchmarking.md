# Load Testing & Benchmarking

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Challenge

Create load test suites using k6 or Gatling, define performance baselines, and analyze results. Tests should cover key API endpoints and user flows.

## Target Outcomes

- Load test suite covering critical API endpoints
- Performance baseline metrics documented
- Load test report with throughput, latency, and error rate analysis
- PR with load test scripts and configuration

## What Participants Will Learn

- How Devin analyzes an API surface to identify critical endpoints for load testing
- How Devin selects and configures performance testing tools for a given stack
- Devin's ability to author realistic load test scenarios with ramp-up patterns
- How to interpret load test results and set performance thresholds

## Devin Features Exercised

- API analysis
- Performance test authoring
- Results analysis
- PR creation

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express API with authentication, CRUD operations, and report generation — ideal for k6 load testing.

### Step 1: Paste into Devin

> Create a k6 load test suite for timesheet-app. Write load test scripts that cover: authentication flow, client CRUD operations, work entry creation, and report generation (including CSV/PDF export). Define realistic load profiles with ramp-up, sustained load, and ramp-down stages. Include performance thresholds (p95 latency < 500ms, error rate < 1%). Document the baseline results. Open a PR with the k6 scripts, a README explaining how to run them, and the baseline performance report.

### Step 2: Research with Ask Devin

- *"What are the critical API endpoints in timesheet-app that should be load tested? Which endpoints are likely to be the bottlenecks?"*
- *"What realistic load profile should we use? How many concurrent users and requests per second would stress this Express.js app?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the API routes and their dependencies. Pay attention to the report generation endpoints — CSV and PDF export involve I/O and may be performance-sensitive.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the load scenarios realistic? Do they handle authentication tokens correctly across virtual users?
- **Leave a comment** asking Devin to add a stress test scenario that pushes beyond the normal load profile
- **Watch Devin respond** and push a follow-up commit

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot monolith with REST and GraphQL APIs — suitable for Gatling or k6 load testing across both API surfaces.

### Step 1: Paste into Devin

> Create a Gatling load test suite for uc-spring-boot-upgrade-microservice-extraction. Write simulation scripts that cover: user authentication, article CRUD operations, comment creation, and tag listing. Include both REST API and GraphQL endpoint scenarios. Define load profiles with graduated ramp-up (10 to 100 users over 2 minutes) and sustained load. Set performance thresholds and document baseline results. Open a PR with the Gatling simulations, build configuration, and a baseline performance report.

### Step 2: Research with Ask Devin

- *"What REST and GraphQL endpoints exist in this application? Which ones involve the most database queries and would be bottlenecks under load?"*
- *"Should we test REST and GraphQL endpoints separately or in a combined scenario that simulates realistic user behavior?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand both the REST and GraphQL API surfaces. Identify endpoints that trigger complex queries (e.g., article feed with pagination, user profile with articles).

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the Gatling simulations properly parameterized? Do they reuse authentication tokens efficiently?
- **Leave a comment** asking Devin to add a spike test scenario that simulates a sudden burst of traffic
- **Watch Devin respond** and push a follow-up commit
