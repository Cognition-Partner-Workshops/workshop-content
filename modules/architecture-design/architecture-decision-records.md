# Architecture Decision Records

## Repositories

- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)
- [timesheet-app](#timesheet-app)

---

## Challenge

Analyze an existing codebase to identify key architectural decisions, then generate a set of Architecture Decision Records (ADRs) following the MADR template that document context, decision rationale, status, and consequences.

## Target Outcomes

- Set of ADR documents in `docs/adr/` following the MADR template
- Architecture diagram or description generated from code analysis
- List of identified architectural patterns and their rationale
- PR with ADR documents and any recommended architecture improvements

## What Participants Will Learn

- How Devin performs deep codebase analysis to infer architectural decisions
- How Devin recognizes architectural patterns (layering, API styles, data access strategies)
- Devin's ability to produce structured technical documentation from code
- How to evaluate and refine AI-generated architectural documentation

## Devin Features Exercised

- Deep codebase analysis
- Architectural pattern recognition
- Technical writing and documentation generation
- PR creation

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot monolith with both REST and GraphQL APIs, DGS framework integration, and multiple data access patterns — rich ground for architectural decision documentation.

### Step 1: Paste into Devin

> Analyze the architecture of uc-spring-boot-upgrade-microservice-extraction. This is a Spring Boot monolith that uses both REST controllers and GraphQL via the DGS framework. Identify the key architectural decisions — including why both REST and GraphQL coexist, the choice of the DGS framework, data access patterns (MyBatis vs JPA), the security model, and the monolithic structure. Generate Architecture Decision Records (ADRs) in `docs/adr/` following the MADR template (Title, Status, Context, Decision, Consequences). Also produce a high-level architecture description in `docs/adr/README.md`. Open a PR with all ADR documents.

### Step 2: Research with Ask Devin

- *"What are the main architectural layers in uc-spring-boot-upgrade-microservice-extraction, and how do requests flow from the API layer to the database?"*
- *"Why does this application use both REST and GraphQL? Are there specific endpoints that are only available through one API style?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the full architecture — the dual API surface, data access layer, and security configuration. Identify which design decisions are explicit (documented) versus implicit (inferred from code structure).

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the ADRs accurately capture the real decisions, or are they generic boilerplate?
- **Leave a comment** asking Devin to add an ADR for a specific decision you notice (e.g., the choice of SQLite for development, the frontend/backend separation)
- **Watch Devin respond** and push a follow-up commit

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express backend with a React frontend — a different tech stack that exercises Devin's ability to recognize architecture decisions across languages and frameworks.

### Step 1: Paste into Devin

> Analyze the architecture of timesheet-app. This is a Node.js/Express + React application. Identify the key architectural decisions — including the frontend/backend separation strategy, API design approach, authentication mechanism, database choice, and state management patterns. Generate Architecture Decision Records (ADRs) in `docs/adr/` using the MADR template (Title, Status, Context, Decision, Consequences). Include an `docs/adr/README.md` that summarizes the overall architecture. Open a PR with all ADR documents.

### Step 2: Research with Ask Devin

- *"How is the timesheet-app project structured? What is the separation between frontend and backend, and how do they communicate?"*
- *"What database and ORM/data access patterns does timesheet-app use? Are there any architectural trade-offs visible in the codebase?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the project structure and technology choices. Look for patterns that reveal implicit architectural decisions — middleware chains, folder organization, and configuration files.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the ADRs specific to this codebase or could they apply to any Express app?
- **Leave a comment** asking Devin to strengthen the "Consequences" section of a specific ADR with concrete trade-offs observed in the code
- **Watch Devin respond** and push a follow-up commit
