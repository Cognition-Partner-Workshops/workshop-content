# 2. SDLC Integration Design

*Reference: [`shared/general-themes/design-patterns-for-devin.md`](../../../shared/general-themes/design-patterns-for-devin.md)*

This section maps Devin's capabilities to each phase of a client's software development lifecycle. For each phase: the trigger that starts Devin, the pattern Devin follows, and where the human touchpoint occurs.

## 2.1 Planning Phase

**Trigger:** Engineer needs research, analysis, or design exploration before writing code.

**Devin Pattern:** Ask Devin (conversational research mode) — no session VM spun up, no PR created. Leverages DeepWiki's auto-generated architectural documentation for instant codebase context. Used for:
- Codebase exploration ("How does the authentication flow work in this service?")
- Technology evaluation ("Compare migration paths from Spring Boot 2.x to 3.x")
- Architecture analysis ("Map the dependencies between these 12 microservices")
- Feasibility assessment ("Can this COBOL program be converted to set-based SQL, or does it need procedural logic?")

**Human Touchpoint:** The engineer reviews Ask Devin's analysis and decides whether to proceed with a full session.

**Design implication:** Ask Devin reduces the planning overhead for complex tasks. Engineers get codebase understanding in minutes rather than hours of manual exploration.

## 2.2 Implementation Phase

**Trigger:** A task is defined (ticket assigned, requirement documented, manual prompt).

**Devin Pattern:** Cloud session for autonomous execution:
- Feature development — implement from requirements, write tests, open PR
- Bug fixes — reproduce, diagnose root cause, fix, verify with tests
- Refactoring — extract service, upgrade framework, modernize patterns
- Migration — translate code between languages/frameworks with parity verification

**Human Touchpoint:** PR review — the engineer inspects the diff, leaves comments, and Devin iterates until approved.

**Design implication:** Implementation sessions are the core unit of work. Each session produces a PR — the auditable artifact that flows through existing review processes.

## 2.3 Testing Phase

**Trigger:** Code merged or PR opened; coverage gaps identified; E2E suite needs expansion.

**Devin Pattern:** Test generation and execution:
- Unit test generation for uncovered code paths
- Integration test creation using existing test patterns
- E2E test authoring (Playwright, Cypress, Selenium) with browser interaction
- Performance test scaffolding with benchmark baselines
- Accessibility audit and remediation

**Human Touchpoint:** Review generated tests for correctness and relevance. Approve or request changes via PR comments.

**Design implication:** Testing is high-volume, pattern-repeatable work — ideal for parallelization with child sessions across many files or modules.

## 2.4 Deployment Phase

**Trigger:** Infrastructure change needed; IaC drift detected; new environment requested.

**Devin Pattern:** Infrastructure-as-Code execution:
- Terraform/CloudFormation authoring and `plan` verification
- Kubernetes manifest generation and `kubectl apply --dry-run` validation
- CI/CD pipeline creation (GitHub Actions, Azure DevOps, Jenkins)
- Asset Bundle deployment (Databricks, AWS CDK)
- Environment provisioning scripts with idempotency verification

**Human Touchpoint:** Review the `plan` output, approve the PR, and merge to trigger deployment through existing CD pipelines.

**Design implication:** Devin proposes infrastructure changes; existing CD pipelines execute them after human approval. No new deployment pathway is introduced.

## 2.5 Operations Phase

**Trigger:** Event-driven (CI failure, incident alert, schedule) — no human initiation required.

**Devin Pattern:** Automated operational responses:
- **Scheduled maintenance** — Weekly dependency bumps, license audits, dead code detection, documentation refresh
- **Event-driven incident response** — Alert fires → Devin investigates logs/traces → triages → implements fix or escalates
- **CI failure remediation** — Build breaks → Devin reads logs → diagnoses → pushes fix → monitors re-run
- **Compliance scanning** — Scheduled SAST/SCA runs with automated remediation of findings below threshold

