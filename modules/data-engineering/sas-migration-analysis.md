# SAS Migration Analysis — Discovery & Assessment

## Table of Contents

- [Quick Start — Jump Straight In](#quick-start)
- [Challenge](#challenge)
- [Repositories](#repositories)
- [What Participants Will Learn](#what-participants-will-learn)
- [Difficulty & Estimated Time](#difficulty--estimated-time)
- [Hands-On Labs](#hands-on-labs)
  - [Lab 1: Estate Discovery (ts-sas-legacy-analytics)](#lab-1-estate-discovery)
  - [Lab 2: dbt Target Mapping (both repos)](#lab-2-dbt-target-mapping)
  - [Lab 3: Validate & Extend dbt Models (uc-data-migration-sas-to-databricks)](#lab-3-validate--extend-dbt-models)
- [Key Takeaways](#key-takeaways)
- [How Devin Builds Understanding — The Context Loop](#how-devin-builds-understanding)
- [SAS Artifact Reference](#sas-artifact-reference)
- [Customer Artifact Collection — What to Request](#customer-artifact-collection)
- [Going Further — Automation & Scale](#going-further)

---

<a id="quick-start"></a>
## Quick Start — Jump Straight In

Already familiar with Devin? Skip the background and start the hands-on work immediately. Copy the prompt below into a Devin session and go.

```
Analyze the SAS codebase in ts-sas-legacy-analytics to produce a comprehensive
migration assessment. Start with Config/autoexec.sas to understand the library
assignments and database connections. Then analyze all programs in
Programs/Banking/, Programs/Insurance/, and Programs/Reports/ — for each,
document: (1) what data sources it reads from (LIBNAME.dataset), (2) what
outputs it produces, (3) which macros from Macro/ it depends on, (4) which SAS
constructs it uses (DATA step, PROC SQL, PROC MEANS, hash objects, RETAIN,
etc.), and (5) a complexity rating. Also analyze the batch orchestrators in
BatchJobs/ to understand the execution dependency chain. Examine Formats/ for
custom format definitions that will need migration to dbt macros or seed tables.
Use the log files in Logs/ to understand production data volumes and execution
times. Produce a SAS_MIGRATION_ASSESSMENT.md with: artifact inventory, data
lineage diagram, macro dependency graph, dataset usage matrix, complexity
scores, risk areas, and a recommended migration sequence.
```

Then continue to [Lab 2](#lab-2-dbt-target-mapping) and [Lab 3](#lab-3-validate--extend-dbt-models) when ready.

---

## Challenge

Perform a comprehensive discovery and assessment of a legacy SAS estate to produce the migration-readiness artifacts needed before any code translation begins. Devin parses SAS source files (.sas, .egp, .spk), extracts data lineage from static code analysis, builds a dependency graph of macros, programs, and datasets, and generates an actionable migration assessment report — all without requiring modifications to the customer's running SAS environment.

The target migration path is **SAS → dbt on Databricks**, using the dbt project structure in `uc-data-migration-sas-to-databricks/dbt_project/` as the reference target architecture.

## Repositories

- [ts-sas-legacy-analytics](https://github.com/Cognition-Partner-Workshops/ts-sas-legacy-analytics) — Legacy SAS analytics environment with 7 business programs (4 Banking, 2 Insurance, 1 Reports), 92 utility macros, Enterprise Guide projects, deployment packages, custom format definitions, batch orchestrators, and sample production logs
- [uc-data-migration-sas-to-databricks](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-databricks) — dbt project with staging/intermediate/marts layers, Jinja macros replacing PROC FORMAT definitions, SAS→dbt construct mapping, and Databricks connection profiles

## What Participants Will Learn

- How Devin performs static analysis of SAS source code without runtime instrumentation — a non-invasive approach that requires no changes to the customer's SAS environment
- How Devin maps SAS constructs (DATA steps, PROC SQL, %MACRO, PROC FORMAT, RETAIN/BY-group, hash objects) to their dbt/Databricks equivalents
- How to use the assessment output to scope a migration project — effort estimation, risk identification, and sequencing
- How Devin uses the shared context layer (Knowledge notes, DeepWiki, MCP integrations) to iteratively deepen its understanding across multiple sessions
- How to treat Devin as a team resource — running parallel sessions, scheduling recurring analysis, and using webhook-driven automation for ongoing migration work

## Devin Features Exercised

- Multi-file static code analysis across SAS programs, macros, and configuration files
- Cross-language construct mapping (SAS → SQL/dbt/Python)
- Dependency graph extraction from %INCLUDE chains, macro calls, and LIBNAME references
- Documentation generation (structured assessment report, migration mapping)
- DeepWiki for rapid codebase orientation before reading source files
- Knowledge notes for encoding migration conventions that persist across sessions
- Child sessions for parallelizing migration analysis across independent program groups

## Difficulty & Estimated Time

Intermediate to Advanced — 75 minutes

## Target Outcomes

- Complete inventory of all SAS artifacts (programs, macros, datasets, libraries)
- Data lineage diagram showing input → program → output flows
- Macro dependency graph (which programs call which macros)
- Dataset usage matrix (which datasets are read/written by which programs)
- Complexity scores per program (LOC, construct count, external dependencies)
- `SAS_MIGRATION_ASSESSMENT.md` documenting estate scope, complexity metrics, risk areas, and recommended migration sequence
- PR with all assessment artifacts

---

## Hands-On Labs

<a id="lab-1-estate-discovery"></a>
### Lab 1: Estate Discovery

**Repository:** [ts-sas-legacy-analytics](https://github.com/Cognition-Partner-Workshops/ts-sas-legacy-analytics)

The SAS estate includes 7 business programs across banking, insurance, and reporting domains. Batch orchestrators in `BatchJobs/` coordinate execution with error handling. `Config/autoexec.sas` defines library assignments and database connections. The `Macro/` directory contains 92 utility macros used across programs.

#### Step 1: Paste into Devin

```
Analyze the SAS codebase in ts-sas-legacy-analytics to produce a comprehensive
migration assessment. Start with Config/autoexec.sas to understand the library
assignments and database connections. Then analyze all programs in
Programs/Banking/, Programs/Insurance/, and Programs/Reports/ — for each,
document: (1) what data sources it reads from (LIBNAME.dataset), (2) what
outputs it produces, (3) which macros from Macro/ it depends on, (4) which SAS
constructs it uses (DATA step, PROC SQL, PROC MEANS, hash objects, RETAIN,
etc.), and (5) a complexity rating. Also analyze the batch orchestrators in
BatchJobs/ to understand the execution dependency chain. Examine Formats/ for
custom format definitions that will need migration to dbt macros or seed tables.
Use the log files in Logs/ to understand production data volumes and execution
times. Produce a SAS_MIGRATION_ASSESSMENT.md with: artifact inventory, data
lineage diagram, macro dependency graph, dataset usage matrix, complexity
scores, risk areas, and a recommended migration sequence.
```

#### Step 2: Research with Ask Devin

While Devin works on the assessment, explore the codebase with Ask Devin:

- *"What external database connections does the SAS estate use (from autoexec.sas), and what's the equivalent Unity Catalog configuration for Databricks?"*
- *"Which SAS programs have the highest migration risk — the ones using hash objects, complex macro nesting, or RETAIN with BY-group processing?"*
- *"How does the batch orchestration in run_daily_banking.sas translate to a Databricks Workflow? What about the error handling and restart logic?"*

#### Step 3 (Optional): Review & Give Feedback

- Review the diff — does the assessment capture all library references from `autoexec.sas`? Are cross-program dataset dependencies correctly traced?
- Leave a comment asking Devin to add a "Migration Effort Estimate" section with hours per program based on complexity scores
- Watch Devin respond and push a follow-up commit

#### Key Takeaways

- **Non-invasive analysis**: Devin maps an entire SAS estate from source code alone — no XML logging configs, no audit loggers, no `-logconfigloc` changes required
- **Construct-level mapping**: SAS patterns (DATA step, PROC SQL, PROC FORMAT, hash objects, RETAIN, %INCLUDE) each have documented dbt/Databricks equivalents
- **Migration sequencing**: The assessment identifies which programs to migrate first based on complexity and dependency order
- **Effort estimation**: Complexity scores drive realistic migration timelines

---

<a id="lab-2-dbt-target-mapping"></a>
### Lab 2: dbt Target Mapping

**Repositories:** Both [ts-sas-legacy-analytics](https://github.com/Cognition-Partner-Workshops/ts-sas-legacy-analytics) and [uc-data-migration-sas-to-databricks](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-databricks)

#### Step 1: Paste into Devin

```
Using the assessment from the previous session and the reference dbt project in
uc-data-migration-sas-to-databricks/dbt_project/, create a detailed migration
plan mapping each SAS program to its dbt/Databricks equivalent. For each
program, specify: (1) which dbt model layer it maps to
(staging/intermediate/marts), (2) which SAS constructs need translation (use
docs/SAS_TO_DBT_MIGRATION_MAP.md as the reference), (3) estimated effort and
risk. Pay special attention to: PROC FORMAT → dbt macros (reference macros/ in
the dbt project), RETAIN/BY-group → window functions, hash objects → broadcast
joins, %INCLUDE chains → dbt ref() DAG, and Control-M scheduling → Databricks
Workflows. Add the migration plan to the PR.
```

#### Step 2: Research with Ask Devin

- *"What SAS constructs in the estate have no direct dbt equivalent? What's the recommended workaround for each?"*
- *"How should the batch orchestration dependency chain map to a dbt DAG? Show the ref() relationships."*

#### Key Takeaways

- **Source-to-target traceability**: Each SAS program maps to one or more dbt models in the target project
- **PROC FORMAT → dbt macros**: Custom SAS formats translate to reusable Jinja macros with CASE expressions
- **Lineage preservation**: The construct mapping document bridges the gap between SAS-side and Databricks-side data flows

---

<a id="lab-3-validate--extend-dbt-models"></a>
### Lab 3: Validate & Extend dbt Models

**Repository:** [uc-data-migration-sas-to-databricks](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-databricks)

The dbt project has models for customer accounts, transactions, and risk scoring. Several SAS programs do not yet have corresponding dbt models.

#### Step 1: Paste into Devin

```
Review the dbt project in uc-data-migration-sas-to-databricks/dbt_project/ —
verify that each dbt model correctly implements the SAS logic from
ts-sas-legacy-analytics. Cross-reference the migration patterns in
docs/SAS_TO_DBT_MIGRATION_MAP.md. For any gaps (SAS programs without
corresponding dbt models), create stub dbt models following the established
pattern.
```

#### Step 2: Paste into Devin — Extend Missing Models

```
The dbt_project/ in uc-data-migration-sas-to-databricks has models for customer
accounts, transactions, and risk scoring. Add the missing dbt models for:
(1) monthly_regulatory_reporting.sas → mart_regulatory_rwa.sql +
    mart_delinquency_aging.sql,
(2) claims_processing.sas → stg_claims.sql + int_claims_adjudication.sql,
(3) policy_valuation.sas → int_policy_valuation.sql + mart_loss_ratios.sql,
(4) customer_profitability.sas → mart_customer_pnl.sql.
Follow the patterns in the existing models and reference the
SAS_TO_DBT_MIGRATION_MAP.md for construct translations. Add dbt tests for key
business rules.
```

#### Step 3 (Optional): Read the DeepWiki

Open the DeepWiki pages for both repos to understand the full migration pipeline from SAS source through assessment to dbt target. DeepWiki's auto-generated docs can accelerate orientation — though coverage depends on the repo's structure and may not capture every nuance of the codebase.

#### Key Takeaways

- **Incremental migration**: dbt models can be developed and tested independently, enabling parallel workstreams
- **Pattern consistency**: Existing models establish conventions that Devin follows when generating new ones
- **Business rule testing**: dbt tests validate that translated logic preserves the original business rules

---

<a id="key-takeaways"></a>
## Key Takeaways

- **Non-invasive assessment** — Devin analyzes SAS estates from source code alone, with no changes to the customer's running SAS environment. This eliminates the most common blocker in migration discovery: getting production access for instrumentation.
- **Construct-level mapping** — Each SAS pattern has a documented dbt/Databricks equivalent. The mapping is deterministic for most constructs, though edge cases (complex macro nesting, RETAIN with multiple BY-group interactions) may require human review of the proposed translation.
- **Context compounds over time** — Knowledge notes, DeepWiki docs, and MCP integrations accumulate across sessions. The first session builds the baseline; subsequent sessions start with the full accumulated understanding. This is the shared context layer in action.
- **Team resource, not individual tool** — Multiple team members can interact with the same Devin session through PR comments. Run parallel sessions for different SAS program groups. Schedule recurring analysis sessions to track migration progress. Set up webhook-driven automation so new SAS code committed to the source repo triggers a re-assessment automatically.
- **Devin works autonomously** — Once you paste a prompt and start a session, Devin runs independently. Move on to the next lab, explore with Ask Devin, or review a previous PR while Devin works. You get notified when it opens a PR.

---

<a id="how-devin-builds-understanding"></a>
## How Devin Builds Understanding — The Context Loop

Migration analysis is not a single-pass operation. Devin uses programmatically accessed resources to build context iteratively — each tool's output feeds into the next step.

```
┌──────────────────────────────────────────────────────────────┐
│                                                              │
│  ① Retrieve architectural context (DeepWiki, Knowledge)      │
│     ↓                                                        │
│  ② Analyze source code (static analysis of .sas files)       │
│     ↓                                                        │
│  ③ Cross-reference target architecture (dbt project)         │
│     ↓                                                        │
│  ④ Query external systems for context (MCP: Jira, Confluence)│
│     ↓                                                        │
│  ⑤ Produce assessment artifacts (PR with migration docs)     │
│     ↓                                                        │
│  ⑥ Human review → PR comments → Devin resumes and refines   │
│     ↓                                                        │
│  ⑦ Loop back to ② with enriched context ──────────→ ②       │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

### Resources Devin Accesses at Each Stage

| Stage | Resource | What Devin Retrieves | How It Uses It |
|-------|----------|---------------------|----------------|
| **① Context** | **DeepWiki** + **Knowledge notes** | Auto-generated architecture docs; org-level conventions | Understands SAS estate structure and dbt target patterns before reading source files. Coverage depends on repo structure — complex or unconventional layouts may require manual orientation |
| **② Analyze** | **Git repo** (clone + shell) | `.sas`, `.egp`, `autoexec.sas`, macro library, format definitions, log files | Parses SAS syntax, extracts LIBNAME refs, traces %INCLUDE chains, counts constructs |
| **③ Cross-ref** | **Git repo** (second repo) | `dbt_project/models/`, `macros/`, `docs/SAS_TO_DBT_MIGRATION_MAP.md` | Maps each SAS program to its dbt equivalent; identifies gaps |
| **④ External** | **MCP servers** (Jira, Confluence, ADO) | Migration tickets, data dictionaries, existing analysis docs | Enriches the assessment with business context — e.g., which programs are business-critical vs. candidates for retirement |
| **⑤ Produce** | **Git** (push + PR) | Opens PR with assessment artifacts | Creates the review artifact that humans inspect |
| **⑥ Review** | **PR comments** (Devin monitors) | Human feedback | Devin resumes from hibernation, reads comments, addresses each one |
| **⑦ Refine** | **All of the above** | Re-reads source code with new understanding | Each cycle adds accuracy |

---

<a id="sas-artifact-reference"></a>
## SAS Artifact Reference

| Artifact | Extension | Purpose | Analysis Value |
|----------|-----------|---------|----------------|
| SAS Program | `.sas` | DATA steps, PROC operations, macro definitions | Core analysis target — data transformations, business logic |
| Enterprise Guide Project | `.egp` | Visual process flows, execution order, parameter prompts | Execution dependency chain, project organization |
| Deployment Package | `.spk` | Promotion-ready bundles for SAS Management Console | Deployment topology, environment dependencies |
| SAS Log | `.log` | Execution output, row counts, timing, warnings/errors | Data volumes, performance bottlenecks, error patterns |
| Autoexec | `autoexec.sas` | Library assignments, macro variables, system options | Environment configuration, data source mapping |
| Format Catalog | `.sas7bcat` | Custom format definitions (PROC FORMAT output) | Value decoding rules (e.g., code-to-description) |
| PROC FORMAT Source | `.sas` | Human-readable format definitions | Migration to dbt macros or seed tables |

---

<a id="customer-artifact-collection"></a>
## Customer Artifact Collection — What to Request

When working with a real customer SAS estate (outside this workshop), the following artifacts are needed. Devin's approach uses **static code analysis** — no changes to the customer's SAS environment configuration are required.

### Email Template

```
To perform our SAS migration analysis, we need the following artifacts from
your SAS environment. These can be provided via a file share or secure
upload — no changes to your running SAS environment are required.

Required:
1. All .sas source files — programs, macros, and include files
2. All .egp Enterprise Guide project files
3. autoexec.sas and any project-level autoexec files
4. Macro autocall library contents (all files from your autocall paths)
5. LIBNAME configuration — a document or extract showing how library names
   map to physical paths, databases, or schemas

Recommended (accelerates analysis):
6. .spk deployment packages
7. Existing SAS log files (as-is — no new logging configuration needed)
8. Format catalogs or PROC FORMAT source code
9. Scheduling metadata (job schedules, dependencies, execution windows)
10. PROC CONTENTS output for key datasets (or data dictionary exports)
11. Database connection details for any external sources accessed via
    SAS/ACCESS engines

Not needed: No XML logging configuration changes, no -logconfigloc updates,
no audit logger enablement. Our analysis is entirely static — we read your
source code, not runtime traces.
```

### Comparison: Static Analysis vs. Runtime Trace Tools

Unlike runtime trace-based tools, Devin's static code analysis approach requires no changes to the customer's SAS environment. Third-party trace tools typically require XML logging configuration changes, audit logger enablement, and `-logconfigloc` updates on the production SAS environment. Devin's approach requires only the source files and configuration artifacts listed above — the analysis is entirely non-invasive.

This difference matters for presales: customers are often reluctant to modify production SAS environments for a migration assessment. Removing that requirement significantly reduces the barrier to starting.

---

<a id="going-further"></a>
## Going Further — Automation & Scale

These patterns extend the workshop exercise into real-world migration workflows:

### Divide and Conquer with Child Sessions

For large SAS estates with dozens or hundreds of programs, a parent session can break the analysis into independent units and spawn a child session for each program group:

```
Parent Session
├── Analyzes estate scope (list of SAS program groups)
├── Creates a playbook encoding the assessment methodology
├── Spawns Child Session 1 → Banking programs → PR
├── Spawns Child Session 2 → Insurance programs → PR
├── Spawns Child Session 3 → Reports programs → PR
└── Monitors progress, consolidates results
```

Each child inherits the org-level Knowledge notes and migration conventions — the shared context layer means every session starts with the same baseline understanding.

### Webhook-Driven Re-Assessment

Connect Devin to your CI pipeline so new SAS code committed to the source repo triggers a re-assessment automatically:

```
SAS source repo push → webhook → Devin API → Re-assessment session → Updated PR
```

This keeps the migration assessment current as the legacy codebase evolves during the migration period.

### Scheduled Migration Progress Tracking

Schedule a recurring Devin session (weekly or sprint-aligned) that:
- Re-runs the estate assessment against the latest source code
- Compares the current dbt model coverage against the SAS program inventory
- Reports on migration progress (programs completed, in-flight, remaining)
- Opens a PR with an updated migration status dashboard

### Team Collaboration Patterns

- Multiple engineers review the same assessment PR — Devin reads and responds to comments from any reviewer
- Create Knowledge notes encoding migration decisions (e.g., "PROC FORMAT → Jinja macro, not seed CSV") so every future session applies the same standards
- Use playbooks to encode the assessment methodology so new team members can run the same analysis process

---

## Notes

- The `ts-sas-legacy-analytics` repo has no SAS runtime — Devin cannot execute SAS code. The assessment is entirely based on static code analysis of the source files
- The `uc-data-migration-sas-to-databricks` dbt project can be validated with `dbt parse` and `dbt compile` if dbt-core and dbt-databricks are installed in the environment blueprint
- Different participants may produce different assessment structures — there is no single "right answer" for how to organize the migration report
- The Enterprise Guide project files (`.egp`) are ZIP archives containing XML — Devin can extract and parse them to understand execution flows
