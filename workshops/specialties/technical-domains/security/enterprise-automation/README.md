# Workshop: Enterprise Security Automation

## Overview

| | |
|---|---|
| **Focus** | Event-driven security automation, agent orchestration, and long-term reasoning for tech debt remediation |
| **Duration** | ~4 hours (3 labs × 90 min + breaks) |
| **Audience** | Engineering leadership, AppSec/DevSecOps, platform engineering, enterprise architecture |
| **Key Modules** | [Event-Driven SAST Remediation](../../../../../../../modules/security/event-driven-sast-remediation.md), [Mass Security Backlog Remediation](../../../../../../../modules/security/mass-security-backlog-remediation.md) |

## Abstract

> This track demonstrates three progressively advanced enterprise patterns for using Devin at organizational scale:
>
> **Lab 1 — Event-Driven SAST Remediation:** Build a CI pipeline where SAST tools automatically scan PRs from human developers and route findings to Devin for autonomous remediation. Devin fixes the security issues, pushes a commit, and CI re-scans to verify. Zero human intervention.
>
> **Lab 2 — Mass Security Backlog Remediation:** Coordinate a multi-repo security remediation using agent orchestration. A parent Devin session reads a consolidated SAST report covering 2 repositories, triages findings, and launches parallel child sessions — one per repo — to remediate simultaneously.
>
> **Lab 3 — One-Shot Tech Debt Remediation:** Craft a single, meticulously structured prompt that achieves 80-90% completion of a major framework upgrade in one session. The prompt demands provable deliverables (build logs, before/after metrics) and requires Devin to honestly document testing gaps it cannot verify.
>
> **Who should attend:** Engineering leaders evaluating Devin for enterprise adoption, AppSec teams with large vulnerability backlogs, platform engineers building developer tooling, and architects designing AI-augmented SDLC pipelines.

## Getting the Most from This Workshop

> **Devin works asynchronously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

A few tips to maximize your hands-on time:

- **Start sessions early, review later.** Kick off the session first, then use the wait time for Ask Devin research or reading DeepWiki — Devin keeps working in the background.
- **Use Ask Devin to refine requirements.** The better-defined a task is, the better Devin's output. Ask Devin helps you think through the problem before Devin executes.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that compounds over time.
- **Leave PR comments to steer Devin.** After Devin opens a PR, you can leave comments and Devin will wake up and address them — this is the core feedback loop.
- **Try parallel sessions.** Running multiple sessions simultaneously mirrors real enterprise usage and demonstrates team-based operation at scale.

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

