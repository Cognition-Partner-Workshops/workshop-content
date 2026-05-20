# SAS Migration Analysis — Facilitator Guide

Companion to [sas-migration-analysis.md](sas-migration-analysis.md). This file is for the workshop host/facilitator — it covers setup, MCP configuration, and presales/delivery positioning that attendees do not need to see.

---

## Pre-Session Setup Checklist

Complete these steps before the workshop session. Most take 5–15 minutes but some (DeepWiki indexing) should be triggered the day before.

### Required

- [ ] **Git connections** — Both repos (`ts-sas-legacy-analytics` and `uc-data-migration-sas-to-databricks`) must be connected in Devin's org settings so Devin can clone them without manual auth
- [ ] **DeepWiki indexing** — Trigger indexing for both repos. DeepWiki takes 5–15 minutes to produce usable docs. Index before the session, not during it
- [ ] **Knowledge notes** — Create org-level knowledge items for migration conventions:
  - *"When migrating SAS constructs to dbt"* → Document which SAS patterns map to which dbt patterns (reference `SAS_TO_DBT_MIGRATION_MAP.md`)
  - *"When analyzing SAS autoexec.sas"* → Document the LIBNAME-to-Unity-Catalog mapping for the environment
  - *"When estimating migration effort"* → Document complexity scoring criteria (LOC thresholds, construct weights)

### Recommended (accelerates participant experience)

- [ ] **MCP servers** — If the customer's migration project is tracked in Jira or ADO, connect the relevant MCP server so Devin can read ticket context (acceptance criteria, priority, business owner) during analysis
- [ ] **Secrets** — If Devin needs to query a Databricks workspace (e.g., to check Unity Catalog schema), provision a scoped token as an org secret
- [ ] **Environment blueprint** — Pre-install `dbt-core` and `dbt-databricks` in the VM blueprint so Devin can validate dbt syntax (`dbt parse`, `dbt compile`) without install delays during the session

---

## Presales Positioning

### Static Analysis vs. Runtime Trace Tools

The key differentiator when positioning against competing approaches: Devin uses **static code analysis** — no changes to the customer's SAS environment are required. Runtime trace-based tools require XML logging configuration changes, audit logger enablement, and `-logconfigloc` updates on the production SAS environment.

This matters because:
- Customers are reluctant to modify production SAS environments for assessment
- Removing that requirement shortens the time from "interested" to "analysis started" from weeks to hours
- The email template in the attendee module can be sent immediately — no internal change control process needed

### For Scoping and Pricing

Run the estate discovery step (Lab 1) during presales. The resulting `SAS_MIGRATION_ASSESSMENT.md` gives you program-level complexity scores and a recommended migration sequence — this is the input for effort estimation and project pricing.

### For Delivery

The shared context layer means every Devin session working on the migration has access to the same Knowledge, conventions, and target architecture. Spin up parallel sessions — one per SAS program — and each one inherits the org-level migration standards. The feedback loop scales horizontally.

### Context Loop Differentiator

- **Manual assessment**: Consultant reads SAS code, writes a document, sends it for review, gets feedback, revises. Each cycle takes days. Context is in the consultant's head — not transferable.
- **Devin assessment**: Devin reads the code programmatically, cross-references the target architecture, queries external systems for business context, produces a structured assessment, and iterates on feedback — all within the same session. Context is in the shared layer (Knowledge, DeepWiki, MCP) — every subsequent session starts with the full accumulated understanding.

---

## MCP Integration Opportunities

If the customer has these systems available, connecting them enriches the analysis:

| System | MCP Server | What It Adds |
|--------|-----------|--------------|
| Jira | `atlassian-mcp` | Migration ticket context — acceptance criteria, priority, business owner |
| Confluence | `atlassian-mcp` | Existing data dictionaries, business rules documentation |
| Azure DevOps | `ado` | Work item tracking, sprint planning for migration phases |
| Databricks | Direct API via secrets | Unity Catalog schema validation, existing table metadata |

Customers attending this workshop will likely **not** have their MCP servers connected. The facilitator should either:
1. Pre-configure a demo MCP integration (e.g., a Jira project with sample migration tickets) for participants to experience the enrichment, or
2. Walk through the MCP integration verbally and show how it would enhance the analysis in a real engagement

---

## Common Participant Questions

| Question | Answer |
|----------|--------|
| "Can this work on our SAS environment?" | Yes — share the email template from the attendee module. All that's needed are the source files, no production access |
| "How long does the full assessment take?" | For an estate of 50–100 programs, expect 2–4 hours of Devin compute time with iterative feedback cycles |
| "What about SAS/ACCESS connections to databases?" | Devin identifies the connection references in autoexec.sas and config files. The actual database connection details are mapped to Unity Catalog equivalents in the migration plan |
| "Can Devin actually run the SAS code?" | No — there is no SAS runtime in the workshop environment. The analysis is entirely static. For runtime validation, the translated dbt models can be tested against Databricks |
| "How does this compare to tool X?" | Position on the non-invasive angle. Most tools require production environment changes. Devin's static analysis starts from source files only |

---

## Timing Guide

| Activity | Duration | Notes |
|----------|----------|-------|
| Lab 1: Estate Discovery | 25 min | Participants paste prompt and review while Devin works |
| Lab 2: dbt Target Mapping | 20 min | Can start while Lab 1 PR is still being reviewed |
| Lab 3: Validate & Extend | 20 min | Most hands-on — participants review dbt model output |
| Discussion & Q&A | 10 min | Use the Key Takeaways as discussion prompts |
| **Total** | **75 min** | |

For shorter sessions (45–60 min), focus on Lab 1 only and use Labs 2–3 as take-home exercises.
