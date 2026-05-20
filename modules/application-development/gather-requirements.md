# Gather Requirements

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
  - [calcom](#calcom)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

1. Pick a repository below
2. Paste the sample prompt into Devin (or describe your own feature idea)
3. Observe how Devin asks clarifying questions and analyzes the codebase to scope the feature
4. Review the GitHub Issue Devin creates and refine the requirements

---

## Challenge

Ideate a feature enhancement or addition, and work with Devin to gather requirements and scope the feature while considering impacts. This challenge emphasizes Devin's planning and reasoning capabilities — no code changes required.

## Target Outcomes

- Feature requirements documented as a GitHub Issue with acceptance criteria
- Impact analysis: affected components, APIs, database changes
- Edge cases and constraints identified
- Technical design outline

## What Participants Will Learn

- How Devin asks clarifying questions to refine vague requirements
- Devin's ability to analyze existing code to identify impacts
- How Devin structures requirements (user stories, acceptance criteria, technical design)
- The value of using Devin for planning before coding
- How **shared context** (knowledge notes, codebase familiarity) helps Devin produce better requirements (see [Architecture Strengths → Shared Context Layer](../../shared/general-themes/architecture-strengths.md#shared-context-layer))

## Devin Features Exercised

- Interactive dialogue (follow-up questions)
- Codebase analysis for impact assessment
- Documentation generation
- Ask Devin (if used for requirements gathering)

## Difficulty

Beginner

## Estimated Time

30 minutes

---

## Repositories

### <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Simple domain (timesheets, clients, work entries) — easy to ideate features like invoice generation, project hierarchy, team views, or reporting dashboards.

#### Step 1: Paste into Devin

```text
I want to add invoice generation to timesheet-app. Help me gather requirements: what data do we need, what's the UI flow, what APIs need to change, what new database tables are needed? Consider edge cases and constraints. Document the requirements as a GitHub Issue with acceptance criteria.
```

#### Step 2: Research with Ask Devin

- *"What features are missing from timesheet-app that a typical timesheet application would have?"*
- *"If I wanted to add project/task hierarchy to group work entries, what would the data model look like?"*
- Use the gathered requirements as input for a coding session (see [New Feature Development](new-feature-development.md))

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the existing data model and API surface. Use this to identify extension points and constraints for new features.

#### Step 4 (Optional): Review & Give Feedback

- **Review the GitHub Issue** — are the acceptance criteria specific and testable?
- **Leave a comment** asking Devin to add a technical design section with API contract sketches
- Use the requirements to start a coding session with clear, pre-gathered specs

---

### <a id="calcom"></a>calcom

**Repository:** [calcom](https://github.com/Cognition-Partner-Workshops/calcom)

Rich scheduling domain with many extension points — custom booking field types, webhook integrations, analytics dashboards, notification preferences.

#### Step 1: Paste into Devin

```text
I want to add a booking analytics dashboard to calcom that shows: bookings per day/week/month, no-show rates, average booking lead time, and most popular time slots. Help me gather requirements: what data sources exist, what aggregations are needed, and what UI components should we use? Document the requirements as a GitHub Issue.
```

#### Step 2: Research with Ask Devin

- *"What data is available in calcom's database that could power an analytics dashboard?"*
- *"What existing dashboard or reporting patterns does calcom use that we should follow?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the booking data model and existing analytics capabilities. Identify which database tables contain the data needed for the dashboard.

#### Step 4 (Optional): Review & Give Feedback

- **Review the GitHub Issue** — are the requirements feasible given calcom's architecture?
- **Leave a comment** asking Devin to add wireframe descriptions or user flow diagrams

---

## Key Takeaways

- Devin can act as a requirements analyst — asking clarifying questions, analyzing codebases for impact, and producing structured specifications
- Well-gathered requirements (acceptance criteria, data model, API contracts) lead to higher-quality implementations in subsequent coding sessions
- The PR feedback loop applies to documentation too — reviewers can comment on the requirements and Devin will refine them
- Shared context (knowledge notes describing domain terminology and architecture) helps Devin produce more accurate requirements

---

## Going Further

- **Requirements-to-implementation pipeline** — use gathered requirements as input for a [New Feature Development](new-feature-development.md) session, creating a two-session workflow: plan → implement
- **Playbook-driven requirements gathering** — create a playbook that encodes your team's requirements process (stakeholder questions → impact analysis → acceptance criteria → technical design) so every feature starts with consistent documentation (see [Platform Capabilities → Playbooks](../../shared/general-themes/platform-capabilities.md#playbooks))
- **Ticket-driven requirements** — when a new Jira epic is created, trigger Devin to gather requirements automatically and post a detailed specification as a comment on the ticket (see [Design Patterns → Event-Driven Triggers](../../shared/general-themes/design-patterns-for-devin.md#pattern-2-event-driven-triggers))
