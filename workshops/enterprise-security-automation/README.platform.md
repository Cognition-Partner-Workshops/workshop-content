# Workshop: Enterprise Security Automation (Desktop + Cloud)

**Other variants:** [Cloud only](README.md)

## Overview

| | |
|---|---|
| **Focus** | Event-driven security automation, agent orchestration, and long-term reasoning for tech debt remediation |
| **Duration** | ~4 hours (3 labs × 90 min + breaks) |
| **Audience** | Engineering leadership, AppSec/DevSecOps, platform engineering, enterprise architecture |
| **Delivery** | Devin Desktop + Devin Cloud |
| **Key Modules** | [Event-Driven SAST Remediation](../../modules/security/event-driven-sast-remediation.md), [Mass Security Backlog Remediation](../../modules/security/mass-security-backlog-remediation.md) |

## Abstract

> This track demonstrates three progressively advanced enterprise patterns for using Devin at organizational scale — delivered through **Devin Desktop** as your primary interface with **Devin Cloud** handling autonomous execution:
>
> **Lab 1 — Event-Driven SAST Remediation:** Build a CI pipeline where SAST tools automatically scan PRs from human developers and route findings to Devin for autonomous remediation. Devin fixes the security issues, pushes a commit, and CI re-scans to verify. Zero human intervention — monitored from Desktop's Agent Command Center.
>
> **Lab 2 — Mass Security Backlog Remediation:** Coordinate a multi-repo security remediation using agent orchestration. A parent Devin session reads a consolidated SAST report covering 2 repositories, triages findings, and launches parallel child sessions — one per repo — to remediate simultaneously. Track both child sessions on the Agent Command Center Kanban board.
>
> **Lab 3 — One-Shot Tech Debt Remediation:** Craft a single, meticulously structured prompt that achieves 80-90% completion of a major framework upgrade in one session. Use Cascade locally for codebase exploration, then delegate the full task to Devin Cloud. Review the PR with one-click checkout in Desktop.
>
> **Who should attend:** Engineering leaders evaluating Devin for enterprise adoption, AppSec teams with large vulnerability backlogs, platform engineers building developer tooling, and architects designing AI-augmented SDLC pipelines.

## Platform Context

This workshop uses **Devin Desktop + Devin Cloud**. You will use Desktop as your primary interface — exploring code locally, delegating tasks to Cloud, and reviewing results without leaving the editor. Cloud agents handle the autonomous execution while Desktop provides the command center for monitoring, reviewing, and iterating.

