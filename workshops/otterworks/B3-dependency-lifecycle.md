# Lab: Dependency Lifecycle Campaign

## Objective

Use Devin's divide-and-conquer pattern to audit all OtterWorks services for outdated, end-of-life, or vulnerable dependencies in parallel — one child session per service, each producing its own upgrade PR.

## Why This Matters

OtterWorks has 10 backend services plus an API gateway across 10 different language ecosystems. Manually checking each one for outdated dependencies, EOL runtimes, and license issues would take a developer days. This is the canonical "capacity-constrained" use case: valuable work that teams defer because no individual has time to do it across the entire monorepo.

Devin's child agent pattern turns this from a multi-day slog into a parallel campaign that completes in an hour.

## The Campaign

### Step 1 — Scope the Work (10 min)

First, understand what you are auditing. Ask Devin:

```
Read the OtterWorks ARCHITECTURE.md and list every
backend service with its primary language, runtime
version, and package manager. Include the two frontends.
Format as a table with columns: Service, Language,
Runtime Version, Package Manager, Dependency File.
```

This gives you the target list for the campaign. You should see services spanning Go, Java, Rust, Python, Node.js, Kotlin, Scala, Ruby, and C#.

### Step 2 — Create the Playbook (10 min)

Before spawning child agents, define the methodology each one should follow. Create a Playbook:

```
name: dependency-audit-per-service
description: Audit a single service for outdated, EOL,
  or vulnerable dependencies and create an upgrade PR.

steps:
  - Read the service's dependency file (package.json,
    requirements.txt, pom.xml, Gemfile, go.mod, etc.)
  - Check each dependency for:
    a) Known CVEs (run the language-appropriate scanner)
    b) Major version staleness (more than 1 major behind)
    c) EOL runtime (e.g., Java 8, Python 3.7, Node 16)
    d) License concerns (AGPL, GPL in non-GPL project)
  - Categorize findings as CRITICAL (CVE/EOL), HIGH
    (major version behind), or LOW (minor behind)
  - For CRITICAL findings: upgrade the dependency and
    run the service's test suite to verify
  - For HIGH findings: document the upgrade path and
    any breaking changes expected
  - Include a findings table and any upgrades applied
    in the PR description
```

### Step 3 — Launch the Campaign (15 min)

Now use Devin to spawn child sessions. Ask:

```
Run the dependency-audit-per-service playbook against
these OtterWorks services in parallel, one child session
per service:

1. services/api-gateway (Go)
2. services/auth-service (Java)
3. services/report-service (Java)
4. services/file-service (Rust)
5. services/document-service (Python)
6. services/search-service (Python)
7. services/collab-service (Node.js)
8. services/notification-service (Kotlin)
9. services/analytics-service (Scala)
10. services/admin-service (Ruby)
11. services/audit-service (C#)

Each child should work on the otterworks repo, read only
its assigned service directory, and open a separate PR
with findings and fixes.
```

### Step 4 — Monitor and Review (20 min)

Watch the child sessions work in parallel:
- Each child reads its service's dependency file
- Each runs the appropriate scanner (Trivy, npm audit, pip-audit, etc.)
- Each categorizes findings and applies CRITICAL fixes
- Each opens a PR

Review the PRs: Are the findings accurate? Did the upgrades break tests? Are the categorizations reasonable?

## What Done Looks Like

- [ ] Scoped the campaign: listed all services with their language, runtime, and dependency file
- [ ] Created a Playbook encoding the dependency audit methodology
- [ ] Launched parallel child sessions (at least 3 services for time-boxed workshops)
- [ ] Child sessions produced PRs with findings and fixes
- [ ] Reviewed at least 2 child session PRs for accuracy and quality
- [ ] Can explain the divide-and-conquer pattern: parent scopes → playbook defines methodology → children execute in parallel → PRs for review

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Start Small

If time is limited, pick 3 diverse services (e.g., report-service/Java, collab-service/Node.js, search-service/Python) instead of all 11. The pattern is the same regardless of N.

### Hint 2 — Known Findings

The report-service is on Java 8 / Spring Boot 2.5 with Guava 28 (multiple CVEs) — this is the most findings-rich service. The collab-service has a vulnerable lodash version. The search-service has a vulnerable urllib3.

### Hint 3 — The Scheduled Angle

After the campaign, discuss: this same Playbook + child agent pattern could run on a weekly schedule. Every Monday, Devin audits all services and opens PRs for anything that has drifted. That is the move from campaign to scheduled automation.

## Key Takeaways

- Divide-and-conquer turns weeks of work into parallel hours — one child agent per target, each producing an independent PR
- Playbooks ensure consistency across child sessions — every service gets the same thorough audit
- The pattern scales linearly: 11 services today, 50 services tomorrow, same approach
- Dependency lifecycle is a "capacity-constrained" use case — teams defer it, Devin automates it
- Campaign → Schedule: run this as a cron job and dependencies never drift again

## Time Budget

- ~45-60 minutes for the full campaign (scoping + playbook + 3-5 child sessions + review)
- Advanced participants: launch all 11 services and compare findings across the polyglot ecosystem
