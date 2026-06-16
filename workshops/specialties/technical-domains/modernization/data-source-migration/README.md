# Workshop: Data Source Migration

## Overview

| | |
|---|---|
| **Focus** | Rewriting a legacy data source to a modern schema and reconnecting the application with comprehensive testing |
| **Duration** | 1-2 hours |
| **Audience** | Database engineers, backend developers, data migration teams |
| **Key Modules** | [Data Source Migration](../../../../../../../modules/data-engineering/data-source-migration.md) |

## Workshop Narrative

Data source migration is not just schema work — the application must be rewired and the entire pipeline validated. This workshop covers both parts: (1) rewriting the legacy data source to a modern schema, and (2) updating the application to use the new data source, with testing at every step.

## Getting the Most from This Workshop

> **Devin works asynchronously on its own machine.** Once you paste a prompt and kick off a session, Devin runs independently — you don't need to watch it. Move on to the next lab, explore Ask Devin, or grab coffee while it works. You'll get notified when it opens a PR.

A few tips to maximize your hands-on time:

- **Start sessions early, review later.** Kick off the session first, then use the wait time for Ask Devin research or reading DeepWiki — Devin keeps working in the background.
- **Use Ask Devin to refine requirements.** The better-defined a task is, the better Devin's output. Ask Devin helps you think through the problem before Devin executes.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this is how teams build a shared context layer that compounds over time.
- **Leave PR comments to steer Devin.** After Devin opens a PR, you can leave comments and Devin will wake up and address them — this is the core feedback loop.

## Table of Contents

- [Lab 1 — Data Source Rewrite + App Reconnection + Testing](#lab-1--data-source-rewrite--app-reconnection--testing)
- [Repos Required](#repos-required)
- [Key Takeaways](#key-takeaways)

---

## Labs

### Lab 1 — Data Source Rewrite + App Reconnection + Testing

- **Module:** [Data Source Migration](../../../../../../../modules/data-engineering/data-source-migration.md)
- **Repository:** [uc-data-source-migration-jdbc-normalization](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-jdbc-normalization)
- **Objective:** Transform a legacy data warehouse (all-VARCHAR, denormalized) to a modern normalized schema, rewire the app, and validate with comprehensive tests
- **Duration:** 60 min

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

```
Review the legacy CDW schema in uc-data-source-migration-jdbc-normalization. This loan management app reads from denormalized tables where all columns are VARCHAR.

Part 1 — Rewrite the data source: Create modern JPA entities matching data/modern-schema/modern_tables.sql with proper types (LocalDate, BigDecimal, Long, enums). Write a migration service that transforms legacy data per data/mappings/column_mappings.md. Add data reconciliation tests that verify record counts and field values.

Part 2 — Reconnect the app: Update LoanService.java to use modern repositories. Add golden-file validation tests that capture API responses before and assert they match after.

Create a `TESTING_REPORT.md` documenting what tests were added and what they validate.
```

#### Step 2: Research with Ask Devin

- *"What are the riskiest data type conversions — VARCHAR dates to LocalDate, amounts to BigDecimal? What edge cases could cause data loss?"*
- *"How should we validate that no records were lost or corrupted? What reconciliation checks should we run?"*
- *"What's the best way to implement a dual-read feature flag for safe rollout?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the domain model and API contracts. Try adding dual-read feature flags, data reconciliation dashboards, or complete data lineage maps.

#### Step 4 (Optional): Review & Give Feedback

Focus on the testing story: data transformation correctness, reconciliation tests, golden-file validation, and app reconnection completeness.

**Target Outcomes:** Modern JPA entities, data migration service, rewired service layer, golden-file validation, reconciliation tests, testing report

## Repos Required

- [ ] uc-data-source-migration-jdbc-normalization

## Key Takeaways

- **"Rewrite + reconnect + test"** — data source migration requires schema work, app rewiring, and pipeline validation
- **"Testing proves the migration didn't break anything"** — golden-file validation, reconciliation checks, and type-conversion tests
- **"Feature flags enable safe rollout"** — dual-read mode lets you switch between data sources at runtime
