# Incident Response & Triage

## Table of Contents

- [Quick Start](#quick-start)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Repositories](#repositories)
  - [timesheet-app](#timesheet-app)
  - [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

Pick a repo below, copy the **Step 1** prompt into a new Devin session, and let Devin analyze the application's failure modes and generate incident response materials. No prerequisites beyond repo access.

---

## Challenge

Create incident response runbooks and triage automation. This exercises Devin's ability to analyze an application's failure modes, create structured response procedures, and set up alerting rules and GitHub Issue templates for incident management.

## Target Outcomes

- Incident response runbook (RUNBOOK.md) covering common failure scenarios
- GitHub Issue templates for incident reporting (severity levels, impact, timeline)
- Alerting rules or health check scripts for early detection
- On-call checklist and escalation procedures
- PR with incident response materials

## What Participants Will Learn

- How Devin analyzes application architecture to identify failure modes
- How Devin creates structured runbooks from codebase analysis
- Incident management best practices (severity classification, communication, post-mortems)
- How to automate incident detection with health checks and alerting

## Devin Features Exercised

- Codebase analysis for failure mode identification
- Documentation generation (runbooks, templates)
- GitHub Issue template creation
- Health check script authoring
- PR creation

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## Repositories

### <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express application — create incident response materials for a web application.

#### Step 1: Paste into Devin

```
Create incident response materials for timesheet-app: (1) Analyze the application to identify common failure modes (database issues, API errors, memory leaks, dependency failures), (2) Create a RUNBOOK.md with step-by-step response procedures for each failure mode, (3) Add GitHub Issue templates for P1/P2/P3/P4 incidents with fields for impact, timeline, and resolution, (4) Create a health check script that tests all critical endpoints.
```

#### Step 2: Research with Ask Devin

- *"What are the most likely production failure modes for a Node.js/Express + SQLite application?"*
- *"What monitoring alerts should be configured to detect issues before users report them?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the application's dependencies and potential failure points. Use this to create comprehensive runbook entries.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the runbook procedures specific and actionable? Would an on-call engineer be able to follow them?
- **Leave a comment** asking Devin to add a post-mortem template and a status page configuration

---

### <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot monolith — create incident response materials for a Java application with database and API layers.

#### Step 1: Paste into Devin

```
Create incident response materials for uc-spring-boot-upgrade-microservice-extraction: (1) Analyze the application architecture to identify failure modes (database connection exhaustion, JVM heap issues, API timeouts, authentication failures), (2) Create a RUNBOOK.md with diagnostic commands and recovery procedures for each scenario, (3) Add GitHub Issue templates for incidents with Spring Boot-specific diagnostic fields (thread dumps, heap dumps, Actuator endpoints), (4) Create a diagnostic script that collects application state via Actuator endpoints.
```

#### Step 2: Research with Ask Devin

- *"What JVM-specific failure modes should the runbook cover (OOM, thread deadlock, GC pauses)?"*
- *"What Spring Boot Actuator endpoints are most useful during incident triage?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the application's configuration and dependencies. Identify which components are most likely to fail under load.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the diagnostic commands correct for Spring Boot? Would an on-call engineer find them useful?
- **Leave a comment** asking Devin to add a load testing script that can help reproduce performance-related incidents

---

## Key Takeaways

- Devin generates runbooks grounded in the actual codebase — referencing real endpoints, configuration files, and dependency chains rather than generic templates
- Incident response materials are high-value, low-urgency work that teams consistently deprioritize — a strong fit for Devin to handle
- Health check scripts and Issue templates created by Devin provide a starting point that teams can refine based on real production experience

## Going Further

Incident response is where **incident-response automation** (alert → Devin investigates) delivers the most value (see [When to Use Devin → Event-Driven](../../reference/general-themes/when-to-use-devin.md)):

- **Alert-triggered investigation** — Connect PagerDuty, OpsGenie, or Azure Monitor alerts to the Devin API. When an alert fires, Devin automatically queries logs and metrics via MCP (Datadog, CloudWatch), correlates the failure with recent deployments, and posts a triage summary to the incident channel — before the on-call engineer finishes reading the alert
- **Automated runbook execution** — When Devin detects a known failure pattern (e.g., database connection exhaustion), it can follow the runbook it generated: verify the symptom, apply the documented remediation (e.g., restart the connection pool), and confirm recovery — opening a PR for any code-level fixes
- **Post-incident documentation** — After an incident is resolved, trigger a Devin session to draft a post-mortem from the incident timeline, Slack thread, and deployment logs