- **Module:** [Event-Driven SAST Remediation](../../../../../../../modules/security/event-driven-sast-remediation.md)
- **Repositories:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) and [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- **Objective:** Build a GitHub Actions workflow where SAST tools scan PRs from non-Devin authors and automatically trigger a Devin session to remediate findings

#### What to Try

1. **Show the existing pattern:** Walk through `timesheet-app`'s `sonar-devin-fix.yml` workflow to show how Devin is already integrated into CI for auto-remediation
2. **Build the pipeline:** Have Devin create a new `sast-auto-remediate.yml` workflow on `uc-cve-remediation-regulatory-compliance` that scans PRs and calls the Devin API when findings exceed a severity threshold
3. **Trigger it live:** Open a PR as a human user, watch the SAST scan run, see Devin automatically start remediating, and observe the re-scan passing

#### Key Takeaways

- **"Devin as a background agent"** — Devin isn't a tool someone opens; it's an agent that responds to events
- **"Closed-loop verification"** — the same CI that found the problem verifies the fix
- **"Author filtering"** — preventing infinite loops where Devin triggers itself
- **"Escalation policy"** — what happens when Devin can't fix something? Route to a human.

#### Target Outcomes

- Working `sast-auto-remediate.yml` workflow with Devin API integration
- Live walkthrough of the scan → fix → re-scan loop
- `ARCHITECTURE.md` documenting the event-driven flow
- PR with the workflow and documentation

---

### Lab 2 — Mass Security Backlog Remediation with Agent Orchestration (90 min)

- **Module:** [Mass Security Backlog Remediation](../../../../../../../modules/security/mass-security-backlog-remediation.md)
- **Repositories:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) and [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- **Objective:** Demonstrate enterprise-scale remediation: one parent Devin session triages a consolidated SAST report and launches parallel child sessions to remediate 2 repos simultaneously

#### What to Try

1. **Generate the backlog:** Run SAST scans on both repos to produce a consolidated findings report (or use the output from Lab 1)
2. **Parent session — triage:** Give Devin the consolidated report and have it create a `SECURITY_BACKLOG.md` with all findings organized by severity and repo
3. **Launch child sessions:** Start 2 parallel Devin sessions — one for each repo — with scoped remediation instructions
4. **Show parallel execution:** Both sessions running simultaneously, each working on its assigned repo
5. **Consolidate results:** After both sessions complete, review the PRs and the consolidated `REMEDIATION_SUMMARY.md`

#### Key Takeaways

- **"Agent orchestration"** — a parent agent that plans and delegates to child agents
- **"Scoped context"** — each child session gets only the information it needs
- **"Parallel execution"** — 2 repos remediated simultaneously, not sequentially
- **"Enterprise scale"** — this pattern scales to 10, 50, 100 repos with the Devin API
- **"Audit trail"** — the consolidated report provides evidence for compliance teams

#### Target Outcomes

- Consolidated `SECURITY_BACKLOG.md` covering both repos
- 2 parallel PRs (one per repo) with security fixes and per-repo `REMEDIATION_REPORT.md`
- Evidence of parallel execution (overlapping session timestamps)
- Consolidated `REMEDIATION_SUMMARY.md` from the parent session

---

### Lab 3 — One-Shot Tech Debt Remediation via Long-Term Reasoning (75 min)

- **Module:** [One-Shot Tech Debt Remediation](../../../../../../../modules/migration-modernization/one-shot-tech-debt-remediation.md)
- **Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction) or [uc-cve-remediation-regulatory-compliance](https://github.com/Cognition-Partner-Workshops/uc-cve-remediation-regulatory-compliance)
- **Objective:** Demonstrate that a single, well-engineered prompt can achieve 80-90% completion of a major tech debt remediation — with provable results and honest acknowledgement of testing gaps

#### What to Try

1. **The prompt IS the engineering:** Spend 10 minutes showing participants the structured prompt with its 5 elements (scope, execution order, proof requirements, testing gap acknowledgement, exit criteria)
2. **Submit and observe:** Submit the prompt to Devin and watch the long-running session execute step by step
3. **While Devin works:** Use AskDevin and DeepWiki to explore the codebase and predict what Devin will encounter
4. **Review the deliverables:** When the PR opens, review `MIGRATION_PROOF.md` (before/after metrics) and `TESTING_GAPS.md` (what Devin couldn't verify)
5. **Compare across participants:** If multiple participants are running the same prompt, compare completion rates

#### Key Takeaways

- **"Prompt engineering for complex tasks"** — the quality of the output is proportional to the quality of the input
- **"Provable deliverables"** — demanding proof in the prompt produces better results than reviewing after the fact
- **"The 80/90 principle"** — 80-90% autonomous completion with honest gap reporting is more valuable than claimed 100% with hidden failures
- **"Testing gap awareness"** — `TESTING_GAPS.md` is the most important artifact; it shows engineering maturity
- **"Long-term reasoning"** — Devin maintains context and executes a multi-step plan over 60+ minutes

#### Target Outcomes

- Single PR completing 80-90% of the framework upgrade
- `MIGRATION_PROOF.md` with before/after metrics (Java version, Spring Boot version, CVE count, test results)
- `TESTING_GAPS.md` documenting what Devin could not verify
- Clean build with all unit tests passing
- PR description with structured summary of all changes

---

## Additional Challenges

Participants who finish early may attempt any challenge from the full [module catalog](../../../../../../../modules/). Recommended extensions:

| Challenge | Module | Repo | Difficulty | Time |
|-----------|--------|------|-----------|------|
| Shift Left Security (build on Lab 1) | [Shift Left Security](../../../../../../../modules/security/shift-left-security.md) | uc-cve-remediation-regulatory-compliance | Intermediate | 60 min |
| Repetitive Framework Upgrades (scale Lab 3) | [Repetitive Framework Upgrades](../../../../../../../modules/migration-modernization/repetitive-framework-upgrades.md) | Multiple repos | Intermediate | 60 min |
| CI/CD Pipeline | [CI/CD Pipeline](../../../../../../../modules/devops-cicd/cicd-pipeline.md) | timesheet-app | Intermediate | 45 min |

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
- **Narrative progression:** Reactive agent (Lab 1) → Coordinated agents (Lab 2) → Autonomous one-shot execution (Lab 3)
- **Enterprise value props demonstrated:**
  - Lab 1: Devin reduces MTTR for security findings to near-zero
  - Lab 2: Devin scales security remediation across the entire portfolio, not one repo at a time
  - Lab 3: Devin handles complex tech debt with minimal human oversight and honest self-assessment

## Devin Features Checklist

Encourage participants to track their progress on the [Devin Features Appendix](../../../../../../../modules/devin-features/README.md) throughout the session. Key activities for this track:

- [ ] Use the Devin API to trigger a session programmatically (Lab 1)
- [ ] Run parallel Devin sessions (Lab 2)
- [ ] Review Devin's consolidated reporting across repos (Lab 2)
- [ ] Craft an advanced prompt with proof requirements (Lab 3)
- [ ] Review `TESTING_GAPS.md` — evaluate Devin's self-awareness (Lab 3)
- [ ] Leave PR comments and watch Devin iterate (all labs)
- [ ] Compare results across participants (Lab 3)

## Post-Event

- [ ] Collect participant feedback — especially: which lab was most compelling for enterprise adoption?
- [ ] Archive any event-specific branches
- [ ] Update challenge modules if issues were discovered
- [ ] Share session recordings/artifacts with participants
