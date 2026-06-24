# Inline Documentation

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
  - [ts-java-spring-boot-realworld](#ts-java-spring-boot-realworld)
  - [uc-dw-migration-teradata-to-snowflake](#uc-dw-migration-teradata-to-snowflake)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

Pick a repo below, copy the **Step 1** prompt into a new Devin session, and let Devin add meaningful inline documentation. No prerequisites beyond repo access. This is a beginner-friendly module.

---

## Challenge

Enhance documentation in a codebase so that code is more readable and maintainable. This includes inline documentation (JSDoc/Javadoc/docstrings), README improvements, architecture decision records (ADRs), and API documentation from code.

## Target Outcomes

- Comprehensive inline documentation added to public methods and classes
- README improved with setup instructions, architecture overview, or contribution guide
- Documentation focuses on the "why" not just the "what"
- PR with documentation changes

## What Participants Will Learn

- How Devin understands code intent and generates meaningful documentation
- Quality of AI-generated documentation vs. boilerplate
- How to guide Devin to produce documentation at the right level of detail

## Devin Features Exercised

- Deep codebase understanding
- Large-scale file editing
- PR creation

## Difficulty

Beginner

## Estimated Time

30 minutes

---

## Repositories

### <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Moderate codebase with a mix of frontend (React) and backend (Express) — benefits from JSDoc comments and API documentation.

#### Step 1: Paste into Devin

```
Review timesheet-app and add comprehensive inline documentation (JSDoc) to all public functions in the backend API routes and service layer. Also add TSDoc comments to the main React components. Focus on explaining the "why" not just the "what".
```

#### Step 2: Research with Ask Devin

- *"Which parts of timesheet-app are most confusing for a new developer? Where would documentation have the highest impact?"*
- *"Should the README be updated with an architecture diagram or setup guide?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page — compare the auto-generated documentation with what's in the code. Identify gaps where inline documentation would complement DeepWiki's output.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — is the documentation accurate? Does it explain intent or just restate the code?
- **Leave a comment** asking Devin to improve a specific doc comment that's too generic

---

### <a id="ts-java-spring-boot-realworld"></a>ts-java-spring-boot-realworld

**Repository:** [ts-java-spring-boot-realworld](https://github.com/Cognition-Partner-Workshops/ts-java-spring-boot-realworld)

Java Spring Boot codebase — benefits from Javadoc comments on services, controllers, and domain models.

#### Step 1: Paste into Devin

```
Review ts-java-spring-boot-realworld and add comprehensive Javadoc comments to all public classes and methods in the service and controller layers. Include parameter descriptions, return values, and exception documentation.
```

#### Step 2: Research with Ask Devin

- *"What are the most complex service methods that would benefit most from documentation?"*
- *"Should we also add package-level documentation (package-info.java) for each module?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the domain model. Use this to write documentation that explains the business domain, not just the technical implementation.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the Javadoc follow standard conventions? Are @param and @return tags used correctly?
- **Leave a comment** asking Devin to add an ADR explaining a specific architectural decision

---

### <a id="uc-dw-migration-teradata-to-snowflake"></a>uc-dw-migration-teradata-to-snowflake

**Repository:** [uc-dw-migration-teradata-to-snowflake](https://github.com/Cognition-Partner-Workshops/uc-dw-migration-teradata-to-snowflake)

SQL-heavy data warehouse repo — benefits from header comments on DDL/DML files, data dictionary documentation, and schema documentation.

#### Step 1: Paste into Devin

```
Review uc-dw-migration-teradata-to-snowflake and add comprehensive header comments to all SQL files in ddl/ and dml/ directories. Each file should document: purpose, input/output tables, key business rules, and Teradata-specific features used. Also create a DATA_DICTIONARY.md from the schema definitions.
```

#### Step 2: Research with Ask Devin

- *"What are the business rules embedded in the stored procedures and macros? Can you extract them into a business rules document?"*
- *"Can you generate an ER diagram description from the DDL files?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the data warehouse schema relationships and ETL flow. Use this to improve the documentation with data lineage information.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the SQL comments accurately describe the business logic?
- **Leave a comment** asking Devin to document the ETL dependencies between stored procedures

---

## Key Takeaways

- Devin generates inline documentation that reflects code intent — it reads the implementation, surrounding context, and naming conventions to produce comments that explain "why" rather than restating "what"
- Documentation quality scales with prompt specificity: asking Devin to "focus on the why" produces meaningfully better output than a generic "add docs" prompt
- Inline documentation is a high-volume, low-risk task that Devin handles efficiently — participants can evaluate quality without worrying about functional correctness

## Going Further

Inline documentation is a natural fit for **documentation-on-code-change triggers** (see [When to Use Devin → Capacity-Constrained](../../reference/general-themes/when-to-use-devin.md)):

- **PR-triggered doc updates** — When a PR adds or modifies public functions, trigger a Devin session that adds or updates inline documentation for the changed code and opens a follow-up PR
- **Scheduled documentation coverage audits** — Run a recurring session that identifies public functions and classes without documentation (using tools like `eslint-plugin-jsdoc` or Checkstyle Javadoc rules) and opens PRs to fill the gaps
- **Documentation drift detection** — Periodic sessions compare inline documentation against the actual implementation, flagging comments that have become stale after code refactors
