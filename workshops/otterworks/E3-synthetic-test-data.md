# Lab: Synthetic Test Data Generation

## Objective

Use the OtterWorks synthetic test data framework to generate, load, and validate realistic test data for a lower environment — driven by acceptance criteria defined in a GitHub Issue.

## What's Wrong

Lower environments (dev, QA, staging, load-test) need realistic data to be useful for testing. Empty databases make it impossible to test search, analytics, pagination, sharing workflows, or performance. OtterWorks has a test data generation framework in `testdata/` that generates data into namespaced PostgreSQL schemas, but participants need to understand how to use it and extend it.

The framework works like this:

1. A GitHub Issue describes a user story with acceptance criteria (row counts, distributions, relationships, activity patterns).
2. Devin reads the issue, introspects the OtterWorks schema, and generates Python scripts in `testdata/generated/<namespace>/`.
3. The scripts create a namespaced PostgreSQL schema (`otterworks_<ns>`), generate data, and load it.
4. The validation harness (`make testdata-validate NS=<ns>`) programmatically proves the data satisfies the schema invariants and the issue's criteria.

## Where to Look

- `testdata/README.md` — **Read this first.** Framework documentation with quick start, directory structure, and validation instructions.
- `testdata/harness/validate.py` — Validation harness that checks schema invariants and custom criteria.
- `testdata/harness/create_schema.sql` — SQL template for creating namespaced schemas.
- `testdata/generated/` — Directory where generated scripts live, organized by namespace.
- `testdata/generated/seed/` — The default seed data namespace (reference implementation).
- `scripts/seed.py` — The admin-service seed script (separate from testdata, but shows the data model).
- `scripts/init-db.sql` — Database initialization (creates base schemas and tables).
- `Makefile` — Testdata targets: `testdata-setup-schema`, `testdata-validate`, `testdata-clean`.

## What Done Looks Like

- [ ] Understood the testdata framework by reading `testdata/README.md` and examining the existing seed data
- [ ] Wrote acceptance criteria for a test scenario (e.g., "100 users, 500 documents, 1000 files, realistic sharing patterns, 30 days of activity data")
- [ ] Asked Devin to generate test data scripts for the scenario into a new namespace
- [ ] Created the namespaced schema: `make testdata-setup-schema NS=<your-namespace>`
- [ ] Ran the generated scripts to load data into the namespace
- [ ] Validated the data: `make testdata-validate NS=<your-namespace>`
- [ ] Validation passes — all schema invariants and acceptance criteria are met

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started

Read `testdata/README.md` for the framework overview. Then examine `testdata/generated/seed/` to see what a working data generation namespace looks like. Run `make testdata-setup-schema NS=workshop-test` to create a new namespace, and `make testdata-validate NS=seed` to see how validation works on the existing seed data.

### Hint 2 — Specific Direction

Write acceptance criteria like: "Generate data for a QA environment with 50 users (mix of admin and regular roles), 200 documents with version history (2-5 versions each), 300 files across 20 folders, realistic sharing (each user shares 3-5 items), and 14 days of audit trail entries."

### Hint 3 — Approach

Ask Devin: "Read `testdata/README.md` and `scripts/init-db.sql` to understand the OtterWorks data model. Then generate test data scripts in `testdata/generated/qa-workshop/` that create: 50 users, 200 documents with version history, 300 files in 20 folders, sharing relationships, and 14 days of audit events. The data must pass `make testdata-validate NS=qa-workshop`."

## Time Budget

- ~10 minutes: Read the framework docs and examine existing seed data
- ~10 minutes: Write acceptance criteria for a test scenario
- ~20-30 minutes: Generate, load, and validate the test data
- ~45-60 minutes total including iteration if validation fails on the first pass
