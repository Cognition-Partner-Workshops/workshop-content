# Ab Initio Lineage & Impact Analysis Demo

A single linear demo that shows Devin analyzing a synthetic Ab Initio
loan-servicing estate without a live database or runtime: orient over the
artifacts, build source-to-target and column-level lineage for
`OUTSTANDING_LOAN_BALANCE`, verify it against a ground-truth answer key, analyze
a source-column rename, generate business documentation, and then scale the
analysis across additional attributes with child sessions.

The prompts below invoke the `!abinitio-lineage-analysis` Devin Playbook. The
playbook source and repo-specific Skill live in the target repository at
`.workshop/playbooks/abinitio-lineage-analysis.devin.md` and
`.agents/skills/abinitio-lineage-analysis/SKILL.md`.

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Part 1 — Devin Analyzes the Estate](#part-1)
  - [Act 1 — Orient over the estate](#act-1)
  - [Act 2 — Build and verify lineage](#act-2)
  - [Act 3 — Analyze a schema change](#act-3)
  - [Act 4 — Generate business documentation](#act-4)
  - [Act 5 — Fan out across attributes](#act-5)
- [Optional Integrations](#optional-integrations)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Open a Devin session with the `ts-abinitio-loan-servicing` repository and
paste the following prompt:

```
!abinitio-lineage-analysis

Analyze the static Ab Initio estate in
ts-abinitio-loan-servicing. Use the default selected attribute
OUTSTANDING_LOAN_BALANCE and the default proposed change:
rename PAYMENT_TXN.PRINCIPAL_COMPONENT to
PAYMENT_TXN.PRINCIPAL_PAID_AMOUNT.

Start with README.md, then inventory graphs/*.mp, xfr/*.xfr, dml/*.dml,
psets/*.pset, sql/oracle/*.sql, sql/teradata/*.sql, scripts/*.ksh, and
scheduler/loan_servicing.jil. Produce LINEAGE.json, LINEAGE.md with a
Mermaid diagram, IMPACT_ANALYSIS.md, and BUSINESS_DOCUMENTATION.md as generated
analysis artifacts. Verify the selected-attribute chain with
python tools/validate_expected_lineage.py and compare the impact report with
expected/impact_analysis_example.md. Do not connect to a database, invoke
air, or modify source artifacts.
```

The remainder of this page follows the same analysis in smaller visible steps.

---

<a id="repositories"></a>
## Repositories

- [ts-abinitio-loan-servicing](https://github.com/Cognition-Partner-Workshops/ts-abinitio-loan-servicing)
  — synthetic Ab Initio loan-servicing estate with 7 textual graph exports,
  7 XFR transforms, 10 DML layouts, 7 PSETs, embedded and companion Oracle /
  Teradata SQL, sample data, KornShell orchestration, AutoSys JIL, and
  ground-truth lineage and impact-analysis answer keys.
- [workshop-metadata](https://github.com/Cognition-Partner-Workshops/workshop-metadata)
  — attendee-facing walkthroughs and hands-on modules, including this demo
  guide.

The analysis target is the first repository. The second repository is only the
content source for this walkthrough.

---

<a id="part-1"></a>
## Part 1 — Devin Analyzes the Estate

The estate is intentionally self-contained. Its `.mp` files are textual
export-style representations with component wiring and SQL, while the `.xfr`
files preserve field-level expressions in source control. No live Oracle,
Teradata, or Ab Initio environment is needed.

<a id="act-1"></a>
### Act 1 — Orient over the estate

Begin with the repository map. The objective is to identify the graph stages,
their record formats, SQL dependencies, runtime parameters, and orchestration
order before asking for lineage.

Paste:

```
Using ts-abinitio-loan-servicing, orient over the full static estate.
Read README.md and the repo Skill at
.agents/skills/abinitio-lineage-analysis/SKILL.md.

Inventory the 7 graph exports in graphs/*.mp, the 7 transforms in xfr/*.xfr,
the DML layouts in dml/*.dml, the PSETs in psets/*.pset, SQL under
sql/oracle/ and sql/teradata/, scripts/*.ksh, and
scheduler/loan_servicing.jil.

Return an artifact inventory and a stage-by-stage table showing each graph's
inputs, outputs, record formats, transform, SQL, PSET, and orchestration
predecessors. Confirm that every referenced graph, XFR, DML, PSET, and SQL
path resolves. Keep this static: do not connect to a database or invoke air.
```

Expected orientation includes these seven stages:

1. `graphs/extract_loan_accounts.mp`
2. `graphs/extract_payments.mp`
3. `graphs/cdc_loan_balances.mp`
4. `graphs/rollup_payments.mp`
5. `graphs/compute_outstanding_balance.mp`
6. `graphs/enrich_dimensions.mp`
7. `graphs/load_loan_portfolio_mart.mp`

The source tables are defined in
`sql/oracle/loan_servicing_schema.sql`, while the Teradata reference and mart
tables are defined in `sql/teradata/reference_and_mart.sql`. Embedded SQL
appears in the input/output components in the `.mp` files and is also
available in companion files such as
`sql/oracle/extract_loan_accounts.sql` and
`sql/oracle/extract_payments.sql`.

<a id="act-2"></a>
### Act 2 — Build and verify lineage

Now ask Devin to follow the graph links, SQL column references, DML fields,
and XFR expressions. The important distinction is between a table-level path
and the complete field-level derivation.

Paste:

```
In ts-abinitio-loan-servicing, build end-to-end table-level and column-level
lineage from the Oracle source tables through the Ab Initio graph exports to
Teradata LOAN_PORTFOLIO_MART.

Focus on OUTSTANDING_LOAN_BALANCE. Trace each source column through
graphs/extract_loan_accounts.mp, graphs/extract_payments.mp,
graphs/rollup_payments.mp, graphs/compute_outstanding_balance.mp,
graphs/enrich_dimensions.mp, and graphs/load_loan_portfolio_mart.mp.
Use the exact field assignments in
xfr/extract_loan_accounts.xfr, xfr/extract_payments.xfr,
xfr/rollup_payments.xfr, xfr/compute_outstanding_balance.xfr,
xfr/enrich_dimensions.xfr, and xfr/load_loan_portfolio_mart.xfr.

Preserve intermediate fields and formulas. Include the paths from
LOAN_ACCOUNT.ORIGINAL_PRINCIPAL,
PAYMENT_TXN.PRINCIPAL_COMPONENT,
PAYMENT_TXN.WRITTEN_OFF_AMOUNT,
LOAN_ACCOUNT.ANNUAL_INTEREST_RATE,
LOAN_ACCOUNT.ORIGINATION_DATE, and
LOAN_ACCOUNT.SNAPSHOT_DATE.

Write LINEAGE.json and LINEAGE.md. Include a Mermaid
dataflow diagram in LINEAGE.md. Then run:
python tools/validate_expected_lineage.py
Compare the result with expected/outstanding_loan_balance_lineage.json and
report any discrepancy without changing the answer key.
```

The marquee formula is:

```text
OUTSTANDING_LOAN_BALANCE
  = ORIGINAL_PRINCIPAL
  - CUMULATIVE_PRINCIPAL_PAID
  + ACCRUED_INTEREST
  - WRITTEN_OFF_AMOUNT
```

The non-trivial path includes payment aggregation in
`xfr/rollup_payments.xfr`, day-count interest calculation and balance arithmetic
in `xfr/compute_outstanding_balance.xfr`, then enrichment and final mart
mapping. A table-level diagram alone is insufficient; the output should show
the intermediate fields and each transform hop.

<a id="act-3"></a>
### Act 3 — Analyze a schema change

The next step demonstrates impact analysis. The default change renames
`PAYMENT_TXN.PRINCIPAL_COMPONENT`. Devin should separate direct references from
transitive effects and record paths that do not depend on the changed column.

Paste:

```
In ts-abinitio-loan-servicing, perform static impact analysis for this
proposed change:

Rename PAYMENT_TXN.PRINCIPAL_COMPONENT to
PAYMENT_TXN.PRINCIPAL_PAID_AMOUNT.

Search the Oracle DDL, companion SQL, embedded SQL in graphs/*.mp,
dml/payment_txn.dml, xfr/extract_payments.xfr,
xfr/rollup_payments.xfr, and all downstream balance and mart artifacts.
Separate direct impacts, downstream impacts, and non-impacts. Explain how
the rename changes payment_rollup.cumulative_principal_paid,
loan_balance_calc.cumulative_principal_paid, and
LOAN_PORTFOLIO_MART.OUTSTANDING_LOAN_BALANCE.

Write IMPACT_ANALYSIS.md with affected artifact paths, affected
columns, the dependency path, and recommended follow-up validation. Compare
the findings with expected/impact_analysis_example.md without editing it.
Remain non-invasive: do not change source files or connect to a database.
```

The expected report includes direct effects in
`sql/oracle/loan_servicing_schema.sql`,
`sql/oracle/extract_payments.sql`,
`graphs/extract_payments.mp`,
`xfr/extract_payments.xfr`, and `dml/payment_txn.dml`. It also includes
downstream effects in the payment rollup, balance calculation, enrichment, and
mart load.

<a id="act-4"></a>
### Act 4 — Generate business documentation

The final single-session step translates technical lineage into language that
a BFSI stakeholder can review. It should explain the mart, the balance
calculation, the as-of-date behavior, and the source fields without suggesting
that this synthetic estate contains production data.

Paste:

```
Using the verified lineage in ts-abinitio-loan-servicing, generate
BUSINESS_DOCUMENTATION.md for a non-technical BFSI stakeholder.

Describe LOAN_PORTFOLIO_MART, the borrower/product/branch dimensions, payment
and write-off measures, accrued interest, and the business meaning of
OUTSTANDING_LOAN_BALANCE. Explain the balance formula in plain language and
include a concise technical mapping table with source table.column,
intermediate field, graph/XFR hop, and final mart column.

State that the estate is synthetic and that the analysis is static with no
live database or Ab Initio runtime. Use US English and keep the documentation
business-friendly while preserving exact source column names.
```

<a id="act-5"></a>
### Act 5 — Fan out across attributes

Once the marquee attribute is verified, the same playbook can be applied to
other mart columns. Use a parent session as the coordinator and one child
session per independent attribute. Keep each child focused and ask the parent
to consolidate the outputs.

Paste:

```
Act as the coordinator for a static lineage campaign in
ts-abinitio-loan-servicing. Use child Devin sessions to analyze these
independent target attributes:

1. OUTSTANDING_LOAN_BALANCE ->
   LOAN_PORTFOLIO_MART.OUTSTANDING_LOAN_BALANCE
2. ACCRUED_INTEREST ->
   LOAN_PORTFOLIO_MART.ACCRUED_INTEREST
3. CUMULATIVE_PRINCIPAL_PAID ->
   LOAN_PORTFOLIO_MART.CUMULATIVE_PRINCIPAL_PAID
4. BRANCH_REGION ->
   LOAN_PORTFOLIO_MART.BRANCH_REGION

Give each child the repo Skill at
.agents/skills/abinitio-lineage-analysis/SKILL.md and ask it to follow the
!abinitio-lineage-analysis playbook. Each child should read only the relevant
graphs, XFRs, DMLs, SQL, PSETs, and expected artifacts, then produce a
namespaced LINEAGE.json and LINEAGE.md with a Mermaid diagram. Do not use live
database or Ab Initio runtime access.

After the children finish, consolidate their findings into a campaign summary
that lists coverage, unresolved references, and any differences between
attributes. Preserve the verified OUTSTANDING_LOAN_BALANCE answer-key result.
```

This is the team-resource pattern: the coordinator establishes scope and
reviews the combined findings while child sessions analyze independent paths.
For a larger estate, the same pattern can be triggered by a scheduled
automation or a repository webhook when graph, SQL, or schema files change.

---

<a id="optional-integrations"></a>
## Optional Integrations

After review, the business documentation can be published to a knowledge base
through an available wiki integration. A material impact finding can also be
filed as a Jira ticket or equivalent work item. These are optional follow-up
steps; the static files and answer-key verification are the source of truth for
this walkthrough.

The shared context layer can improve repeatability across sessions:

- Playbooks provide the analysis procedure.
- The repo Skill provides artifact-specific syntax and commands.
- Knowledge notes preserve domain terminology and review preferences.
- DeepWiki can accelerate repository orientation; coverage depends on
  repository structure.
- MCP integrations can provide optional access to approved documentation or
  ticketing systems.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **Lineage without runtime access** — graph exports, embedded SQL, XFR
  expressions, DML layouts, and orchestration are sufficient inputs for a
  useful static analysis.
- **Column-level evidence matters** — the balance result is a multi-hop
  derivation, not a direct table mapping.
- **Verification creates confidence** — the selected chain is checked against
  `expected/outstanding_loan_balance_lineage.json`, while the rename analysis
  is compared with `expected/impact_analysis_example.md`.
- **Impact analysis follows dependencies** — a source-column rename reaches
  SQL, DML, XFR, rollup, balance, enrichment, mart, and business-documentation
  artifacts, while unrelated paths remain non-impacts.
- **Devin works as a team resource** — a coordinator can divide independent
  attributes across child sessions, use shared context, and bring findings
  back through the normal human review and PR feedback loop.
