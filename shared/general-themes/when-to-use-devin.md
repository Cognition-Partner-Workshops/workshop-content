# When to Use Devin

## Overview

Devin is an autonomous software engineering agent. It excels at well-defined engineering tasks that would otherwise not get done — because of constrained capacity, prohibitive volume, insufficient priority, or sheer difficulty. The key insight is not "replace engineers" but "unlock work that is currently blocked or deferred."

## Trigger Categories

### Event-Driven (Reactive)

Devin responds to signals from your existing toolchain. A webhook, alert, or tag transition fires and Devin begins working immediately — no human has to context-switch.

| Trigger | Example | Why Devin? |
|---------|---------|-----------|
| **Incident alert** | PagerDuty/OpsGenie/Azure Monitor fires on 5xx spike | Devin queries logs via MCP, identifies root cause, opens a fix PR — all before the on-call engineer finishes reading the alert |
| **Security finding** | SAST scan (SonarQube, Snyk, Checkmarx) detects HIGH/CRITICAL CVEs | Devin reads the scan report, remediates findings, pushes a fix, and the re-scan verifies the fix — closed-loop |
| **Ticket assignment** | Jira/Linear/ADO work item tagged `Devin:Implementation` | Devin reads the acceptance criteria, implements, opens a PR, and links it back to the ticket |
| **PR event** | PR opened by a developer triggers Devin Review | Devin reviews the diff for bugs, style issues, and test gaps — proactively opening remediation PRs for discovered issues |
| **CI failure** | Build or test fails on a branch | Devin reads the failure logs, diagnoses the issue, and pushes a fix commit to the same branch |

### Large-Scale (Proactive Campaigns)

Devin divides and conquers work that would take a team of engineers weeks. A parent agent plans the campaign, then spawns child agents — each handling one unit of work (one repo, one module, one migration target).

| Campaign | Example | Why Devin? |
|----------|---------|-----------|
| **Codebase modernization** | Translate COBOL → Java across 200 copybooks | Each child agent handles one copybook. Humans review the PRs. Volume that would take months becomes weeks |
| **Framework migration** | Upgrade Spring Boot 2.x → 3.x across 50 microservices | Each service gets its own agent. Jakarta namespace migration, dependency bumps, and test verification — all parallelized |
| **Language translation** | Migrate SAS → Python/Snowflake for 300 ETL jobs | Devin translates each job, generates equivalence tests, and validates output parity |
| **Dependency hygiene** | Bump all transitive dependencies across an org's repos | Scheduled weekly. Devin opens PRs, CI validates, humans merge |
| **Security backlog** | Remediate 500 open Snyk findings across 80 repos | Each finding gets its own agent session. Devin reads the advisory, applies the fix, runs tests |
| **Test coverage** | Generate unit tests for 200 uncovered modules | Devin analyzes each module, writes tests following existing conventions, achieves target coverage |

### Capacity-Constrained (Ongoing O&M)

Work that engineers know should be done but deprioritize because higher-value tasks demand their attention. Devin runs on a schedule or is triggered by routine events, handling the hygiene so engineers can focus on architecture, product features, and complex debugging.

| Task | Frequency | Why Devin? |
|------|-----------|-----------|
| **Dependency updates** | Weekly | Devin bumps versions, runs tests, opens PRs. Engineers merge if green |
| **Dead code cleanup** | Monthly | Devin identifies unused imports, unreachable code, and deprecated APIs. Opens cleanup PRs |
| **Documentation refresh** | On code change | Devin updates inline docs, READMEs, and API docs when code changes land |
| **License compliance** | Quarterly | Devin audits all dependencies for license compatibility, flags violations |
| **Accessibility audits** | Sprint boundary | Devin runs axe/Lighthouse, remediates findings, opens PRs |

## The Sweet Spot

Give Devin work that is:

1. **Well-defined** — clear acceptance criteria, verifiable outcomes (tests pass, scan is clean, PR is merged)
2. **Repetitive at scale** — the same pattern applied to many targets (repos, modules, findings)
3. **Lower priority but valuable** — work humans would do if they had unlimited time
4. **Automatable end-to-end** — Devin can fetch context, implement, test, and submit for review without manual intervention
5. **Safe to iterate** — Devin works on branches, never pushes to main, and humans always approve the merge

## What Devin Needs to Succeed

- **Indexed codebases** — Devin retrieves context from code repositories it has access to. Connect your repos via Git integrations
- **Programmatic access to remote resources** — MCP servers, API keys, and service account credentials let Devin interact with your toolchain (Jira, Datadog, Azure DevOps, databases, etc.)
- **Locally buildable and testable code** — Devin runs builds and tests on its own VM. If your code requires a locally reproducible build, Devin can verify its own work
- **Clear prompts** — The more specific the instructions, the better the outcome. Include repo names, file paths, acceptance criteria, and examples when possible
