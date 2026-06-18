# COBOL System Understanding & Reverse Engineering

## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Going Further](#going-further)
- [Notes](#notes)
- [uc-legacy-modernization-cobol-to-java](#uc-legacy-modernization-cobol-to-java)

---

## Quick Start

Paste this prompt into Devin to try estate discovery on the CardDemo COBOL application:

```
Analyze the entire COBOL estate in
uc-legacy-modernization-cobol-to-java. Produce the following
artifacts:

1. APPLICATION_INVENTORY.md — list every program in app/cbl/
   and sub-application directories with: filename, purpose
   (inferred from code), classification (batch/online), key
   I/O operations (files read/written, DB2 tables accessed),
   and copybooks referenced. Also catalog all JCL jobs in
   app/jcl/ with their step sequences.
2. DATA_DICTIONARY.md — for every copybook in app/cpy/,
   extract: field names, PIC clauses, data types, business
   meaning (inferred), and validation rules. Group fields by
   business entity (account, customer, card, transaction).
3. DEPENDENCY_MAP.md — build a call graph showing which
   programs CALL or PERFORM other programs. Map dataset
   lineage: which JCL jobs read/write which files, and which
   programs process them. Identify the end-to-end batch
   pipeline flow.
4. HOTSPOT_REPORT.md — rank the top 10 programs by: lines of
   code, number of copybooks referenced, number of I/O
   operations, business logic density (EVALUATE/IF nesting
   depth), and inter-program dependencies. Recommend which
   programs to modernize first and why.
```

---

## Repositories

- [uc-legacy-modernization-cobol-to-java](#uc-legacy-modernization-cobol-to-java)

---

## Challenge

Use Devin to reverse-engineer a real COBOL mainframe application and produce concrete system-understanding artifacts: an application inventory, a data dictionary extracted from copybooks, a dependency and data-flow model, and a hotspot report identifying the highest-risk modules. This is the discovery phase that normally takes weeks of SME interviews — Devin can compress it into a single session.

In most COBOL modernization programs, **understanding the existing system is the biggest blocker**. Tribal knowledge lives in the heads of retiring developers, documentation is sparse or outdated, and the codebase uses conventions that modern engineers cannot read. This module shows how Devin turns an opaque COBOL estate into a queryable, documented knowledge surface.

## Target Outcomes

- `APPLICATION_INVENTORY.md` — complete catalog of programs (.cbl), copybooks (.cpy), JCL jobs (.jcl), BMS screen maps (.bms), data files, and sub-applications with classification (batch vs. online, I/O-heavy vs. compute)
- `DATA_DICTIONARY.md` — business entities, fields, validation rules, and data types extracted from copybooks and WORKING-STORAGE sections
- `DEPENDENCY_MAP.md` — call graph (which programs call which), dataset/table lineage (inputs → transforms → outputs), and batch pipeline map (job steps + dataset dependencies)
- `HOTSPOT_REPORT.md` — top 10 modules ranked by business logic complexity, change risk, and regulatory impact with migration priority recommendations
- PR with all artifacts and a structured summary

## What Participants Will Learn

- How Devin reads and navigates a COBOL codebase that most modern developers cannot understand
- How copybook analysis reveals the hidden data model of a mainframe application
- How call graphs and data lineage expose the true architecture buried in JCL and PERFORM statements
- How hotspot detection prioritizes where to focus modernization effort
- The value of DeepWiki as a persistent knowledge surface for legacy codebases

## Devin Features Exercised

- DeepWiki for codebase exploration and architecture visualization
- AskDevin for targeted questions about legacy code
- Multi-file analysis across programs, copybooks, JCL, and data definitions
- Documentation generation (structured markdown with tables and diagrams)
- Large-scale code comprehension (30+ programs, 30+ copybooks)

## Difficulty

Intermediate

## Estimated Time

60 minutes

## Going Further

- **Child sessions for parallel discovery**: A parent session can divide the estate by sub-application (main app, authorization, transaction-type, VSAM/MQ variant) and spawn a child session for each. Each child produces its own inventory and dependency map; the parent merges them into a consolidated view.
- **Scheduled re-analysis**: Configure a weekly scheduled session that re-runs the inventory against the repo. As migration progresses, the inventory reflects which programs have been modernized and which remain.
- **Event-driven triggers**: Connect a webhook so that when a new COBOL program is committed, Devin automatically analyzes it and updates the inventory.
- **Knowledge notes**: Capture the data dictionary and dependency map as org-level knowledge notes so every future Devin session working on this codebase starts with the accumulated understanding.
- **Playbook-driven discovery**: Encode the 4-artifact analysis (inventory, data dictionary, dependency map, hotspot report) as a playbook. Reuse it across multiple COBOL estates — each new customer engagement starts with the same proven process.

## Notes

- This module produces **analysis artifacts, not code changes** — it is the discovery phase before any migration begins
- The outputs from this module feed directly into [COBOL Migration Planning](cobol-migration-planning.md) (migration planning) and [Migration Test Harness](migration-test-harness.md) (test harness design)
- Encourage participants to use DeepWiki first to get oriented, then use Devin sessions for deep-dive analysis
- The CardDemo app has multiple sub-applications (main app, authorization with IMS/DB2/MQ, transaction type with DB2, VSAM/MQ variant) — participants should discover these through the inventory exercise
- Compare inventories across participants — did everyone find the same programs and dependencies?

---

## <a id="uc-legacy-modernization-cobol-to-java"></a>uc-legacy-modernization-cobol-to-java

**Repository:** [uc-legacy-modernization-cobol-to-java](https://github.com/Cognition-Partner-Workshops/uc-legacy-modernization-cobol-to-java)

AWS Mainframe Modernization CardDemo (Apache 2.0). Contains 30+ COBOL programs, copybooks, JCL, BMS screen maps, DB2 DDL, VSAM definitions, IMS DBDs/PSBs, ASCII + EBCDIC data files, and multiple sub-applications.

### Step 1: Paste into Devin — Estate Discovery

```
Analyze the entire COBOL estate in
uc-legacy-modernization-cobol-to-java. Produce the following
artifacts:

1. APPLICATION_INVENTORY.md — list every program in app/cbl/
   and sub-application directories with: filename, purpose
   (inferred from code), classification (batch/online), key
   I/O operations (files read/written, DB2 tables accessed),
   and copybooks referenced. Also catalog all JCL jobs in
   app/jcl/ with their step sequences.
2. DATA_DICTIONARY.md — for every copybook in app/cpy/,
   extract: field names, PIC clauses, data types, business
   meaning (inferred), and validation rules. Group fields by
   business entity (account, customer, card, transaction).
3. DEPENDENCY_MAP.md — build a call graph showing which
   programs CALL or PERFORM other programs. Map dataset
   lineage: which JCL jobs read/write which files, and which
   programs process them. Identify the end-to-end batch
   pipeline flow.
4. HOTSPOT_REPORT.md — rank the top 10 programs by: lines of
   code, number of copybooks referenced, number of I/O
   operations, business logic density (EVALUATE/IF nesting
   depth), and inter-program dependencies. Recommend which
   programs to modernize first and why.
```

### Step 2: Research with Ask Devin

- *"What are the main sub-applications in uc-legacy-modernization-cobol-to-java and how do they differ? What data stores does each use (VSAM, DB2, IMS)?"*
- *"Which copybooks are shared across the most programs? What business entities do they represent?"*
- *"What is the end-to-end transaction processing flow from card swipe to statement generation?"*
- Use the answers to enrich your inventory and identify gaps in the initial analysis

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to see auto-generated architecture diagrams and module relationships. Use DeepWiki to:
- Identify program clusters (which programs work together)
- Understand the data model from copybook relationships
- Trace transaction flows across programs
- This becomes the **queryable knowledge surface** that accelerates all subsequent modernization work

### Step 4 (Optional): Review & Give Feedback

- **Review the inventory** — did Devin find all sub-applications? Are the program classifications accurate?
- **Review the data dictionary** — are the business entity groupings sensible? Did Devin infer field meanings correctly?
- **Leave a comment** asking Devin to add a section on PII fields (which copybooks contain personally identifiable information like customer names, SSNs, account numbers)
- **Leave a comment** asking Devin to identify which JCL jobs are likely run daily vs. monthly vs. on-demand

### Key Takeaways

- **Automated estate discovery**: Devin can inventory an entire COBOL codebase — programs, copybooks, JCL, data files — in a single session, compressing weeks of manual discovery into hours
- **Queryable knowledge surface**: The inventory, data dictionary, and dependency map become persistent artifacts that inform every subsequent modernization decision
- **Hotspot-driven prioritization**: Complexity metrics and dependency analysis guide where to focus migration effort first
- **Foundation for planning**: These artifacts feed directly into migration planning, test harness design, and domain decomposition
