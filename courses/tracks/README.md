# Tracks

## Overview

Three role-based reading tracks that build on the shared [Foundations](../foundations/) course. Each track targets a distinct persona in the partner systems integrator ecosystem and covers what that role needs to deliver effectively with Devin.

```
courses/
├── foundations/          ← Shared base (everyone completes this first)
└── tracks/
    ├── sales/            ← Operating model, cost structures, engagement capture
    ├── solutions/        ← System design, SDLC integration, live walkthroughs
    └── engineering/      ← Tactical platform usage, prompt engineering, orchestration

../workshops/             ← Hands-on practice (general, otterworks, by-tech-domain, by-tech-role)
```

---

## Progression Model

| Level | Content | Audience | Status |
|-------|---------|----------|--------|
| **Level 1: Foundations** | `courses/foundations/` | All personas | Exists |
| **Level 2: Tracks** | `courses/tracks/{sales,solutions,engineering}/` | Role-specific | Planned (this document) |
| **Level 3: Deep-Dives** | `workshops/by-tech-domain/` + `workshops/by-tech-role/` | Domain experts | Exists |

---

<a id="toc"></a>
## Table of Contents

- [Track: Sales](#track-sales)
- [Track: Solutions](#track-solutions)
- [Track: Engineering](#track-engineering)
- [Content Modalities](#content-modalities)
- [Level 3 Deep-Dives](#level-3-deep-dives)
- [Relationship to Existing Content](#relationship-to-existing-content)

---

<a id="track-sales"></a>
## Track: Sales

**Persona:** Sales engineers, account executives, practice leads, and P&L owners at SI partner firms who need to understand how the hybrid operating model changes staffing, bidding, and resource allocation — so they can capture work and price engagements competitively.

**Prerequisite:** Foundations workshop (Level 1)

### Planned Sections

| Section | What They'll Learn |
|---------|-------------------|
| Operating Model Transformation | How hybrid human + AI agent delivery changes staffing models, bench economics, and margin structure |
| Unit-of-Work Economics | The cost formula: `(ACUs per unit x $/ACU) + (Review hours per unit x Blended rate)`. How to estimate and bid |
| Value Narrative Selection | Which narrative resonates with which buyer persona (CTO, CISO, FinOps, Delivery Director) |
| Engagement Scoping & Estimation | Decomposing large efforts into standard units, estimating cost with confidence intervals |
| Competitive Positioning | Displacement risk — the SI without an AI-augmented operating model loses on cost, timeline, and quality simultaneously |
| The Decision Framework for Sales | Using the "what to give AI" framework to scope what's agent-executable in a proposal vs. what remains human-delivered |

### Modalities Required

- Reading material (reference: `courses/tracks/sales/03-value-narrative-selection.md`; quick-reference: `reference/general-themes/value-narratives.md`)
- Knowledge checks (multiple choice/response after each section)
- Case study exercises (estimate an engagement, select a narrative, scope a campaign)
- ROI estimation worksheets

### Key Gaps to Fill

- [ ] Cost modeling exercises with worked examples
- [ ] Competitive scenario handling (battle cards / objection responses)
- [ ] Sample engagement proposals showing hybrid staffing models
- [ ] Video: recorded example of a delivery pitch or client conversation
- [ ] ROI calculator tool or worksheet template

---

<a id="track-solutions"></a>
## Track: Solutions

**Persona:** Solutions engineers and architects at SI partner firms who design systems and operational processes within the SDLC, and who need to demonstrate the full capabilities of Devin in client-facing simulated environments.

**Prerequisite:** Foundations workshop (Level 1)

### Planned Sections

| Section | What They'll Learn |
|---------|-------------------|
| Platform Architecture Mastery | Clean-room execution, shared context layer, session lifecycle, verification model — know it cold for client questions |
| SDLC Integration Design | Where Devin fits in the development lifecycle: event-driven triggers, scheduled maintenance, PR-based delivery |
| Live Walkthrough Delivery | How to run each walkthrough type end-to-end (security remediation, migration, data engineering) in a simulated environment |
| Automation Topology Design | Given client requirements, design the webhook/schedule/trigger architecture |
| Full Platform Configuration | Configure a complete Devin org from scratch: blueprints, Knowledge, Playbooks, MCP servers, Secrets, Git connections, Automations |
| Handling Unexpected Outcomes | Recovery patterns when the agent gets stuck, CI fails, or the walkthrough goes off-script |

### Modalities Required

- Reading material (reference: `courses/tracks/solutions/01-platform-architecture-mastery.md`, `02-sdlc-integration-design.md`, `04-automation-topology-design.md`; quick-reference: `reference/general-themes/`)
- Knowledge checks (architecture questions, "which pattern for this scenario?")
- Design challenges (design an automation topology, configure an org)
- Live walkthrough delivery in a simulated environment (uses `demos/` content as the script)
- Walkthrough delivery rubric (grading criteria for competent delivery)

### Key Gaps to Fill

- [ ] Simulated environment setup guide (workshop org configured for live practice)
- [ ] Walkthrough delivery rubric with pass/fail criteria
- [ ] Recorded reference walkthroughs (video of ideal delivery for self-study)
- [ ] Integration design templates (webhook topologies, trigger architectures)
- [ ] "Handling the hard questions" guide (common client questions + answers about architecture, security, data residency)
- [ ] Platform configuration lab (step-by-step org setup from zero)

---

<a id="track-engineering"></a>
## Track: Engineering

**Persona:** Engineers and developers at SI partner firms who use Devin to execute their already-defined jobs — by knowing features, tactical best practices, and core architecture decisions (e.g., when to use Ubuntu vs. Windows host, how to configure environments, how to write effective prompts).

**Prerequisite:** Foundations workshop (Level 1)

### Planned Sections

| Section | What They'll Learn |
|---------|-------------------|
| Platform Fundamentals | Session lifecycle, cloud vs. local decision tree, PR feedback loop, verification model |
| Prompt Engineering for Agents | What makes a good prompt (repo, paths, acceptance criteria, constraints, verification); anti-patterns; progressive examples |
| Environment & Architecture Decisions | Ubuntu vs. Windows host selection, blueprint configuration, network connectivity for private resources, secret management, MCP server setup |
| The Task Selection Framework | Applying "what to give AI" in practice: verifiability, clarity, judgment level; gray-zone decomposition into stages |
| Orchestration Patterns in Practice | When to use single-agent vs. parent-child vs. event-driven vs. scheduled; configuring each |
| Working with Agent Output | PR review techniques, effective feedback comments, iteration patterns, when to escalate |
| Tactical Tips & Troubleshooting | Common failure modes, debugging failing sessions, Knowledge notes that improve success rate, Playbook design |

### Modalities Required

- Reading material (reference: Foundations 01-09; quick-reference: `reference/general-themes/when-to-use-devin.md`, `cloud-vs-local-agents.md`)
- Knowledge checks (the critical missing modality — "Is this task good for an agent?", "What's wrong with this prompt?")
- Challenge-mode labs (write your OWN prompt for a given scenario — no copy-paste provided)
- Debug exercises (diagnose what went wrong in a described session failure)
- Hands-on walkthroughs (existing `workshops/general/` labs serve as practice material)

### Key Gaps to Fill

- [ ] Challenge-mode labs with grading rubrics (scenarios where no prompt is provided)
- [ ] Knowledge checks for each Foundations section (3-5 questions per doc)
- [ ] Tactical platform operations guide (Ubuntu vs. Windows, blueprint config, network patterns, secret management)
- [ ] "Debug a failing session" exercises with scenarios and diagnosis
- [ ] Progressive prompt examples (bad → acceptable → great) with explanations
- [ ] Video: recorded session walkthrough showing the PR feedback loop in action

---

<a id="content-modalities"></a>
## Content Modalities

Each track uses a mix of modalities. The table below shows the target distribution:

| Modality | Sales | Solutions | Engineering | Exists Today? |
|----------|-------|-----------|----------|---------------|
| Reading material | 30% | 20% | 25% | Yes (heavy) |
| Video (recorded) | 15% | 20% | 10% | No |
| Knowledge checks | 20% | 15% | 20% | No |
| Guided walkthroughs | 10% | 15% | 25% | Yes (workshops/general, demos/) |
| Challenges / case studies | 25% | 30% | 20% | Minimal |

**Current state:** ~40% reading, ~55% guided walkthroughs, ~5% open challenges, 0% video, 0% knowledge checks.

**Target state:** Balanced across all five modalities, with role-appropriate emphasis.

---

<a id="level-3-deep-dives"></a>
<a id="level-3-specialties"></a>
## Level 3: Deep-Dives

Level 3 content lives in two directories:

```
workshops/by-tech-domain/
├── modernization/        ← COBOL, .NET, Framework Upgrades, Data Migration, Platform Decomposition
├── security/             ← Compliance, Enterprise Automation
└── ai-and-automation/    ← Agentic AI

workshops/by-tech-role/
├── development/          ← App Maintenance, Feature Development
├── digital-engineering/  ← CI/CD, cloud infrastructure, observability
└── quality-engineering/  ← Engineering, Engineering + Security
```

**Planned additions for Level 3:**

| Specialty | Track Prerequisite | Focus |
|-----------|-------------------|-------|
| Security Automation Specialist | Engineering | Event-driven SAST, compliance remediation at scale, scan-fix-verify loops |
| Modernization Specialist | Engineering | COBOL→Java, .NET cloud-native, framework upgrades, platform decomposition |
| Data Migration Specialist | Engineering | SAS→Snowflake/Databricks, ETL translation, reconciliation harness design |
| Enterprise Integration Architect | Solutions | MCP server design, multi-tool orchestration, network connectivity patterns |

These will be built out as Level 2 tracks mature.

---

<a id="relationship-to-existing-content"></a>
## Relationship to Existing Content

| Existing Content | Role in Track Structure |
|-----------------|----------------------|
| `courses/foundations/` | Level 1 prerequisite for all tracks — comprehensive training for all core topics |
| `reference/general-themes/` | Quick-reference cheat sheets for lab use — condensed versions pointing back to foundations and tracks |
| `courses/tracks/sales/` | Comprehensive value narratives, operating model, unit economics (supersedes GT `value-narratives.md` for depth) |
| `courses/tracks/solutions/` | Comprehensive architecture, SDLC integration, automation topology (supersedes GT `architecture-strengths.md`, `design-patterns-for-devin.md` for depth) |
| `courses/tracks/engineering/` | Comprehensive platform fundamentals, task selection, orchestration (supersedes GT `when-to-use-devin.md`, `cloud-vs-local-agents.md` for depth) |
| `workshops/general/` | Practice labs for Engineering track |
| `demos/` | Walkthrough scripts for Solutions track |
| `workshops/by-tech-domain/` | Level 3 deep-dives by technical domain |
| `workshops/by-tech-role/` | Level 3 deep-dives by technical role |
| `labs/` | Building blocks composed into walkthroughs and challenges |
