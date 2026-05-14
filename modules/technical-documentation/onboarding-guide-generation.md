# Onboarding Guide Generation

## Repositories

- [uc-framework-upgrade-monolith-to-microservices](#uc-framework-upgrade-monolith-to-microservices)
- [calcom](#calcom)

---

## Challenge

Create comprehensive developer onboarding guides from repo analysis. The guide should help a new developer understand the codebase architecture, setup process, development workflow, and key patterns used throughout the project.

## Target Outcomes

- Developer onboarding guide covering architecture overview, local setup, and development workflow
- Codebase map showing key directories, modules, and their responsibilities
- Common task guides (adding a feature, fixing a bug, running tests)
- All onboarding documents placed in `docs/onboarding/`
- PR with onboarding documentation

## What Participants Will Learn

- How Devin explores and maps unfamiliar codebases to identify key architectural patterns
- How Devin generates structured documentation from code analysis
- How Devin identifies setup steps, dependencies, and common development workflows
- The value of asking Devin to target specific audiences (new hires, contractors, etc.) in prompts

## Devin Features Exercised

- Codebase analysis and architecture mapping
- Documentation generation and technical writing
- Developer experience design
- PR creation with documentation artifacts

## Difficulty

Intermediate

## Estimated Time

45 minutes

## Notes

- Encourage participants to compare Devin's generated guide against their own onboarding experience — what did Devin miss or get right?
- Good follow-up: have a participant who is unfamiliar with the repo try to follow the generated guide
- The guide should be specific to the repo, not generic — it should reference actual file paths, commands, and patterns

---

## <a id="uc-framework-upgrade-monolith-to-microservices"></a>uc-framework-upgrade-monolith-to-microservices

**Repository:** [uc-framework-upgrade-monolith-to-microservices](https://github.com/Cognition-Partner-Workshops/uc-framework-upgrade-monolith-to-microservices)

Spring Boot monolith with a Next.js frontend — a complex full-stack application ideal for testing onboarding guide quality.

### Step 1: Paste into Devin

> Analyze the uc-framework-upgrade-monolith-to-microservices repository and create a comprehensive developer onboarding guide in `docs/onboarding/`. Generate: (1) `architecture-overview.md` — high-level architecture diagram (in markdown/mermaid) showing backend services, frontend, database, and their interactions, (2) `local-setup-guide.md` — step-by-step setup instructions with prerequisites, environment variables, and verification steps, (3) `codebase-map.md` — directory-by-directory guide explaining what each module does and where to find key files, and (4) `common-tasks.md` — how-to guides for adding a new API endpoint, adding a frontend page, running tests, and debugging common issues. Open a PR.

### Step 2: Research with Ask Devin

- *"What are the main architectural layers in uc-framework-upgrade-monolith-to-microservices? How do the backend and frontend communicate?"*
- *"What build tools, test frameworks, and development scripts are available? What does a typical development workflow look like?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to cross-reference Devin's generated architecture overview. Check if any modules or patterns were missed in the onboarding guide.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the setup guide include all prerequisites and environment variables needed to run the app locally?
- **Leave a comment** asking Devin to add a troubleshooting section covering common setup failures (port conflicts, missing dependencies, database issues)
- **Watch Devin respond** and push a follow-up commit

---

## <a id="calcom"></a>calcom

**Repository:** [calcom](https://github.com/Cognition-Partner-Workshops/calcom)

Large-scale open-source scheduling platform with a complex monorepo structure — a challenging codebase for onboarding guide generation.

### Step 1: Paste into Devin

> Analyze the calcom repository and create a developer onboarding guide in `docs/onboarding/`. Generate: (1) `architecture-overview.md` — high-level architecture showing the monorepo structure, key apps (web, api/v1, api/v2), shared packages, and how they interact, (2) `local-setup-guide.md` — step-by-step setup covering Node.js/Yarn prerequisites, database setup, environment configuration, and verification, (3) `codebase-map.md` — guide to the monorepo layout explaining what each app and package does, and (4) `common-tasks.md` — how-to guides for adding a new tRPC endpoint, creating a new UI component, modifying the database schema, and running tests. Open a PR.

### Step 2: Research with Ask Devin

- *"What is the monorepo structure of Cal.com? What are the key apps and packages, and how do they depend on each other?"*
- *"What is the development workflow for Cal.com? How do Turborepo, Prisma, and tRPC fit together in the build and development process?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the full monorepo architecture. A project this large will have patterns and conventions that are critical for the onboarding guide to capture.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the codebase map accurately capture the monorepo structure and package dependencies?
- **Leave a comment** asking Devin to add a section on the embed system and how platform integrations work
- **Watch Devin respond** and push a follow-up commit
