# Data Quality & Validation

## Table of Contents

- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [uc-data-source-migration-jdbc-normalization](#uc-data-source-migration-jdbc-normalization)
- [Going Further](#going-further)

---

## Challenge

Add data quality checks, schema validation, and data contracts to an existing Spring Boot loan service application that reads from a legacy data warehouse schema. Participants will analyze the data pipeline's input/output flow and build a validation framework that catches data integrity issues across the legacy-to-modern migration boundary.

This exercise uses **non-invasive static analysis**: Devin reads the existing schema definitions, column mappings, and seed data in the repository to infer validation rules — no connection to a live database or production system is required.

## Quick Start

1. Open Devin and connect to the **uc-data-source-migration-jdbc-normalization** repository
2. Paste the [Step 1 prompt](#step-1-paste-into-devin) into Devin
3. Watch Devin analyze the data pipeline, generate validation rules, and open a PR with quality checks and a data quality report

## Repositories

- [uc-data-source-migration-jdbc-normalization](#uc-data-source-migration-jdbc-normalization)

---

## Target Outcomes

- Data quality validation framework added to the loan service pipeline
- Schema validation tests for input/output data covering all legacy CDW tables
- Data contract definitions specifying expectations on data shape, types, and value ranges
- PR with quality checks, test results, and a `DATA_QUALITY_REPORT.md`

## What Participants Will Learn

- How Devin analyzes existing data flows and identifies quality risk points
- How Devin generates validation rules from schema definitions and mapping documents
- How Devin builds test frameworks that validate data transformations across schema boundaries
- The importance of data contracts in migration projects

## Devin Features Exercised

- Data pipeline analysis and flow comprehension
- Test generation for data validation
- Schema understanding and constraint inference
- PR creation with quality documentation

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## <a id="uc-data-source-migration-jdbc-normalization"></a>uc-data-source-migration-jdbc-normalization

**Repository:** [uc-data-source-migration-jdbc-normalization](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-jdbc-normalization)

Spring Boot 3.2 / Java 17 loan management service with legacy CDW tables (all-VARCHAR, cryptic column names, denormalized). Includes column mappings in `data/mappings/column_mappings.md`, modern target schema DDL, and 5 legacy seed data records per table.

### Step 1: Paste into Devin

```
Analyze the data flow in uc-data-source-migration-jdbc-normalization from legacy CDW tables through LoanService.java to the API DTOs. Using the column mappings in data/mappings/column_mappings.md and the legacy schema in src/main/resources/schema-legacy.sql, build a data quality validation framework: (1) Create validation rules for each legacy table — check for null required fields, valid date formats (MM/DD/YYYY), parseable amounts, valid status codes (ACT/CLO/DFT/FRB), and referential integrity between CDW_LN_ACCT and CDW_BORR_MSTR. (2) Add schema contract tests that verify the shape and types of API responses from /api/loans and /api/borrowers. (3) Create a data quality report service that scores each record's quality. Write a DATA_QUALITY_REPORT.md summarizing the validation rules and findings.
```

### Step 2: Research with Ask Devin

- *"What data quality issues exist in the legacy CDW tables in uc-data-source-migration-jdbc-normalization? Check for null values, inconsistent formats, orphaned records, and invalid codes in data-legacy.sql."*
- *"What validation rules should be applied to the column transformations defined in data/mappings/column_mappings.md? Which transformations are most likely to produce data loss or corruption?"*
- Use the analysis to refine your validation framework to focus on the highest-risk transformations

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the full data model — legacy CDW tables, the translation layer in LoanService.java, and the clean DTO contracts. Identify which fields go through the most complex transformations (amount parsing, date conversion, code expansion) as validation priorities.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the validation rules cover all the transformation patterns in column_mappings.md? Are edge cases handled (empty strings, malformed dates, amounts with extra characters)?
- **Leave a comment** asking Devin to add a continuous validation mode that runs quality checks on every API request and logs violations to a separate table
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- **Static analysis of data flows**: Devin traces the data path from legacy CDW tables through service-layer transformations to API DTOs without requiring a running database connection
- **Validation rule generation**: Schema definitions and column mappings provide enough information for Devin to infer meaningful validation rules automatically
- **Data contracts as migration guardrails**: Explicit contracts on data shape and types catch regressions early — especially valuable when running legacy and modern schemas in parallel
- **Parallel validation workstreams**: Each legacy table's validation rules can be developed independently, making this a candidate for [child-session parallelism](../../shared/general-themes/design-patterns-for-devin.md#pattern-3-divide-and-conquer-with-child-agents) in larger estates

---

## Going Further

### Automation and Webhooks

Configure a CI trigger so that data quality checks run automatically on every PR that modifies schema files or column mappings. When checks fail, a Devin session can investigate and propose fixes. See [Design Patterns → Event-Driven Triggers](../../shared/general-themes/design-patterns-for-devin.md#pattern-2-event-driven-triggers).

### Scheduled Sessions

Schedule a weekly Devin session to run the full data quality suite against the latest seed data and report any drift in data contracts. See [Platform Capabilities → Scheduled Sessions](../../shared/general-themes/platform-capabilities.md#scheduled-sessions).

### Shared Context Layer

Create organization-level [knowledge notes](../../shared/general-themes/architecture-strengths.md#shared-context-layer) documenting your data quality standards (e.g., "All date fields must be validated against MM/DD/YYYY format", "Status codes must belong to the approved enumeration"). Every Devin session inherits these conventions automatically.

### Team-Based Operation

Multiple team members — data engineers, QA analysts, and domain experts — can review the generated validation rules simultaneously via PR comments. Devin reads feedback from any reviewer and iterates. See [Collaboration Model → Multi-User Communication](../../shared/general-themes/collaboration-model.md#multi-user-communication).
