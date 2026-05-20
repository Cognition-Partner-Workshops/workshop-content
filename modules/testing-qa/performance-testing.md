# Performance Testing & Optimization

<a id="table-of-contents"></a>
## Table of Contents
- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Prerequisites](#prerequisites)
- [timesheet-app](#timesheet-app)
- [calcom](#calcom)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [Going Further](#going-further)

## Repositories

- [timesheet-app](#timesheet-app)
- [calcom](#calcom)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

<a id="challenge"></a>
## Challenge

Profile, benchmark, and optimize application performance. This challenge covers identifying performance bottlenecks, adding benchmarks, optimizing database queries, and improving response times. Performance optimization requires deep codebase understanding and iterative measurement — Devin can profile, diagnose, and fix in a tight loop on its own VM.

<a id="quick-start"></a>
## Quick Start

Paste this into a new Devin session to get started immediately:

```
Run timesheet-app locally and profile the API endpoints.
Identify the slowest endpoints and database queries. Add
request timing middleware to measure response times.
Optimize the top 3 slowest operations (query optimization,
adding indexes, caching). Create a simple benchmark script
to measure before/after performance.
```

<a id="target-outcomes"></a>
## Target Outcomes

- Performance bottlenecks identified with evidence (profiling data, slow query logs, etc.)
- At least one optimization implemented with before/after measurements
- Benchmark or load test script created for regression testing
- PR with optimization changes and performance evidence

<a id="what-participants-will-learn"></a>
## What Participants Will Learn

- How Devin profiles and measures application performance
- How Devin identifies N+1 queries, slow endpoints, and memory issues
- How Devin implements optimizations (caching, query optimization, indexing)
- The importance of measuring before and after optimization
- How event-driven triggers can catch performance regressions when they are introduced

<a id="devin-features-exercised"></a>
## Devin Features Exercised

- Runtime application profiling on Devin's VM
- Database query analysis and optimization
- Code optimization with measured evidence
- PR creation with before/after performance data

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

## Prerequisites

The application should be running locally for profiling. See [runtime-resources.md](../../shared/runtime-resources.md) for hosted instance details.

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express backend with SQLite — good candidate for query optimization and API response time improvement.

### Step 1: Paste into Devin

```
Run timesheet-app locally and profile the API endpoints.
Identify the slowest endpoints and database queries. Add
request timing middleware to measure response times.
Optimize the top 3 slowest operations (query optimization,
adding indexes, caching). Create a simple benchmark script
to measure before/after performance.
```

### Step 2: Research with Ask Devin

- *"Are there any N+1 query patterns in the work entries or client listing endpoints?"*
- *"What SQLite indexes would most improve query performance for the typical access patterns?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the data access patterns and API usage. Identify which endpoints are most likely to be called frequently.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the optimizations safe? Do they change any API contracts?
- **Leave a comment** asking Devin to add a specific index or caching strategy you think would help

### Key Takeaways

- Devin measures before and after — optimizations are backed by data, not assumptions
- N+1 queries are a common performance issue that Devin identifies through SQL log analysis
- Adding indexes and caching must be done carefully to avoid correctness issues

---

## <a id="calcom"></a>calcom

**Repository:** [calcom](https://github.com/Cognition-Partner-Workshops/calcom)

Complex Next.js application with Prisma ORM — many opportunities for database query optimization and frontend performance improvement.

### Step 1: Paste into Devin

```
Start calcom locally with `yarn dev`. Profile the booking
page load time and the availability check API. Identify
slow database queries using Prisma query logging. Optimize
the top 3 performance bottlenecks. Create before/after
measurements.
```

### Step 2: Research with Ask Devin

- *"What are the most expensive Prisma queries in calcom? Are there any missing database indexes?"*
- *"Are there any React components that re-render unnecessarily on the booking page?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the booking engine and availability calculation logic. These are likely the most performance-critical paths.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the optimizations maintain data correctness?
- **Leave a comment** asking Devin to benchmark a specific high-traffic endpoint

### Key Takeaways

- Prisma query logging reveals the actual SQL generated by the ORM — often surprising for complex includes/relations
- Frontend performance (component re-renders, bundle size) is a separate concern from API performance
- Availability calculation is the hot path in scheduling apps — optimizations here have the highest impact

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot monolith with MyBatis and SQLite — opportunities for query optimization, connection pooling, and caching.

### Step 1: Paste into Devin

```
Run uc-spring-boot-upgrade-microservice-extraction locally
and profile the article listing and feed endpoints. Enable
SQL logging to identify slow queries. Add Spring Boot
Actuator for metrics. Optimize the top 3 performance
bottlenecks (query optimization, caching with Spring
Cache, pagination improvements).
```

### Step 2: Research with Ask Devin

- *"Are there any N+1 query patterns in the article feed or comment loading?"*
- *"Would adding Redis or in-memory caching significantly improve the article feed performance?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the data access patterns. Identify which MyBatis mappers could benefit from query optimization or result caching.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are cache invalidation strategies correct? Do optimized queries return the same results?
- **Leave a comment** asking Devin to add a JMH benchmark for a specific hot path

### Key Takeaways

- Spring Boot Actuator provides built-in metrics that Devin uses for before/after comparison
- MyBatis XML mappers often contain suboptimal joins that benefit from index hints and restructuring
- Cache invalidation is the hard part — review Devin's strategy carefully

---

<a id="going-further"></a>
## Going Further

### Event-Driven Performance Regression Detection

When CI detects response time increases (via benchmark tests in the pipeline), automatically trigger a Devin session to diagnose and fix:

```
CI benchmark detects p95 latency increase > 20%
    → webhook fires
    → Devin session starts: "Profile the /api/articles
       endpoint. p95 latency increased from 120ms to
       180ms. Identify and fix the regression."
    → Devin opens a PR with the optimization
```

### Scheduled Performance Audits

Configure a weekly Devin session to profile the application and report on performance trends. This catches gradual degradation that individual PRs don't reveal.

### Devin Review for Performance Impact

Enable Devin Review to flag PRs that introduce potentially expensive operations — missing pagination, new N+1 patterns, removed caching — before they reach production.
