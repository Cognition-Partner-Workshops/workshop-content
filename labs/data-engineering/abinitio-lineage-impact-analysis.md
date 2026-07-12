# Ab Initio Lineage & Impact Analysis — Hands-On Walkthrough

## Table of Contents

- [Quick Start](#quick-start)
- [Challenge](#challenge)
- [Repositories](#repositories)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty & Estimated Time](#difficulty--estimated-time)
- [Target Outcomes](#target-outcomes)
- [Hands-On Labs](#hands-on-labs)
  - [Lab 1: Map the estate](#lab-1-map-the-estate)
  - [Lab 2: Verify selected-attribute lineage](#lab-2-verify-lineage)
  - [Lab 3: Analyze a source-column change](#lab-3-impact-analysis)
  - [Lab 4: Generate business documentation](#lab-4-business-documentation)
  - [Lab 5: Scale with child sessions](#lab-5-scale-with-child-sessions)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

This walkthrough uses the synthetic `ts-abinitio-loan-servicing` repository.
Copy the prompt below into a Devin session to begin:

```
!abinitio-lineage-analysis

Analyze the static estate in ts-abinitio-loan-servicing. Use
OUTSTANDING_LOAN_BALANCE as the selected attribute and analyze the proposed
rename of PAYMENT_TXN.PRINCIPAL_COMPONENT to
PAYMENT_TXN.PRINCIPAL_PAID_AMOUNT.

Read README.md, .agents/skills/abinitio-lineage-analysis/SKILL.md,
graphs/*.mp, xfr/*.xfr, dml/*.dml, psets/*.pset, sql/oracle/*.sql,
sql/teradata/*.sql, scripts/*.ksh, and scheduler/loan_servicing.jil.
Produce LINEAGE.json, LINEAGE.md with Mermaid, IMPACT_ANALYSIS.md, and
BUSINESS_DOCUMENTATION.md. Verify the selected chain with
python tools/validate_expected_lineage.py. Do not use a live database,
invoke air, or modify source artifacts.
```

The source repository already contains the answer keys
`expected/outstanding_loan_balance_lineage.json` and
`expected/impact_analysis_example.md`. The analysis outputs are the artifacts
to review during the hands-on work.

---

<a id="challenge"></a>
## Challenge

Build an evidence-based view of a retail loan-servicing data flow from static
Ab Initio artifacts. The estate represents Oracle OLTP source tables, Ab Initio
graph stages, Teradata reference dimensions, and the
`LOAN_PORTFOLIO_MART` reporting table. Its marquee attribute is derived through
payment aggregation, accrued-interest calculation, dimensional enrichment, and
mart loading.

The challenge is intentionally non-invasive. There is no live Oracle or
Teradata connection and no Ab Initio runtime requirement. Devin must infer
dependencies from textual `.mp` graph exports, embedded and companion SQL,
`.xfr` assignments, `.dml` record layouts, `.pset` parameters, and orchestration
files. The answer keys provide a review contract without turning the exercise
into a finished lineage-extraction product.

---

<a id="repositories"></a>
## Repositories

- [ts-abinitio-loan-servicing](https://github.com/Cognition-Partner-Workshops/ts-abinitio-loan-servicing)
  — synthetic BFSI loan-servicing estate with 7 graph exports, 7 XFR
  transforms, 10 DML layouts, 7 PSETs, Oracle and Teradata SQL, deterministic
  sample data, KornShell and AutoSys orchestration, answer keys, a playbook,
  and a repo Skill.

The target repository is synthetic and intended for static analysis practice.
It does not represent a production environment.

---

<a id="what-participants-will-learn"></a>
## What Participants Will Learn

- How to inventory an Ab Initio estate before making lineage claims.
- How SQL dependencies, graph wiring, XFR expressions, DML layouts, PSETs,
  and batch orchestration combine into source-to-target lineage.
- How to trace a calculated business attribute through several intermediate
  records and graph stages.
- How to distinguish direct, downstream, and non-impact findings for a
  schema or logic change.
- How shared context — Knowledge notes, DeepWiki, MCP integrations, and
  playbooks — helps a team apply one analysis method across many attributes.

---

<a id="devin-features-exercised"></a>
## Devin Features Exercised

- Multi-file static analysis across Ab Initio-style graph, transform, schema,
  SQL, parameter, and orchestration artifacts.
- The `!abinitio-lineage-analysis` playbook for a repeatable procedure.
- The repo Skill in
  `.agents/skills/abinitio-lineage-analysis/SKILL.md` for local syntax and
  validator mechanics.
- DeepWiki and Knowledge notes for repository orientation and domain terms;
  coverage depends on repository structure and the quality of the notes.
- Structured artifact generation, including JSON, Markdown, and Mermaid.
- Child sessions for divide-and-conquer analysis of independent attributes.
- The PR feedback loop: a human reviews generated artifacts, leaves focused
  feedback, and Devin responds with a follow-up change.
- Optional MCP integrations for approved documentation and ticketing systems.

---

<a id="difficulty--estimated-time"></a>
## Difficulty & Estimated Time

**Intermediate–Advanced · 75 minutes**

The work is best suited to data engineers, analytics engineers, data
architects, and ETL developers who are comfortable reading SQL and ETL
metadata. No Oracle, Teradata, or Ab Initio runtime is required.

---

<a id="target-outcomes"></a>
## Target Outcomes

By the end of the hands-on work, participants should have five reviewable
artifacts or findings:

1. `LINEAGE.json` containing structured table-level and column-level lineage.
2. `LINEAGE.md` containing a readable explanation and Mermaid dataflow diagram.
3. A verified column-level chain for
   `LOAN_PORTFOLIO_MART.OUTSTANDING_LOAN_BALANCE`, checked against
   `expected/outstanding_loan_balance_lineage.json` with
   `python tools/validate_expected_lineage.py`.
4. `IMPACT_ANALYSIS.md` covering direct, downstream, and non-impact findings
   for the `PAYMENT_TXN.PRINCIPAL_COMPONENT` rename, cross-checked against
   `expected/impact_analysis_example.md`.
5. `BUSINESS_DOCUMENTATION.md` explaining the mart and the balance calculation
   for a non-technical BFSI stakeholder.

---

<a id="hands-on-labs"></a>
## Hands-On Labs

<a id="lab-1-map-the-estate"></a>
### Lab 1: Map the Estate

Start by building a source map. Read the root README and the repo Skill, then
inventory graph stages, transforms, record formats, parameters, SQL, and batch
dependencies.

#### Step 1: Paste into Devin

```
In ts-abinitio-loan-servicing, map the static Ab Initio estate before
extracting lineage. Read README.md and
.agents/skills/abinitio-lineage-analysis/SKILL.md.

Inventory graphs/*.mp, xfr/*.xfr, dml/*.dml, psets/*.pset,
sql/oracle/*.sql, sql/teradata/*.sql, scripts/*.ksh, and
scheduler/loan_servicing.jil. For each graph, record its input and output
components, ports and links, referenced DML, XFR, PSET, SQL, and predecessor
or successor stages.

Confirm that the 7 graph files are:
extract_loan_accounts, extract_payments, cdc_loan_balances,
rollup_payments, compute_outstanding_balance, enrich_dimensions, and
load_loan_portfolio_mart. Confirm that referenced artifact paths resolve.
Return a concise inventory in Markdown. Keep the analysis static.
```

#### Step 2: Review the map

Check that the map identifies:

- Oracle inputs for `LOAN_ACCOUNT`, `BORROWER`, `LOAN_PRODUCT`, and
  `PAYMENT_TXN`.
- Teradata references and the `LOAN_PORTFOLIO_MART` target.
- Payment rollup before balance calculation.
- The XFR and DML files bound to each graph.
- The AutoSys and KornShell dependency chain.

Use Ask Devin or a Knowledge note to preserve terms such as
`CUMULATIVE_PRINCIPAL_PAID`, `ACCRUED_INTEREST`, and
`WRITTEN_OFF_AMOUNT` for the next session.

#### Key Takeaways

- Static analysis starts with inventory and artifact relationships.
- Textual graph exports make component wiring inspectable without a runtime.
- The shared context layer lets later sessions reuse the estate vocabulary.

---

<a id="lab-2-verify-lineage"></a>
### Lab 2: Verify Selected-Attribute Lineage

Trace `OUTSTANDING_LOAN_BALANCE` from source columns through each intermediate
record and graph stage to the final mart column. This is the core walkthrough.

#### Step 1: Paste into Devin

```
In ts-abinitio-loan-servicing, extract end-to-end source-to-target lineage
for LOAN_PORTFOLIO_MART.OUTSTANDING_LOAN_BALANCE.

Parse embedded and companion SQL under sql/oracle/ and sql/teradata/,
component wiring in graphs/*.mp, field assignments in
xfr/extract_loan_accounts.xfr, xfr/extract_payments.xfr,
xfr/rollup_payments.xfr, xfr/compute_outstanding_balance.xfr,
xfr/enrich_dimensions.xfr, and xfr/load_loan_portfolio_mart.xfr, plus the
relevant DML layouts under dml/.

Preserve the complete chain for ORIGINAL_PRINCIPAL,
PRINCIPAL_COMPONENT, WRITTEN_OFF_AMOUNT, ANNUAL_INTEREST_RATE,
ORIGINATION_DATE, and SNAPSHOT_DATE. Include intermediate fields and the
formula for accrued interest and outstanding balance.

Write LINEAGE.json and LINEAGE.md. Put a Mermaid dataflow diagram in
LINEAGE.md. Run python tools/validate_expected_lineage.py and compare the
selected chain with expected/outstanding_loan_balance_lineage.json.
```

#### Step 2: Review the derivation

The expected formula is:

```text
OUTSTANDING_LOAN_BALANCE
  = ORIGINAL_PRINCIPAL
  - CUMULATIVE_PRINCIPAL_PAID
  + ACCRUED_INTEREST
  - WRITTEN_OFF_AMOUNT
```

Review the evidence in:

- `sql/oracle/extract_loan_accounts.sql`
- `sql/oracle/extract_payments.sql`
- `xfr/rollup_payments.xfr`
- `xfr/compute_outstanding_balance.xfr`
- `xfr/enrich_dimensions.xfr`
- `xfr/load_loan_portfolio_mart.xfr`
- `expected/outstanding_loan_balance_lineage.json`

Ask Devin to identify any path that is inferred rather than explicitly
supported by a SQL reference, graph link, XFR expression, or DML field.

#### Key Takeaways

- Column lineage is strongest when each hop has an artifact-level citation.
- The selected balance is a multi-hop derivation, not a direct passthrough.
- An answer key provides a deterministic review checkpoint for the analysis.

---

<a id="lab-3-impact-analysis"></a>
### Lab 3: Analyze a Source-Column Change

Analyze the proposed rename of
`PAYMENT_TXN.PRINCIPAL_COMPONENT` to
`PAYMENT_TXN.PRINCIPAL_PAID_AMOUNT`. Follow the changed field through SQL,
DML, extraction, rollup, balance, enrichment, and mart artifacts.

#### Step 1: Paste into Devin

```
In ts-abinitio-loan-servicing, analyze this proposed schema change:

Rename PAYMENT_TXN.PRINCIPAL_COMPONENT to
PAYMENT_TXN.PRINCIPAL_PAID_AMOUNT.

Find direct references in sql/oracle/loan_servicing_schema.sql,
sql/oracle/extract_payments.sql, graphs/extract_payments.mp,
dml/payment_txn.dml, and xfr/extract_payments.xfr. Follow downstream
references through xfr/rollup_payments.xfr,
xfr/compute_outstanding_balance.xfr,
xfr/enrich_dimensions.xfr, and xfr/load_loan_portfolio_mart.xfr.

Separate direct impacts, downstream impacts, and non-impacts. Write
IMPACT_ANALYSIS.md with artifact paths, affected columns, dependency paths,
and recommended validation. Cross-check it against
expected/impact_analysis_example.md. Do not edit source artifacts.
```

#### Step 2: Review through the feedback loop

Review the report as a team. Ask Devin to clarify an ambiguous dependency,
add a missing non-impact, or cite a specific graph link. The normal PR
feedback loop is useful even for documentation: a reviewer can request a
more precise impact boundary, and Devin can respond with a focused follow-up
commit for human review.

#### Key Takeaways

- Direct references and transitive consequences should be reported separately.
- Non-impacts prevent an impact report from becoming an undifferentiated list.
- Review comments improve evidence quality without requiring runtime access.

---

<a id="lab-4-business-documentation"></a>
### Lab 4: Generate Business Documentation

Translate the technical findings into a data dictionary and narrative for a
non-technical BFSI stakeholder. Preserve exact field names where traceability
matters, but explain the business meaning in plain language.

#### Step 1: Paste into Devin

```
Using the verified lineage in ts-abinitio-loan-servicing, create
BUSINESS_DOCUMENTATION.md for a non-technical BFSI stakeholder.

Describe LOAN_PORTFOLIO_MART, borrower/product/branch enrichment, payment
measures, write-offs, accrued interest, the snapshot date, and the business
meaning of OUTSTANDING_LOAN_BALANCE. Explain the formula in plain language.
Include a compact table mapping source table.column to intermediate field,
graph/XFR hop, and final mart column.

State that this is a synthetic estate and that the result is based on static
artifacts rather than a live database or Ab Initio runtime. Use US English.
```

#### Step 2: Review the narrative

Confirm that the documentation distinguishes original principal, cumulative
principal paid, accrued interest, and written-off amount. Check that it does
not imply that sample `.dat` files are production records or that a live
Teradata mart was queried.

#### Key Takeaways

- Business documentation makes lineage useful beyond engineering teams.
- Exact technical citations and plain-language definitions can coexist.
- Static-analysis limits should be stated so stakeholders understand the
  evidence boundary.

---

<a id="lab-5-scale-with-child-sessions"></a>
### Lab 5: Scale with Child Sessions

Lineage campaigns often contain many independent target attributes. Use a
coordinator session and child sessions to divide the work while keeping the
playbook, Skill, and shared context consistent.

#### Step 1: Paste into Devin

```
Act as the coordinator for a static lineage campaign in
ts-abinitio-loan-servicing. Spawn one child Devin session for each target:

1. LOAN_PORTFOLIO_MART.OUTSTANDING_LOAN_BALANCE
2. LOAN_PORTFOLIO_MART.ACCRUED_INTEREST
3. LOAN_PORTFOLIO_MART.CUMULATIVE_PRINCIPAL_PAID
4. LOAN_PORTFOLIO_MART.BRANCH_REGION

Give each child the repo Skill at
.agents/skills/abinitio-lineage-analysis/SKILL.md and require the
!abinitio-lineage-analysis playbook. Each child should produce a
namespaced LINEAGE.json and LINEAGE.md with a Mermaid diagram, cite the
relevant graphs, XFRs, DMLs, SQL, and PSETs, and report unresolved
references. Keep all work static and preserve the answer-key verification for
OUTSTANDING_LOAN_BALANCE.

After the children finish, consolidate their findings into a summary with
coverage, differences, unresolved references, and follow-up review items.
```

#### Step 2: Discuss automation and context

This pattern works well when a webhook or scheduled automation starts a
lineage refresh after graph, SQL, or schema changes. Knowledge notes can hold
the business glossary, DeepWiki can accelerate orientation, MCP integrations
can provide optional documentation or ticketing access, and the playbook keeps
the procedure consistent. A coordinator reviews the child outputs before the
team uses them in a PR feedback loop.

#### Key Takeaways

- Child sessions turn independent attributes into parallel work units.
- Shared context reduces repeated orientation and preserves terminology.
- Human review remains the gate for merging generated documentation or code.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **Start with evidence** — inventory graph, SQL, transform, schema, parameter,
  and orchestration artifacts before drawing a lineage conclusion.
- **Verify the critical chain** — the answer key and validator make the
  `OUTSTANDING_LOAN_BALANCE` result reviewable.
- **Bound the change** — direct, downstream, and non-impact categories make a
  source-column rename actionable.
- **Translate for stakeholders** — `BUSINESS_DOCUMENTATION.md` turns technical
  dependencies into a plain-language BFSI narrative.
- **Scale as a team** — playbooks, Knowledge, DeepWiki, MCP integrations,
  scheduled automation, child sessions, and PR feedback form a shared context
  and collaboration loop.
