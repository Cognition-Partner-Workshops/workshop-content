# Data Source Migration

## Repositories

- [uc-data-source-migration-jdbc-normalization](#uc-data-source-migration-jdbc-normalization)

---

## Challenge

Migrate a running application's data source from a legacy data warehouse schema (denormalized, all-VARCHAR, cryptic column names) to a modern normalized schema with proper types, foreign keys, and clear naming — while keeping the API endpoints functioning identically.

## Legacy Schema Characteristics

The app currently reads from legacy CDW-style tables that exhibit common data warehouse antipatterns:

- **All-VARCHAR typing** — dates as `MM/DD/YYYY` strings, amounts as `"285,000"` strings
- **Cryptic column names** — `BORR_FST_NM`, `LN_CURR_BAL`, `PMT_ESCROW_AMT`
- **Denormalized** — borrower fields duplicated inside loan account records
- **No foreign keys** — relationships implied but not enforced
- **Code abbreviations** — `ACT`, `CLO`, `DFT`, `FRB` for status values

## Target Outcomes

- Modern JPA entities with proper types and FK relationships
- Data migration script that transforms and loads all records
- Service layer rewired to modern repositories (no more string parsing)
- Golden-file validation: API responses match pre-migration baseline
- `DATA_SOURCE_MIGRATION_NOTES.md` documenting decisions

## What Participants Will Learn

- How Devin understands and maps between legacy and modern schemas
- How Devin handles data type conversions (string to proper types)
- The importance of golden-file testing during data source migrations
- How to validate migration correctness at the API level

## Devin Features Exercised

- Schema comprehension and entity generation
- Data transformation logic
- Service layer refactoring
- Test generation for migration validation
- PR creation with migration documentation

## Difficulty

Intermediate

## Estimated Time

60 minutes

## Notes

- The app uses H2 in-memory database — both legacy and modern schemas can coexist in the same instance
- The column mapping doc in `data/mappings/column_mappings.md` provides the complete field-level reference
- Bonus: implement a feature flag for dual-read mode (switch between data sources at runtime)

---

## <a id="uc-data-source-migration-jdbc-normalization"></a>uc-data-source-migration-jdbc-normalization

**Repository:** [uc-data-source-migration-jdbc-normalization](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-jdbc-normalization)

Spring Boot 3.2 / Java 17 loan management application reading from legacy CDW-style tables. Includes modern target schema DDL, column mappings, and 5 workshop migration tasks.

### Step 1: Paste into Devin

> Review the legacy CDW schema in uc-data-source-migration-jdbc-normalization. Create modern JPA entities matching the target schema in data/modern-schema/modern_tables.sql. Write a migration service that reads from legacy tables, transforms the data (parse dates, amounts, expand codes per data/mappings/column_mappings.md), and inserts into modern tables. Then update LoanService.java to read from modern repositories instead of legacy ones. Verify all API endpoints return the same data. Open a PR.

### Step 2: Research with Ask Devin

- *"What are the data type mismatches between the legacy and modern schemas? Which transformations are most error-prone?"*
- *"How should we handle the code abbreviations (ACT, CLO, DFT, FRB) — enum mapping, lookup table, or inline conversion?"*
- Use the analysis to plan a more robust migration with error handling and validation

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the loan service domain model and API contracts. Map out which endpoints read from which legacy tables to ensure complete coverage.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the data type conversions correct? Does the migration handle edge cases (null values, malformed dates)?
- **Leave a comment** asking Devin to add a dual-read feature flag that can switch between legacy and modern data sources at runtime
- **Watch Devin respond** and push a follow-up commit
