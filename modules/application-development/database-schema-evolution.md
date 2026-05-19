# Database Schema Evolution

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-data-source-migration-jdbc-normalization](#uc-data-source-migration-jdbc-normalization)
- [uc-spring-boot-upgrade-microservice-extraction](#uc-spring-boot-upgrade-microservice-extraction)

---

## Challenge

Plan and execute a database schema evolution — adding new tables, modifying columns, creating indexes, or restructuring relationships — using proper migration tooling (Flyway, Liquibase, Knex migrations, etc.) to ensure changes are versioned, reversible, and safe.

## Target Outcomes

- Database migration script(s) created using the project's migration framework
- Schema changes applied successfully with data preserved
- Rollback script or down migration verified
- Application code updated to work with the new schema
- PR with migration files and updated code

## What Participants Will Learn

- How Devin works with database migration frameworks (Flyway, Liquibase, Knex)
- How Devin plans schema changes that preserve existing data
- The importance of reversible migrations and rollback strategies
- How to validate schema changes against running applications

## Devin Features Exercised

- Database schema design
- Migration framework usage
- Application code refactoring for schema changes
- PR creation with migration documentation

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

Node.js/Express with SQLite — add or modify database migrations to support new features or schema improvements.

### Step 1: Paste into Devin

> Add a database migration system to timesheet-app using Knex.js (or the existing migration approach). Create a migration that adds a "projects" table with columns: id, name, description, client_id (FK to clients), start_date, end_date, status, created_at, updated_at. Add an index on client_id. Create a second migration that adds a project_id column to the work_entries table. Ensure both migrations have rollback (down) functions. Open a PR.

### Step 2: Research with Ask Devin

- *"What migration framework does timesheet-app currently use? If none, what's the best option for SQLite + Express?"*
- *"How should we handle the foreign key from work_entries to projects — nullable (gradual migration) or required (breaking change)?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the current database schema and ORM usage. Plan the migration to be compatible with the existing data access patterns.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — do the migrations handle existing data correctly? Are rollbacks safe?
- **Leave a comment** asking Devin to add a seed script that populates sample project data

---

## <a id="uc-data-source-migration-jdbc-normalization"></a>uc-data-source-migration-jdbc-normalization

**Repository:** [uc-data-source-migration-jdbc-normalization](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-jdbc-normalization)

Spring Boot with JPA and H2 — add Flyway migrations for schema evolution.

### Step 1: Paste into Devin

> Add Flyway database migration support to uc-data-source-migration-jdbc-normalization. Create versioned migration scripts that: (1) create the modern schema tables from data/modern-schema/modern_tables.sql, (2) add audit columns (created_at, updated_at, created_by) to all modern tables, (3) add a loan_payment_history table for payment tracking. Configure Flyway in application.properties. Verify migrations run on startup. Open a PR.

### Step 2: Research with Ask Devin

- *"Does this project already use any database migration tool? If not, is Flyway or Liquibase a better fit for Spring Boot 3.x?"*
- *"How should the migration handle the coexistence of legacy and modern schemas in H2?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the current schema setup and JPA entity definitions. Plan migrations that align with the existing entity model.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are migration version numbers correct? Will they run in the right order?
- **Leave a comment** asking Devin to add a migration that creates database indexes on frequently queried columns

---

## <a id="uc-spring-boot-upgrade-microservice-extraction"></a>uc-spring-boot-upgrade-microservice-extraction

**Repository:** [uc-spring-boot-upgrade-microservice-extraction](https://github.com/Cognition-Partner-Workshops/uc-spring-boot-upgrade-microservice-extraction)

Spring Boot with Flyway and MyBatis — add new migrations for schema changes.

### Step 1: Paste into Devin

> Review the existing Flyway migrations in uc-spring-boot-upgrade-microservice-extraction. Add new migrations that: (1) add a "bookmarks" join table between users and articles, (2) add a "tags" count column to articles for denormalized tag counting, (3) add database indexes on commonly queried columns (article slug, user email). Ensure all existing tests still pass. Open a PR.

### Step 2: Research with Ask Devin

- *"What Flyway migrations already exist? What schema version is the database currently at?"*
- *"What columns would benefit most from database indexes based on the MyBatis query patterns?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the existing schema and query patterns. Identify which tables and columns would benefit from indexing.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — are the migrations idempotent? Do they handle the case where the table already exists?
- **Leave a comment** asking Devin to add a migration validation test that verifies schema state after all migrations run
