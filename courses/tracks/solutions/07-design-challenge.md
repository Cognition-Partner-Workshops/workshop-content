# 7. Design Challenge: End-to-End Engagement Architecture

This comprehensive exercise mirrors a real engagement architecture proposal. You must design the full Devin integration architecture for a client scenario.

## Scenario

A systems integrator client has the following environment:
- **80 microservices** across 4 product teams (20 services each), deployed on Kubernetes
- **3 priority initiatives:**
  1. Automated security remediation — SAST findings should be fixed automatically when detected in CI
  2. Quarterly framework upgrades — Spring Boot 2.7 → 3.x across all 80 services
  3. Incident response — when a PagerDuty alert fires, Devin should investigate, triage, and implement a fix or escalate
- **Existing tools:** GitHub (repos + CI), SonarQube (SAST), PagerDuty (alerting), Jira (tickets), Datadog (observability), Confluence (docs)
- **Constraints:** Least-privilege access, no direct production access, all changes through PRs, existing CI/CD pipeline must be preserved

## What You Should Produce

**1. Automation Topology Diagram (describe in text)**

Design the trigger architecture for each initiative:
- What events fire which automations?
- What safeguards are on each automation?
- Where do parent-child patterns apply?
- What is the escalation path for each?

**2. Configuration List**

List every component of the shared context layer you would configure:
- Git connections (which repos/orgs)
- Environment blueprint (what runtimes/tools)
- Knowledge notes (what standards)
- Playbooks (what procedures)
- MCP servers (which integrations)
- Secrets (what credentials)
- Automations (what triggers)

**3. Automation Rules**

For each automation, specify:
- Trigger (event source + conditions)
- Prompt template (what Devin does)
- Safeguards (invocation limits, ACU caps, escalation)
- Expected output (PR, issue, report)

---

## Reference Answer

**1. Automation Topology**

```
Initiative 1: Security Remediation (Event-Driven)
─────────────────────────────────────────────────
SonarQube scan → GitHub check_run (conclusion: failure)
    ↓ Devin Automation (trigger: check_run fail on "sonar-scan")
Devin Session
    ├── Reads findings from check run
    ├── Triages by severity (CRITICAL, HIGH)
    ├── Remediates in correct manifest/source
    ├── Runs affected tests
    └── Pushes fix → re-scan converges to green
Safeguards: 3 invocations/PR/hour, 50 ACU/session, escalate after 2

Initiative 2: Quarterly Framework Upgrade (Campaign)
────────────────────────────────────────────────────
Manual trigger: Engineer starts parent session with list
    ↓ Parent Agent
Parent analyzes 80 services, creates upgrade playbook
    ├── Spawns Child 1 → service-a → PR
    ├── Spawns Child 2 → service-b → PR
    ├── ... (10 concurrent, 80 total)
    └── Monitors, escalates failures
Safeguards: 10 concurrent children, 100 ACU/child,
            parent summarizes progress weekly

Initiative 3: Incident Response (Event-Driven)
──────────────────────────────────────────────
PagerDuty alert → webhook → Devin Automation
    ↓ Devin Session
Devin Session
    ├── Queries Datadog (logs, traces, metrics) via MCP
    ├── Identifies affected service
    ├── Reads recent commits for potential cause
    ├── Implements fix (if root cause is clear)
    │   └── Pushes PR for review
    └── Escalates to Jira (if complex or unclear)
Safeguards: 1 invocation/alert, 30 ACU/session,
            escalate if fix not found within 15 min
```

**2. Configuration List**

| Component | Configuration |
|-----------|--------------|
| **Git connections** | GitHub org (all 80 service repos + shared libraries) |
| **Environment** | Java 21, Maven, Node.js 20, Docker, kubectl, Helm, SonarQube CLI, Trivy |
| **DeepWiki** | Indexed for 80 service repos — typically provides architecture context for incident investigation and upgrade compatibility (coverage depends on repo structure) |
| **Knowledge** | Java coding standards, Spring Boot 3 migration patterns, error handling conventions, microservice boundaries, API versioning policy |
| **Playbooks** | `!spring-boot-upgrade` (upgrade procedure), `!security-remediate` (scan-fix-verify), `!incident-triage` (investigate-fix-escalate) |
| **MCP servers** | Jira (tickets), Datadog (logs/traces), Confluence (runbooks), PagerDuty (incident context) |
| **Secrets** | `SONAR_TOKEN`, `JIRA_API_TOKEN`, `DATADOG_API_KEY`, `PAGERDUTY_API_KEY`, `GITHUB_TOKEN` (org), `K8S_KUBECONFIG` (read-only, staging only) |
| **Automations** | Security scan failure → remediation, PagerDuty alert → incident response, Weekly dependency audit (scheduled) |

**3. Automation Rules**

| Automation | Trigger | Prompt | Safeguards | Output |
|-----------|---------|--------|------------|--------|
| Security Remediation | `check_run` fail on "sonar-scan" in any of 80 repos | "Read findings, triage HIGH+CRITICAL, remediate, run tests, push fix" | 3/PR/hr, 50 ACU, escalate after 2 | PR with fix + re-scan |
| Incident Response | PagerDuty webhook (severity P1/P2) | "Query Datadog for logs/traces on affected service, identify root cause, implement fix or escalate to Jira" | 1/alert, 30 ACU, escalate if no fix in 15 min | PR (if fixable) or Jira issue (if complex) |
| Dependency Audit | Weekly schedule (Monday 6 AM) | "Check all dependencies for minor/patch updates, upgrade, run tests, document" | 100 ACU per session | PRs with version bumps |
| Framework Upgrade | Manual (parent session) | "Upgrade Spring Boot 2.7→3.x following playbook" | 10 concurrent children, 100 ACU/child | 80 PRs (one per service) |