> **Tip:** If you prefer a browser-only workflow, see the [Cloud only variant](README.md) which uses the Devin web app ([app.devin.ai](https://app.devin.ai)) exclusively.

## Desktop Setup

Before starting the labs, set up your Desktop environment:

1. **Create a Space** for this workshop — name it "Enterprise Security Automation" or similar. This groups your sessions, PRs, and files in a single view
2. **Familiarize yourself with the Agent Command Center** — the Kanban board at the top of Desktop showing agents organized by status (Working, Waiting, Review, Done)
3. **Note the delegation flow** — throughout the labs you will use Cascade or Devin Local for exploration, then delegate implementation tasks to Devin Cloud

> All sessions you create within your Space automatically inherit the project context, so you do not need to re-specify repos or background information each time.

## Getting the Most from This Workshop

> **Devin Cloud works asynchronously on its own machine.** Once you delegate a task from Desktop, the cloud agent runs independently — you can continue local work, explore code with Cascade, or move to the next lab. The Agent Command Center shows real-time status, and you will get notified when a PR is ready for review.

A few tips to maximize your hands-on time:

- **Delegate early, review later.** Send the task to Cloud first, then use the wait time for local exploration with Cascade or Devin Local — Cloud keeps working in the background.
- **Use Cascade or Devin Local to refine requirements.** Explore code locally before delegating to Cloud. The better-defined a task is, the better the cloud agent's output.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that compounds over time. Knowledge, playbooks, and MCP integrations carry across local and cloud surfaces.
- **Review PRs with one-click checkout.** When a cloud agent opens a PR, review it directly in the Desktop editor — no manual `git fetch` or branch switching required.
- **Monitor parallel sessions on the Kanban board.** The Agent Command Center shows all your agents (local and cloud) by status. Especially useful during Labs 2 and 3 when running multiple sessions.
- **Mix agents for different tasks.** Desktop supports multiple agents via ACP (Agent Client Protocol) — use Devin Local for quick tasks and Devin Cloud for long-running autonomous work.

## Table of Contents

- [Structure](#structure)
- [Featured Labs](#featured-labs)
  - [Lab 1 — Event-Driven SAST Remediation](#lab-1--event-driven-sast-remediation-90-min)
  - [Lab 2 — Mass Security Backlog Remediation with Agent Orchestration](#lab-2--mass-security-backlog-remediation-with-agent-orchestration-90-min)
  - [Lab 3 — One-Shot Tech Debt Remediation](#lab-3--one-shot-tech-debt-remediation-via-long-term-reasoning-75-min)
- [Repos Required](#repos-required-on-devins-machine)

---

## Structure

Three labs that build on each other in a progressive sequence:

1. **Lab 1 — Event-Driven SAST Remediation (90 min):** Devin as a reactive agent triggered by CI events
2. **Break (15 min)**
3. **Lab 2 — Mass Backlog Remediation with Agent Orchestration (90 min):** Devin coordinating parallel work across repos
4. **Break (15 min)**
5. **Lab 3 — One-Shot Tech Debt via Long-Term Reasoning (75 min):** Devin executing a complex, multi-step task from a single prompt

**Progression:**
- Lab 1: Devin responds to *one finding on one repo* autonomously
- Lab 2: Devin coordinates *many findings across multiple repos* in parallel
- Lab 3: Devin executes *a complete upgrade in one shot* with proof of completion

---

## Featured Labs

### Lab 1 — Event-Driven SAST Remediation (90 min)

- **Module:** [Event-Driven SAST Remediation](../../modules/security/event-driven-sast-remediation.md)
- **Repositories:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) and [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- **Objective:** Build a GitHub Actions workflow where SAST tools scan PRs from non-Devin authors and automatically trigger a Devin session to remediate findings

#### Desktop Workflow

1. **Explore the existing pattern with Cascade:** Use Cascade or Devin Local in Desktop to explore `timesheet-app`'s `sonar-devin-fix.yml` workflow. Ask it to explain how the event-driven SAST remediation loop works — Cascade can read the repo locally and walk through the flow
2. **Delegate pipeline creation to Cloud:** From Desktop, delegate a task to Devin Cloud to create a new `sast-auto-remediate.yml` workflow on `uc-cve-remediation-regulatory-compliance` that scans PRs and calls the Devin API when findings exceed a severity threshold
3. **Monitor in Agent Command Center:** Watch the cloud session's progress on the Kanban board. When Devin opens a PR, use one-click checkout to review the workflow file directly in the editor
4. **Trigger it live:** Open a PR as a human user, watch the SAST scan run, see Devin automatically start remediating, and observe the re-scan passing. Track the new auto-triggered session in the Agent Command Center

#### Key Takeaways

- **"Devin as a background agent"** — Devin isn't a tool someone opens; it's an agent that responds to events
- **"Closed-loop verification"** — the same CI that found the problem verifies the fix
- **"Author filtering"** — preventing infinite loops where Devin triggers itself
- **"Escalation policy"** — what happens when Devin can't fix something? Route to a human.
- **"Local exploration, cloud execution"** — use Cascade to understand the pattern, then delegate the implementation to Cloud

#### Target Outcomes

- Working `sast-auto-remediate.yml` workflow with Devin API integration
- Live walkthrough of the scan → fix → re-scan loop
- `ARCHITECTURE.md` documenting the event-driven flow
- PR reviewed with one-click checkout in Desktop

---

### Lab 2 — Mass Security Backlog Remediation with Agent Orchestration (90 min)

- **Module:** [Mass Security Backlog Remediation](../../modules/security/mass-security-backlog-remediation.md)
- **Repositories:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) and [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- **Objective:** Demonstrate enterprise-scale remediation: one parent Devin session triages a consolidated SAST report and launches parallel child sessions to remediate 2 repos simultaneously

#### Desktop Workflow

1. **Generate the backlog:** Run SAST scans on both repos to produce a consolidated findings report (or use the output from Lab 1)
2. **Parent session — triage:** Delegate to Devin Cloud from Desktop — give it the consolidated report and have it create a `SECURITY_BACKLOG.md` with findings organized by severity and repo
3. **Watch child sessions spawn:** As the parent session launches child sessions, they appear on your Agent Command Center Kanban board automatically. Watch both child sessions progress side by side
4. **Monitor parallel execution:** Both child agents run simultaneously, each on its assigned repo. The Kanban board shows their status in real time — no browser tab switching needed
5. **Review results with one-click checkout:** As each child session opens a PR, use one-click checkout to review the fixes directly in the editor. Compare the per-repo `REMEDIATION_REPORT.md` files side by side

#### Key Takeaways

- **"Agent orchestration"** — a parent agent that plans and delegates to child agents
- **"Scoped context"** — each child session gets only the information it needs
- **"Parallel execution"** — 2 repos remediated simultaneously, not sequentially
- **"Enterprise scale"** — this pattern scales to 10, 50, 100 repos with the Devin API
- **"Audit trail"** — the consolidated report provides evidence for compliance teams
- **"Single pane of glass"** — the Agent Command Center provides visibility across all running agents without switching between browser tabs

#### Target Outcomes

- Consolidated `SECURITY_BACKLOG.md` covering both repos
- 2 parallel PRs (one per repo) with security fixes and per-repo `REMEDIATION_REPORT.md`
- Evidence of parallel execution (overlapping session timestamps visible on the Kanban board)
- Consolidated `REMEDIATION_SUMMARY.md` from the parent session

---

### Lab 3 — One-Shot Tech Debt Remediation via Long-Term Reasoning (75 min)

- **Module:** [One-Shot Tech Debt Remediation](../../modules/migration-modernization/one-shot-tech-debt-remediation.md)
- **Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) or [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- **Objective:** Demonstrate that a single, well-engineered prompt can achieve 80-90% completion of a major tech debt remediation — with provable results and honest acknowledgement of testing gaps

#### Desktop Workflow

1. **The prompt IS the engineering:** Review the structured prompt with its 5 elements (scope, execution order, proof requirements, testing gap acknowledgement, exit criteria)
2. **Explore locally first:** Use Cascade or Devin Local to explore the codebase before submitting the prompt. Understand the current state — framework versions, dependency graph, test coverage — so you can predict what the cloud agent will encounter
3. **Delegate to Cloud:** Send the full structured prompt to Devin Cloud from Desktop. The cloud agent will work autonomously for 60+ minutes on its own VM
4. **Monitor progress:** Track the long-running session on the Agent Command Center. Continue other work locally while Cloud executes — this is the platform continuum in action
5. **Review with one-click checkout:** When the PR opens, review `MIGRATION_PROOF.md` (before/after metrics) and `TESTING_GAPS.md` (what Devin couldn't verify) directly in Desktop's editor
6. **Compare across participants:** If multiple participants are running the same prompt, compare completion rates by reviewing each other's PRs

#### Key Takeaways

- **"Prompt engineering for complex tasks"** — the quality of the output is proportional to the quality of the input
- **"Provable deliverables"** — demanding proof in the prompt produces better results than reviewing after the fact
- **"The 80/90 principle"** — 80-90% autonomous completion with honest gap reporting is more valuable than claimed 100% with hidden failures
- **"Testing gap awareness"** — `TESTING_GAPS.md` is the most important artifact; it shows engineering maturity
- **"Long-term reasoning"** — Devin maintains context and executes a multi-step plan over 60+ minutes
- **"Local → Cloud handoff"** — explore locally to build understanding, then delegate to Cloud for autonomous execution

#### Target Outcomes

- Single PR completing 80-90% of the framework upgrade
- `MIGRATION_PROOF.md` with before/after metrics (Java version, Spring Boot version, CVE count, test results)
- `TESTING_GAPS.md` documenting what Devin could not verify
- Clean build with all unit tests passing
- PR reviewed with one-click checkout in Desktop

---

## Additional Challenges

Participants who finish early may attempt any challenge from the full [module catalog](../../modules/). Recommended extensions:

| Challenge | Module | Repo | Difficulty | Time |
|-----------|--------|------|-----------|------|
| Shift Left Security (build on Lab 1) | [Shift Left Security](../../modules/security/shift-left-security.md) | uc-cve-remediation-regulatory-compliance | Intermediate | 60 min |
| Repetitive Framework Upgrades (scale Lab 3) | [Repetitive Framework Upgrades](../../modules/migration-modernization/repetitive-framework-upgrades.md) | Multiple repos | Intermediate | 60 min |
| CI/CD Pipeline | [CI/CD Pipeline](../../modules/devops-cicd/cicd-pipeline.md) | timesheet-app | Intermediate | 45 min |

---

## Repos Required on Devin's Machine

- [ ] timesheet-app
- [ ] uc-cve-remediation-regulatory-compliance
- [ ] uc-spring-boot-upgrade-microservice-extraction

## Runtime Resources

- **Devin API access:** Required for Lab 1 (programmatic session triggering) and Lab 2 (child session launching)
- **SonarQube (optional):** `docker compose -f docker-compose.sonarqube.yml up -d` on port 9000 for enhanced SAST scanning in Labs 1 and 2

## Context

- **Audience:** Enterprise engineering leadership evaluating Devin for organizational adoption
- **Delivery surface:** Devin Desktop (primary) + Devin Cloud (autonomous execution)
- **Narrative progression:** Reactive agent (Lab 1) → Coordinated agents (Lab 2) → Autonomous one-shot execution (Lab 3)
- **Platform story:** Each lab reinforces the local→cloud continuum — explore with Cascade, delegate to Cloud, monitor on the Kanban board, review with one-click checkout
- **Enterprise value props demonstrated:**
  - Lab 1: Devin reduces MTTR for security findings to near-zero
  - Lab 2: Devin scales security remediation across the entire portfolio, not one repo at a time
  - Lab 3: Devin handles complex tech debt with minimal human oversight and honest self-assessment

## Devin Features Checklist

Encourage participants to track their progress on the [Devin Features Appendix](../../modules/devin-features/README.md) throughout the session. Key activities for this track:

- [ ] Create a Space in Desktop to organize workshop sessions (setup)
- [ ] Use Cascade or Devin Local for code exploration (Labs 1, 3)
- [ ] Delegate tasks to Devin Cloud from Desktop (all labs)
- [ ] Monitor sessions in the Agent Command Center Kanban board (all labs)
- [ ] Use the Devin API to trigger a session programmatically (Lab 1)
- [ ] Run parallel Devin sessions and watch them on the Kanban board (Lab 2)
- [ ] Review Devin's consolidated reporting across repos (Lab 2)
- [ ] Review PRs with one-click checkout in Desktop (all labs)
- [ ] Craft an advanced prompt with proof requirements (Lab 3)
- [ ] Review `TESTING_GAPS.md` — evaluate Devin's self-awareness (Lab 3)
- [ ] Leave PR comments and watch Devin iterate (all labs)
- [ ] Compare results across participants (Lab 3)

## Post-Event

- [ ] Collect participant feedback — especially: which lab was most compelling for enterprise adoption?
- [ ] Archive any event-specific branches
- [ ] Update challenge modules if issues were discovered
- [ ] Share session recordings/artifacts with participants