**Human Touchpoint:** Review the PR (for fixes) or the escalation issue (for complex problems requiring human judgment).

**Design implication:** Operations is where automation topology matters most. The combination of scheduled sessions and event-driven triggers creates an "always-on" maintenance layer that responds without human initiation.

## 2.6 SDLC Integration Summary

```
┌─────────────────────────────────────────────────────────────────┐
│                        CLIENT SDLC                               │
├──────────┬──────────────┬──────────┬────────────┬───────────────┤
│ Planning │Implementation│ Testing  │ Deployment │  Operations   │
├──────────┼──────────────┼──────────┼────────────┼───────────────┤
│ Ask Devin│ Cloud Session│ Cloud    │ Cloud      │ Scheduled +   │
│ (research│ (autonomous  │ Session  │ Session    │ Event-Driven  │
│  mode)   │  execution)  │ (gen +   │ (IaC +    │ Sessions      │
│          │              │  execute)│  validate) │ (automated)   │
├──────────┼──────────────┼──────────┼────────────┼───────────────┤
│ Engineer │   PR Review  │PR Review │ Plan Review│ PR / Escalate │
│ Decision │              │          │            │   Issue       │
└──────────┴──────────────┴──────────┴────────────┴───────────────┘
         Trigger ──→ Devin Pattern ──→ Human Touchpoint
```

---

## Knowledge Checks

**Knowledge Check 2.1**
Q: A client asks: "Where does Devin fit in our deployment process? We use Terraform with a plan-apply workflow and require manual approval before `apply`." How do you respond?
A) "Devin replaces your Terraform workflow with its own deployment mechanism"
B) "Devin writes and validates Terraform code, opens a PR with the `plan` output, and your existing approval/apply workflow triggers on merge — no new deployment pathway"
C) "Devin runs `terraform apply` directly in its session"
D) "Devin does not support infrastructure work"
*Answer: B — Devin proposes infrastructure changes via PR. The existing CD pipeline (plan on PR, apply on merge after approval) is unchanged. Devin adds capacity to write and validate IaC, not a new deployment path.*

**Knowledge Check 2.2**
Q: Which SDLC phase benefits most from event-driven automation (no human initiation)?
A) "Planning — research should be automated"
B) "Implementation — feature development should be fully automated"
C) "Operations — scheduled maintenance, CI failure remediation, and incident response are high-volume, reactive tasks where event-driven triggers eliminate human paging"
D) "Deployment — all deployments should be automated end to end"
*Answer: C — Operations is where automation topology creates the most value. Maintenance tasks and incident response are reactive, high-volume, and follow repeatable patterns — ideal for event-driven Devin sessions.*

**Knowledge Check 2.3**
Q: A client's team uses Ask Devin to research a migration path, then kicks off a Cloud session to execute it. What is the human touchpoint between these two phases?
A) "There is no human touchpoint — it flows automatically"
B) "The engineer reviews Ask Devin's analysis and decides whether to proceed with a full session"
C) "A manager must approve the session"
D) "The PR reviewer decides whether to start the session"
*Answer: B — Ask Devin is a research tool (no VM, no PR). The engineer reviews the analysis and makes the decision to start a Cloud session for execution. This keeps humans in the decision loop for high-impact work.*

## Key Takeaways

- **Each SDLC phase has a natural Devin pattern:** research (Ask Devin), execution (Cloud session), testing (parallel sessions), deployment (IaC + PR), operations (event-driven/scheduled)
- **The human touchpoint is always defined:** PR review for implementation, plan review for deployment, escalation issue for operations — no phase runs without a human gate
- **Operations is the automation frontier:** event-driven triggers and scheduled sessions create an always-on maintenance layer that responds without human initiation
- **Devin does not replace existing processes — it plugs into them:** PRs flow through existing review workflows, Terraform plans trigger existing CD pipelines, and existing CI pipelines verify Devin's output
