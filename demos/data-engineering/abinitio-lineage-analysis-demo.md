# Ab Initio Lineage & Impact Analysis Demo

A single linear demo on the synthetic Ab Initio loan-servicing estate
[ts-abinitio-loan-servicing](https://github.com/Cognition-Partner-Workshops/ts-abinitio-loan-servicing):
ask Devin how the marquee mart column is derived, codify a completed
column-level lineage analysis into a reusable Skill, fan the Skill out across
additional mart attributes with parallel child sessions, and consolidate the
results into a Confluence page created live during the demo.

The estate is self-contained and static: 7 textual graph exports, 7 XFR
transforms, 10 DML layouts, embedded and companion Oracle / Teradata SQL,
KornShell orchestration, AutoSys JIL, and ground-truth answer keys under
`expected/`. No live database or Ab Initio runtime is used at any point.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Step 1 — Ask Devin about the estate](#step-1)
- [Step 2 — Codify the flow into a reusable Skill](#step-2)
- [Step 3 — Fan out with parallel child sessions](#step-3)
- [Step 4 — Review the consolidated Confluence page](#step-4)
- [Key Takeaways](#key-takeaways)

---

<a id="prerequisites"></a>
## Prerequisites

- A completed lineage analysis of
  `TERADATA_DW.LOAN_PORTFOLIO_MART.OUTSTANDING_LOAN_BALANCE` in
  `ts-abinitio-loan-servicing`, produced by a prior session that followed
  `.workshop/playbooks/abinitio-lineage-analysis.devin.md` and
  `.agents/skills/abinitio-lineage-analysis/SKILL.md`. Its deliverables live
  under `analysis/` on the session's PR branch: `LINEAGE.json`, `LINEAGE.md`
  (with a Mermaid diagram), `IMPACT_ANALYSIS.md`, `BUSINESS_DOCUMENTATION.md`,
  `lineage_viz.html`, and `BROWSER_TEST.md` — validated against
  `expected/outstanding_loan_balance_lineage.json` with
  `python tools/validate_expected_lineage.py` and verified by an in-browser
  click-through of the visualization with a screen recording.
- An Atlassian (Confluence) MCP integration with page-write access, and a
  Confluence parent page for Ab Initio lineage demo runs on the workshop
  Atlassian site (recorded in organization Knowledge). Each demo run publishes
  a new timestamped child page under this parent so past demo runs never
  clash.

---

<a id="step-1"></a>
## Step 1 — Ask Devin about the estate

Start in Ask Devin (chat) mode on `ts-abinitio-loan-servicing` to orient the
audience over the estate before any session runs:

```
How is OUTSTANDING_LOAN_BALANCE in LOAN_PORTFOLIO_MART computed, and what
would a full end-to-end lineage analysis of it involve?
```

The answer should surface the multi-hop derivation implemented in
`xfr/compute_outstanding_balance.xfr`:

```text
OUTSTANDING_LOAN_BALANCE
  = ORIGINAL_PRINCIPAL
  - CUMULATIVE_PRINCIPAL_PAID
  + ACCRUED_INTEREST
  - WRITTEN_OFF_AMOUNT
```

with payment aggregation in `xfr/rollup_payments.xfr`, day-count interest
arithmetic, dimension enrichment, and the final Teradata mart mapping in
`xfr/load_loan_portfolio_mart.xfr`.

---

<a id="step-2"></a>
## Step 2 — Codify the flow into a reusable Skill

In the session that completed the `OUTSTANDING_LOAN_BALANCE` analysis (see
[Prerequisites](#prerequisites)), paste:

```
Turn this flow into a top-quality reusable skill called
attribute-lineage-analysis. Parameterize on any mart column: exact procedure,
estate file conventions, deliverable formats, mandatory validation gates
(answer-key check + in-browser viz test with a screen recording), and
pitfalls you hit.
```

The result is a Skill at
`.agents/skills/attribute-lineage-analysis/SKILL.md`, parameterized on
`TARGET_ATTRIBUTE` / `PROPOSED_CHANGE` / `OUTPUT_DIR`, that encodes the estate
conventions (textual `.mp` component wiring, `out.field :: expression;` XFR
assignments, embedded `sql = << ... >>` blocks), the deliverable formats, both
validation gates, and the pitfalls found during the original analysis. This is
the shared context layer at work: one engineer's verified workflow becomes a
procedure any session in the organization can follow.

---

<a id="step-3"></a>
## Step 3 — Fan out with parallel child sessions

With the Skill in place, apply it to the remaining balance-sheet attributes of
the mart in parallel. In a coordinator session on
`ts-abinitio-loan-servicing`, paste:

```
Run 3 parallel child sessions using the attribute-lineage-analysis skill,
one each for CUMULATIVE_PRINCIPAL_PAID, ACCRUED_INTEREST, and
WRITTEN_OFF_AMOUNT. Then consolidate all four attributes into one
portfolio-wide lineage & impact report and publish it to Confluence as a new
timestamped child page under the Ab Initio lineage demos parent page.
```

Each child session runs the Skill end to end for its attribute — column-level
lineage, impact analysis, business documentation, interactive visualization,
answer-key/structural validation, and an in-browser click-through with a
screen recording — and returns a structured summary. The coordinator waits
for the children, merges the three new attributes with the verified
`OUTSTANDING_LOAN_BALANCE` result, and publishes the consolidated report.

This is the team-resource pattern: lineage analysis across independent
attributes is divide-and-conquer work, so wall-clock time stays roughly flat
as the estate grows. On a larger estate the same fan-out can be triggered by a
scheduled session or a repository webhook when graph, SQL, or schema files
change.

---

<a id="step-4"></a>
## Step 4 — Review the consolidated Confluence page

Open the timestamped Confluence page the coordinator just created (linked in
its final message). It contains the portfolio-wide view across all four
attributes:

- source-to-target lineage from the Oracle OLTP tables through the seven
  graph stages to `LOAN_PORTFOLIO_MART`;
- column-level derivations with the exact transform expression at each hop;
- a direct / downstream / non-impact matrix for the proposed
  `PAYMENT_TXN.PRINCIPAL_COMPONENT` rename;
- business-friendly narrative a non-technical BFSI stakeholder can review.

Because every run creates a new timestamped child page, the page shown is
provably the one created during this demo, and previous runs remain intact
for comparison.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **Lineage without runtime access** — graph exports, embedded SQL, XFR
  expressions, DML layouts, and orchestration metadata are sufficient for a
  useful static, column-level analysis.
- **Verification creates confidence** — results are checked against
  ground-truth answer keys, and the interactive visualization is click-tested
  in Devin's own browser with a screen recording.
- **Skills make the workflow repeatable** — a verified one-off analysis is
  codified into a parameterized Skill that any session can invoke.
- **Parallel child sessions scale the work** — independent attributes fan out
  across child sessions with a coordinator consolidating the findings, so
  larger estates increase compute, not calendar time.
- **Deliverables land where stakeholders live** — the consolidated report is
  published to Confluence through an MCP integration, closing the loop from
  raw ETL artifacts to business-readable documentation.
