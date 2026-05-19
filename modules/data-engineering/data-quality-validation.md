# Data Quality & Validation

## Repositories

- [uc-data-source-migration-jdbc-normalization](#uc-data-source-migration-jdbc-normalization)

---

## Challenge

Add data quality checks, schema validation, and data contracts to an existing Spring Boot loan service application that reads from a legacy data warehouse schema. Participants will analyze the data pipeline's input/output flow and build a validation framework that catches data integrity issues across the legacy-to-modern migration boundary.

## Target Outcomes

- Data quality validation framework added to the loan service pipeline
- Schema validation tests for input/output data covering all legacy CDW tables
- Data contract definitions specifying expectations on data shape, types, and value ranges
- PR with quality checks, test results, and a DATA_QUALITY_REPORT.md

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

> Analyze the data flow in uc-data-source-migration-jdbc-normalization from legacy CDW tables through LoanService.java to the API DTOs. Using the column mappings in data/mappings/column_mappings.md and the legacy schema in src/main/resources/schema-legacy.sql, build a data quality validation framework: (1) Create validation rules for each legacy table — check for null required fields, valid date formats (MM/DD/YYYY), parseable amounts, valid status codes (ACT/CLO/DFT/FRB), and referential integrity between CDW_LN_ACCT and CDW_BORR_MSTR. (2) Add schema contract tests that verify the shape and types of API responses from /api/loans and /api/borrowers. (3) Create a data quality report service that scores each record's quality. Write a DATA_QUALITY_REPORT.md summarizing the validation rules and findings. Open a PR.

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
