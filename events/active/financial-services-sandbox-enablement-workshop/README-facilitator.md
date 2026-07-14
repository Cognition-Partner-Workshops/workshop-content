# Financial Services Sandbox Enablement — Facilitator Companion

Companion to [README.md](README.md). This file is for the **host running the session** — a Cognition solutions engineer or a consulting-partner enablement lead. It is not attendee-facing.

The audience is a **consulting partner's practitioners** preparing to position Devin with a banking client and run a customer pilot. Everything here is aimed at leaving them able to run their own client sessions afterward, with a sandbox they keep using internally.

## Table of Contents

- [Session Goals](#session-goals)
- [Pre-Session Setup Checklist](#pre-session-setup-checklist)
- [Presales & Delivery Positioning](#presales--delivery-positioning)
- [MCP Integration Opportunities](#mcp-integration-opportunities)
- [Timing Guide & Adaptation](#timing-guide--adaptation)
- [Common Questions & Answers](#common-questions--answers)
- [Next Steps Toward the Pilot](#next-steps-toward-the-pilot)

---

## Session Goals

By the end of the hour, the partner's practitioners should:

1. Understand Devin's differentiator — it orchestrates the entire SDLC, not just code completion.
2. Be comfortable running client sessions that map to the banking client's top use cases (end-to-end brownfield delivery; security & defect resolution).
3. Know which workflows to emphasize for a banking buyer and how to frame them per persona.
4. Have a working sandbox they can keep using internally.
5. Align on concrete next steps toward the joint customer workshop and pilot.

Keep pulling the conversation back to the headline message: **faster delivery, not just faster coding.** The bottleneck in banking delivery is requirements → design → tested, compliant implementation — not typing speed.

---

## Pre-Session Setup Checklist

Do this a day ahead — DeepWiki indexing and repo connection are not instant.

- [ ] **Repo connections** — Confirm both lab repos are connected to the workshop org in Devin settings:
  - `ts-java-spring-boot-internet-banking` (Lab 1)
  - `uc-cve-remediation-regulatory-compliance` (Lab 2)
  - Optional Going Further repos: `uc-legacy-modernization-cobol-to-java`, `uc-db-migration-sybase-to-sqlserver`, `fineract`
- [ ] **DeepWiki indexing** — Trigger indexing on `ts-java-spring-boot-internet-banking` so Lab 1's DeepWiki step works during the session.
- [ ] **Knowledge notes** — Seed one or two org Knowledge notes so participants see the shared context layer in action (e.g., a note on the `com.javatodev.finance` package conventions, or the bank's naming/testing standards). Encourage accepting Devin's suggested Knowledge during the labs.
- [ ] **Environment blueprint** — Both Java repos build with Gradle; confirm the sandbox environment can run `./gradlew test` (JDK 21 for internet-banking, JDK 11 for the CVE repo). A blueprint that pre-installs the JDKs makes verification fast.
- [ ] **Secrets / service accounts** — Neither lab requires customer credentials. If you plan to show an MCP integration (see below), provision the scoped, read-only service-account token ahead of time — never ask participants to bring production credentials.
- [ ] **Accounts & access** — Confirm every participant has a Devin login and can create sessions in the workshop org.
- [ ] **Dry run** — Run both prompts yourself end-to-end the day before. Confirm PRs open and `./gradlew test` is green. Note the actual wall-clock time so your agenda is realistic.

---

## Presales & Delivery Positioning

This client's stated goals: **increase throughput 30–40%+, reduce cycle time, standardize the SDLC, keep headcount flat while adding capacity, and maintain strict security/compliance/quality.** Success is measured by faster *delivery*, not faster *coding*. Frame every lab against that.

### Persona framing (banking buyers)

Match the narrative to who is in the room. Full matrix in [Value Narratives](../../../reference/general-themes/value-narratives.md).

| Persona | Lead with | What they need to hear |
|---------|-----------|------------------------|
| CTO / VP Eng | Capacity unlocking + focus elevation | "Engineers move from 60% implementation to design + review; the deferred backlog actually gets done — headcount flat." |
| CISO / Security | Risk reduction | "Exposure window shrinks from next sprint to next review; findings remediated as detected, with auditable evidence." |
| Delivery Director | Velocity multiplication | "Requirements-to-PR compresses; agents parallelize while engineers review — cycle time drops." |
| Practice Lead / P&L (the partner) | Operating-model efficiency | "Hybrid human + agent model — most competitive cost structure in RFPs; margin expands as playbooks mature." |

### Differentiation vs. other AI coding assistants

Keep it neutral — position Devin as covering more of the lifecycle, not as knocking a named competitor. Tools in the AI-assisted-development category (in-IDE completion assistants, PR-review bots, chat copilots) are complementary and often already in use. The distinction to draw:

- **Lifecycle coverage** — Lab 1 makes it visible: a spec and design document precede code, tests follow, and quality gates run before review. Completion-style assistants accelerate the typing step; Devin runs the arc around it.
- **Autonomous, cloud-based execution** — Devin works on its own VM, builds and tests its work, and opens a PR — it is not an in-editor suggestion engine.
- **Team-based context** — indexed codebases, Knowledge, and playbooks belong to the team and compound; this maps to how a bank standardizes an SDLC across many squads.
- **Governance fit** — clean-room start, scoped service-account access, and auditable artifacts align with banking security and compliance controls.

### Scoping inputs for the pilot

While running the labs, collect what you need to scope the engagement:
- Which brownfield services / repos are the best pilot candidates (right-sized, buildable, real backlog)?
- Which SDLC stages hurt most today (requirements, design, testing, remediation)?
- What toolchain would Devin integrate with (issue tracker, SAST, CI, artifact registry)?
- Rough volume for the ROI story — deferred backlog size, dependency-debt count, migration surface — for a unit-of-work / ACU-based estimate (see [Value Narratives](../../../reference/general-themes/value-narratives.md)).

---

## MCP Integration Opportunities

MCP servers let Devin read from and act on the systems a bank already runs, turning the labs into their real workflow:

| System category | Adds to the story |
|-----------------|-------------------|
| Issue tracker (Jira, Azure DevOps) | Ticket-driven delivery — a tagged epic triggers a Lab 1-style feature session; requirements posted back to the ticket |
| SAST / dependency scanner (SonarQube, Snyk, and similar) | Lab 2 reads findings and quality-gate status directly instead of parsing manifests |
| CI/CD (GitHub Actions, Azure Pipelines, Jenkins) | Event-driven remediation — a failing check triggers a diagnose-and-fix session |
| Observability / incident (Datadog, ServiceNow, PagerDuty) | Incident → triage → fix PR, closing the loop on defect resolution |

> **Customers usually will NOT bring their own MCP servers to a workshop.** Plan for this. Options for the facilitator:
>
> 1. **Pre-configure one sample integration** on the workshop org ahead of time (a read-only SAST MCP is the highest-impact for Lab 2). Provision a scoped, read-only service-account token — never production write credentials.
> 2. **Walk it through verbally** — run the labs without MCP (both work standalone) and narrate where each integration would slot in for the client's real toolchain.
> 3. **Whiteboard the target state** — sketch the client's toolchain and mark each MCP connection point as a pilot deliverable.

Both labs are designed to work with **no** MCP connected, so a missing integration never blocks the session.

---

## Timing Guide & Adaptation

Per-segment budget for the 1-hour hybrid format:

| Segment | Target | Notes |
|---------|--------|-------|
| Positioning intro | 7 min | Full-SDLC message; keep it tight |
| Kick off both labs | 5 min | Get every participant's sessions running early |
| Ask Devin + DeepWiki | 13 min | Productive wait time; circulate and answer questions |
| Review Lab 1 PR | 15 min | The centerpiece — walk the spec → design → code → tests artifacts |
| Review Lab 2 PR | 12 min | Triage → RCA → fix → verify; highlight the audit artifacts |
| Wrap-up & next steps | 8 min | Differentiation recap, Going Further, pilot alignment |

Adaptation notes:

- **Running short on time?** Drop Lab 2 to a live walkthrough on your own pre-run PR and spend the reclaimed time on Lab 1 — the end-to-end SDLC is the highest-priority use case for this client.
- **Room is deeply technical?** Have them add the Lab 1 follow-up (monthly summary endpoint) via PR comment to show the feedback loop, and open the Lab 1 stretch (schema + two endpoints).
- **Room is more leadership than hands-on?** Run both labs yourself and spend the time on positioning, persona framing, and the pilot conversation.
- **Have 90 minutes?** Add one Going Further item live — an event-driven automation sketch or a modernization starter on `uc-legacy-modernization-cobol-to-java`.

PRs typically begin arriving 10–15 minutes after kickoff; adjust review windows to when they actually land.

---

## Common Questions & Answers

- **"How is this different from the AI coding assistant we already have?"** Those accelerate the typing step in the editor. Devin runs the lifecycle around it — requirements, design, tests, quality gates, and a review-ready PR from its own VM. Position them as complementary; Devin covers stages completion assistants don't.
- **"How does Devin handle our security and compliance requirements?"** Devin starts with access to nothing (clean-room) and receives scoped, service-account credentials for specific systems. It works on branches, never pushes to main, and humans approve every merge. Each change ships with auditable artifacts.
- **"Does it just bump versions, or does it actually fix things?"** Lab 2 shows triage, root-cause analysis, fixing breaking API changes (e.g., the `SecurityFilterChain` migration), and verifying with tests — not blind upgrades.
- **"Will this replace our engineers?"** No — it's team augmentation. Engineers shift from implementing to designing and reviewing; the deferred backlog gets done and headcount stays flat.
- **"How do we control cost / predict spend?"** Consumption-based (ACU). Volume of work maps to a unit-of-work estimate; walk through the numbers using [Value Narratives](../../../reference/general-themes/value-narratives.md) and the sales-track unit-of-work economics.
- **"Can multiple people work with the same session?"** Yes — multiple team members can comment on the same PR and Devin responds; the VM hibernates after inactivity and resumes on new feedback (see [Collaboration Model](../../../reference/general-themes/collaboration-model.md)).
- **"What about modernization?"** Supported and compelling, but lower priority for this client. Point to [Going Further](README.md#going-further) and the [Banking Modernization workshop](../banking-modernization-workshop/README.md).

---

## Next Steps Toward the Pilot

Close the session by aligning on the follow-up path:

1. **Collect target repositories and use cases** from the partner — the best brownfield candidates and the SDLC stages that hurt most.
2. **Customize the sandbox** — swap in client-representative repos, seed Knowledge notes with their standards, and stand up an org playbook from the Lab 1 SDLC flow.
3. **Prepare the joint customer workshop** — reuse this structure with the client's own repos and toolchain; wire the highest-impact MCP integration.
4. **Increase delivery-engineering involvement** during the pilot phase to scope volume, define success metrics (cycle time, throughput, MTTR), and set up event-driven and scheduled automations.

Log outcomes and any Knowledge/playbook assets created so the next session inherits them.
