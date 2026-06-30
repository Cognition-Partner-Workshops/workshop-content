# Ab Initio Migration Analysis — Discovery & Assessment

## Table of Contents

- [Quick Start — Jump Straight In](#quick-start)
- [Challenge](#challenge)
- [Repositories](#repositories)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty & Estimated Time](#difficulty--estimated-time)
- [Target Outcomes](#target-outcomes)
- [Hands-On Labs](#hands-on-labs)
  - [Lab 1: Estate Discovery (ts-python-abinitio-etl)](#lab-1-estate-discovery)
  - [Lab 2: Target Mapping (both repos)](#lab-2-target-mapping)
  - [Lab 3: Convert a Graph with Verification (target repo)](#lab-3-convert-with-verification)
  - [Lab 4: Divide and Conquer with Child Sessions (Advanced)](#lab-4-divide-and-conquer)
- [Key Takeaways](#key-takeaways)
- [How Devin Builds Understanding — The Context Loop](#how-devin-builds-understanding)
- [Ab Initio Artifact Reference](#abinitio-artifact-reference)
- [Customer Artifact Collection — What to Request](#customer-artifact-collection)
- [Going Further — Automation & Scale](#going-further)

---

<a id="quick-start"></a>
## Quick Start — Jump Straight In

Already familiar with Devin? Skip the background and start the hands-on work
immediately. Copy the prompt below into a Devin session and go.

```
Analyze the Ab Initio codebase in ts-python-abinitio-etl to produce a
comprehensive migration assessment. Start with scripts/setenv.ksh and the
psets/ parameter sets to understand the environment and run parameters. Then
analyze the graphs in graphs/ and the KornShell wrappers in scripts/ — for each
pipeline, document: (1) what it reads and writes, (2) which DML record formats
from dml/ it binds, (3) which PSET parameters it depends on, (4) which Ab Initio
constructs it uses (reformat, join, rollup, partition-by-key, the CDC
compare-by-key component, nested/variable-length records), and (5) a complexity
rating. Map the DML record formats in dml/ to target schemas, calling out
delimiters, packed decimals, nested records, variable-length arrays, and
null(...) defaults. Analyze scripts/run_daily_orders.ksh and
scripts/run_customer_cdc.ksh for the execution dependency chain. Produce an
ABINITIO_MIGRATION_ASSESSMENT.md with: artifact inventory, data lineage diagram,
DML→schema mapping, PSET/parameter matrix, complexity scores, risk areas, and a
recommended migration sequence.
```

Then continue to [Lab 2](#lab-2-target-mapping) and
[Lab 3](#lab-3-convert-with-verification) when ready.

---

<a id="challenge"></a>
## Challenge

Perform a comprehensive discovery and assessment of a legacy Ab Initio ETL estate
to produce the migration-readiness artifacts needed before any code translation
begins. Devin parses Ab Initio artifacts (DML record formats, graphs, PSET
parameter sets, KornShell wrappers), extracts data lineage from static analysis,
builds a dependency graph of graphs, parameters, and datasets, and generates an
actionable migration assessment — all without requiring access to the customer's
running Co>Operating System environment.

The target migration path is **Ab Initio → PySpark** (platform-agnostic, runs
locally) or **Ab Initio → dbt on Databricks** (Lakehouse), using the project
structure in the corresponding `uc-data-migration-abinitio-to-*` repo as the
reference target architecture.

<a id="repositories"></a>
## Repositories

- [ts-python-abinitio-etl](https://github.com/Cognition-Partner-Workshops/ts-python-abinitio-etl) — Legacy Ab Initio ETL estate: 2 graphs (CDC processor, parallel loader), 8 DML record formats (including nested/variable-length and packed-decimal layouts), 3 PSET parameter sets, KornShell/AutoSys batch wrappers, a DML parser utility, and deployment/monitoring helpers
- [uc-data-migration-abinitio-to-pyspark](https://github.com/Cognition-Partner-Workshops/uc-data-migration-abinitio-to-pyspark) — PySpark target: converted jobs, DML→StructType mapping, a local source→target reconciliation harness, deterministic seeder, the `!convert-abinitio-to-pyspark` playbook source, and the repo Skill
- [uc-data-migration-abinitio-to-databricks](https://github.com/Cognition-Partner-Workshops/uc-data-migration-abinitio-to-databricks) — dbt/Databricks target: staging/intermediate/marts models, DML→Delta mapping, reconciliation harness (dbt tests + report), Asset Bundle Workflow, the `!convert-abinitio-to-databricks` playbook source, and the repo Skill

<a id="what-participants-will-learn"></a>
## What Participants Will Learn

- How Devin performs static analysis of Ab Initio artifacts without runtime instrumentation — a non-invasive approach that requires no changes to the customer's Co>Operating System environment
- How Devin maps Ab Initio constructs (DML record formats, reformat/join/rollup, partition-by-key, the CDC compare-by-key component, PSET parameters, KornShell/AutoSys orchestration) to their PySpark and dbt/Databricks equivalents
- How to use the assessment output to scope a migration project — effort estimation, risk identification, and sequencing
- How a **programmatic reconciliation loop** turns "looks reasonable" review into source-parity proof — and catches divergences a human reviewer would miss
- How Devin uses the shared context layer (Knowledge notes, DeepWiki, MCP integrations, playbooks) to apply a consistent conversion procedure across many graphs in parallel

<a id="devin-features-exercised"></a>
## Devin Features Exercised

- Multi-file static code analysis across Ab Initio graphs, DML, PSETs, and wrappers
- Cross-language construct mapping (Ab Initio → PySpark / SQL / dbt)
- Dependency graph extraction from graph→DML bindings, PSET references, and KornShell phase chains
- Documentation generation (structured assessment report, DML→schema mapping)
- Playbooks: the `!convert-abinitio-to-pyspark` / `!convert-abinitio-to-databricks` conversion procedures
- DeepWiki for rapid codebase orientation before reading source files
- Knowledge notes for encoding migration conventions that persist across sessions
- Child sessions for parallelizing conversion across independent graphs

<a id="difficulty--estimated-time"></a>
## Difficulty & Estimated Time

Intermediate to Advanced — 75 minutes

<a id="target-outcomes"></a>
## Target Outcomes

- Complete inventory of all Ab Initio artifacts (graphs, DML, PSETs, wrappers, datasets)
- Data lineage diagram showing input → graph → output flows
- DML→schema mapping (record formats → StructType / Delta types), including delimiters, packed decimals, nested/variable-length records, and `null(...)` defaults
- PSET/parameter matrix (which parameters drive which graphs)
- Complexity scores per graph (constructs, nesting, external dependencies)
- `ABINITIO_MIGRATION_ASSESSMENT.md` documenting estate scope, complexity metrics, risk areas, and a recommended migration sequence
- PR with all assessment artifacts

---

<a id="hands-on-labs"></a>
## Hands-On Labs

<a id="lab-1-estate-discovery"></a>
### Lab 1: Estate Discovery

**Repository:** [ts-python-abinitio-etl](https://github.com/Cognition-Partner-Workshops/ts-python-abinitio-etl)

The Ab Initio estate includes 2 graphs (a CDC processor and a parallel loader), 8
DML record formats, 3 PSET parameter sets, and KornShell wrappers in `scripts/`
that orchestrate the daily orders and customer-CDC pipelines. `scripts/setenv.ksh`
defines the environment; `utils/dml_parser.py` shows how the DML layouts are read.

#### Step 1: Paste into Devin

```
Analyze the Ab Initio codebase in ts-python-abinitio-etl to produce a
comprehensive migration assessment. Start with scripts/setenv.ksh and the
psets/ parameter sets to understand the environment and run parameters. Then
analyze the graphs in graphs/ and the KornShell wrappers in scripts/ — for each
pipeline, document: (1) what it reads and writes, (2) which DML record formats
from dml/ it binds, (3) which PSET parameters it depends on, (4) which Ab Initio
constructs it uses (reformat, join, rollup, partition-by-key, CDC
compare-by-key, nested/variable-length records), and (5) a complexity rating.
Map the DML record formats in dml/ to target schemas, calling out delimiters,
packed decimals, nested records, variable-length arrays, and null(...) defaults.
Analyze scripts/run_daily_orders.ksh and scripts/run_customer_cdc.ksh for the
execution dependency chain. Produce an ABINITIO_MIGRATION_ASSESSMENT.md with:
artifact inventory, data lineage diagram, DML->schema mapping, PSET/parameter
matrix, complexity scores, risk areas, and a recommended migration sequence.
```

#### Step 2: Research with Ask Devin

While Devin works on the assessment, explore the codebase with Ask Devin:

- *"Which DML record formats are the riskiest to migrate — the ones with packed decimals, nested records, variable-length arrays, or null(...) defaults?"*
- *"How does the CDC compare-by-key logic in graphs/cdc_processor.py work, and what is the PySpark/Delta equivalent?"*
- *"How does the batch orchestration in scripts/run_daily_orders.ksh translate to a Databricks Workflow? What about the phase dependencies and restart logic?"*

#### Step 3 (Optional): Review & Give Feedback

- Review the diff — does the assessment capture every DML binding for each graph? Are the `null(...)` defaults (e.g. the transaction `channel` default) called out as parity risks?
- Leave a comment asking Devin to add a "Migration Effort Estimate" section with hours per graph based on complexity scores
- Watch Devin respond and push a follow-up commit

#### Key Takeaways

- **Non-invasive analysis**: Devin maps an entire Ab Initio estate from artifacts alone — no Co>Operating System access, no EME export, no runtime tracing required
- **Construct-level mapping**: Ab Initio patterns (DML record formats, reformat/join/rollup, partition-by-key, CDC compare-by-key, PSETs) each have documented PySpark/dbt equivalents
- **Parity risks surface early**: DML `null(...)` defaults, packed decimals, and nested records are flagged as the things a naive conversion gets wrong

---

<a id="lab-2-target-mapping"></a>
### Lab 2: Target Mapping

**Repositories:** [ts-python-abinitio-etl](https://github.com/Cognition-Partner-Workshops/ts-python-abinitio-etl) and one target (`uc-data-migration-abinitio-to-pyspark` or `uc-data-migration-abinitio-to-databricks`)

Pick the target that matches your audience — PySpark (platform-agnostic, runs
locally) or dbt/Databricks (Lakehouse). Both ship a migration-map doc and a
reconciliation harness as the reference target architecture.

#### Step 1: Paste into Devin

```
Using the assessment from the previous session and the reference project in
uc-data-migration-abinitio-to-pyspark/ (docs/ABINITIO_TO_PYSPARK_MIGRATION_MAP.md
and src/), create a detailed migration plan mapping each Ab Initio graph to its
PySpark equivalent. For each pipeline specify: (1) which target layer it maps to
(staging/intermediate/marts/curated), (2) which Ab Initio constructs need
translation (DML record -> StructType, reformat -> select, join, rollup ->
groupBy, partition-by-key -> repartition, CDC compare-by-key -> full_outer join +
row hash), (3) estimated effort and risk. Pay special attention to DML null(...)
defaults, packed decimals, nested records, and variable-length arrays. Add the
migration plan to the PR.
```

> For the Databricks target, point Devin at
> `uc-data-migration-abinitio-to-databricks/docs/ABINITIO_TO_DBT_MIGRATION_MAP.md`
> and `dbt_project/` instead, and map to dbt models + Delta `MERGE` for CDC.

#### Step 2: Research with Ask Devin

- *"Which Ab Initio constructs in the estate have no direct target equivalent? What's the recommended workaround for each?"*
- *"How should the KornShell phase chain map to the target's job DAG? Show the dependency edges."*

#### Key Takeaways

- **Source-to-target traceability**: Each Ab Initio graph maps to one or more target models/jobs
- **DML → schema**: Record formats translate to typed schemas; delimiters, packed decimals, and `null(...)` defaults each have an explicit rule
- **Lineage preservation**: The construct-mapping document bridges the Ab Initio side and the target side

---

<a id="lab-3-convert-with-verification"></a>
### Lab 3: Convert a Graph with Verification

**Repository:** a target repo (`uc-data-migration-abinitio-to-pyspark` or `uc-data-migration-abinitio-to-databricks`)

The target repo ships with the customer + orders graphs already converted and a
reconciliation harness in place. Several graphs do not yet have corresponding
models — this lab converts one **with source-parity verification**, the heart of
a trustworthy migration.

#### Step 1: Paste into Devin

```
!convert-abinitio-to-pyspark

Convert the Ab Initio transactions detail pipeline from the ts-python-abinitio-etl
estate into a PySpark job in uc-data-migration-abinitio-to-pyspark.

- DML: dml/transaction_detail.dml (nested merchant_info + line_items[] array)
- Source layout/orchestration: scripts/run_daily_orders.ksh
- Target: src/jobs/curated_transactions.py writing out/<NS>/curated/transactions
- Namespace: dev

Add a reconciliation control for the channel default and build until the
reconciliation report is green.
```

#### Step 2: Watch the verification loop

`dml/transaction_detail.dml` declares `string("\n", null("UNKNOWN")) channel` — a
blank channel is read by Ab Initio as the literal `UNKNOWN`, never NULL. A naive
conversion lands blanks as `NULL`; row counts and totals still tie out, so it
looks fine. The `transactions_channel_parity` control fails until the DML default
is reproduced:

```bash
make reconcile NS=dev
#   transactions_channel_parity | FAIL | 6 transaction(s) with NULL/blank channel (expected the DML default 'UNKNOWN')
# ...fix: reproduce the null("UNKNOWN") default, re-run...
make reconcile NS=dev
#   transactions_channel_parity | PASS
```

This is the lesson: "looks reasonable" review ships the bug; the source-parity
control catches it. Reproduce legacy behaviour faithfully — do not relax the
control to make it pass.

#### Step 3 (Optional): Read the DeepWiki

Open the DeepWiki pages for the source and target repos to understand the full
pipeline from Ab Initio source through assessment to converted target. DeepWiki's
auto-generated docs can accelerate orientation — though coverage depends on the
repo's structure and may not capture every nuance.

#### Key Takeaways

- **Verification is the deliverable**: a conversion is "done" when the reconciliation controls are green, not when the code runs
- **Source is the source of truth**: DML `null(...)` defaults and CDC rules are reproduced faithfully and flagged, never silently "fixed"
- **The playbook keeps it consistent**: every graph is converted the same way, with the same reconciliation contract

---

<a id="lab-4-divide-and-conquer"></a>
### Lab 4: Divide and Conquer with Child Sessions (Advanced)

**Repositories:** [ts-python-abinitio-etl](https://github.com/Cognition-Partner-Workshops/ts-python-abinitio-etl) and a target repo

In a real migration with dozens or hundreds of graphs, a single session
converting everything sequentially is slow. Instead, use child sessions to
parallelize — one session per graph, all inheriting the same conversion procedure
from the playbook and the shared context layer.

#### Step 1: Paste into Devin

```
You are the parent coordinator for an Ab Initio migration across
ts-python-abinitio-etl. Create child Devin sessions to divide and conquer the
conversion into uc-data-migration-abinitio-to-pyspark — one child per pipeline:

- Child 1: dml/transaction_detail.dml (+ scripts/run_daily_orders.ksh) ->
  src/jobs/curated_transactions.py, with a channel-default reconciliation control.
- Child 2: graphs/cdc_processor.py (+ psets/pset_templates/customer_cdc.pset) ->
  src/jobs/customer_cdc.py (compare-by-key + row hash), with a CDC parity control.
- Child 3: dml/order_items.dml -> src/jobs/int_order_items.py (explode the
  variable-length item arrays), with a completeness + control-total check.

Each child follows the !convert-abinitio-to-pyspark playbook, uses its own
namespace (NS=child1, child2, ...), treats the Ab Initio source as the source of
truth, and builds until its reconciliation report is green. Each child opens its
own PR.

Once all children complete, consolidate into a CONVERSION_SUMMARY.md listing each
pipeline, its target, the controls added, and any source-parity divergences the
children caught. Open a final PR with the consolidated view.
```

#### Step 2: Watch the Coordination

- Observe how the parent session creates child sessions and monitors their progress
- Each child works independently on its pipeline, in its own namespace
- The parent waits for all children to complete, then consolidates

#### Key Takeaways

- **Horizontal scale**: Child sessions run in parallel — a 100-graph estate takes the same wall-clock time as a 10-graph group
- **Shared context**: Each child inherits the playbook + org Knowledge notes, so the conversion procedure and reconciliation contract are applied consistently
- **Consolidation**: The parent stitches individual conversions into a unified view, surfacing the divergences each child caught
- **Team pattern**: This is how Devin operates as a team resource — a coordinated group of agents dividing work, not one agent doing everything

---

<a id="key-takeaways"></a>
## Key Takeaways

- **Non-invasive assessment** — Devin analyzes Ab Initio estates from artifacts alone, with no changes to the customer's running environment. This eliminates the most common blocker in migration discovery: getting production access for instrumentation.
- **Construct-level mapping** — Each Ab Initio pattern has a documented PySpark/dbt equivalent. The mapping is deterministic for most constructs, though edge cases (deeply nested records, conditional sub-records, complex CDC hashing) may require human review of the proposed translation.
- **Verification turns review into proof** — A programmatic reconciliation loop (completeness, control totals, per-class parity) catches divergences — like a DML `null(...)` default dropped in conversion — that "looks reasonable" review misses.
- **Context compounds over time** — Knowledge notes, DeepWiki docs, playbooks, and MCP integrations accumulate across sessions. The first session builds the baseline; subsequent sessions start with the full accumulated understanding.
- **Team resource, not individual tool** — Run parallel sessions for different graphs, schedule recurring analysis as the estate changes, and use PR comments to collaborate. Devin works autonomously once you paste a prompt — move on to the next lab while it runs.

---

<a id="how-devin-builds-understanding"></a>
## How Devin Builds Understanding — The Context Loop

Migration analysis is not a single-pass operation. Devin uses programmatically
accessed resources to build context iteratively — each tool's output feeds into
the next step.

```
┌──────────────────────────────────────────────────────────────┐
│                                                              │
│  ① Retrieve architectural context (DeepWiki, Knowledge)      │
│     ↓                                                        │
│  ② Analyze source artifacts (DML, graphs, PSETs, wrappers)   │
│     ↓                                                        │
│  ③ Cross-reference target architecture (PySpark / dbt repo)  │
│     ↓                                                        │
│  ④ Convert + verify (playbook + reconciliation harness)      │
│     ↓                                                        │
│  ⑤ Produce artifacts (PR with models, controls, report)      │
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
| **① Context** | **DeepWiki** + **Knowledge notes** | Auto-generated architecture docs; org conventions | Understands the Ab Initio estate and target patterns before reading source. Coverage depends on repo structure |
| **② Analyze** | **Git repo** (clone + shell) | `dml/*.dml`, `graphs/*.py`, `psets/*.pset`, `scripts/*.ksh` | Parses DML layouts, traces graph→DML bindings and KornShell phases, counts constructs |
| **③ Cross-ref** | **Git repo** (target) | migration-map doc, `src/` or `dbt_project/`, the reconciliation harness | Maps each graph to its target model/job; identifies gaps |
| **④ Convert** | **Playbook + Skill + harness** | `!convert-abinitio-to-*`, the repo's build + `make reconcile` targets | Converts faithfully and proves parity against the source |
| **⑤ Produce** | **Git** (push + PR) | Opens PR with models, controls, reconciliation report | Creates the review artifact humans inspect |
| **⑥ Review** | **PR comments** (Devin monitors) | Human feedback | Devin resumes from hibernation, addresses each comment |
| **⑦ Refine** | **All of the above** | Re-reads source with new understanding | Each cycle adds accuracy |

---

<a id="abinitio-artifact-reference"></a>
## Ab Initio Artifact Reference

| Artifact | Extension | Purpose | Analysis Value |
|----------|-----------|---------|----------------|
| DML record format | `.dml` | Field-level record layout (delimiters, packed decimals, nested/variable-length records, `null(...)` defaults) | The schema contract — drives target StructType/Delta types and parity risks |
| Graph | `.mp` / (here) `.py` | Dataflow: inputs → transforms (reformat/join/rollup/partition/CDC) → outputs | Core analysis target — business logic and data transformations |
| PSET (parameter set) | `.pset` | Run parameters bound to a graph | Maps to job parameters / `vars` / `DBT_SCHEMA`; reveals run variants |
| KornShell wrapper | `.ksh` | Phase orchestration, scheduled by AutoSys/Control-M | Maps to a job DAG / Databricks Workflow; reveals phase dependencies |
| DML parser / utils | `.py` | How layouts are read in this estate | Confirms delimiter/null semantics for faithful conversion |

---

<a id="customer-artifact-collection"></a>
## Customer Artifact Collection — What to Request

To run this assessment against a real customer estate, request (non-invasively):

- **DML library** — all `.dml` record formats (the schema contracts)
- **Graphs** — exported `.mp`/`.ksh`/source for the in-scope graphs
- **PSETs** — parameter sets for the graphs (run variants, environment values)
- **Orchestration** — KornShell/AutoSys/Control-M job definitions and phase chains
- **Sample data + record counts** — a small extract per feed plus production row counts/volumes (for completeness and control-total checks)
- **Lookups/reference data** — any reformat lookup files the graphs depend on

None of this requires touching the running Co>Operating System — it is static
artifact collection, which is what makes the assessment safe to run early.

---

<a id="going-further"></a>
## Going Further — Automation & Scale

- **Parallel conversion** — fan out across graphs with child sessions (Lab 4); each produces its own verified PR
- **Scheduled re-assessment** — schedule a recurring session that re-runs the estate assessment as the source repo changes, so the migration backlog stays current
- **Event-driven** — wire a webhook so new Ab Initio code committed to the source repo triggers an incremental assessment automatically
- **Knowledge capture** — encode customer-specific conventions (delimiter quirks, packed-decimal scales, CDC hash columns) as Knowledge notes so every future session applies them consistently
