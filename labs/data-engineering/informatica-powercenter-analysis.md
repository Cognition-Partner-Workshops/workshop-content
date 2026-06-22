# Informatica PowerCenter Analysis

## Table of Contents

- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [PowerCenter XML Artifact Types](#powercenter-xml-artifact-types)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [ts-informatica-powercenter](#ts-informatica-powercenter)
- [Going Further](#going-further)

---

## Challenge

Analyze an Informatica PowerCenter estate exported as XML to produce a comprehensive inventory of mappings, workflows, sources, targets, transformations, and data lineage. This challenge tests Devin's ability to parse complex vendor-specific XML metadata, trace data flows across ETL components, and generate actionable documentation for migration planning.

This is a **non-invasive, static analysis** exercise: Devin reads the XML exports directly from the repository — no Informatica PowerCenter server, no repository connection, and no modifications to the source system are required. The analysis produces migration-readiness artifacts from metadata alone.

## Quick Start

1. Open Devin and connect to the **ts-informatica-powercenter** repository
2. Paste the [Step 1 prompt](#step-1-paste-into-devin) into Devin
3. Watch Devin parse the PowerCenter XML, extract lineage, and open a PR with the assessment

## Repositories

- [ts-informatica-powercenter](#ts-informatica-powercenter)

---

## PowerCenter XML Artifact Types

| XML Element | Purpose | Key Attributes |
|-------------|---------|---------------|
| `SOURCE` | Source table or flat-file definition | `DATABASETYPE`, `DBDNAME`, `OWNERNAME` |
| `TARGET` | Target table definition | `DATABASETYPE`, `DBDNAME`, `OWNERNAME` |
| `MAPPING` | Transformation logic connecting sources to targets | `NAME`, `OBJECTVERSION` |
| `TRANSFORMATION` | Individual transform (Expression, Filter, Lookup, etc.) | `TYPE`, `NAME`, `TEMPLATENAME` |
| `CONNECTOR` | Dataflow link between transformation ports | `FROMFIELD`, `TOFIELD` |
| `SESSION` | Runtime configuration for a mapping | `MAPPINGNAME`, `SORTORDER` |
| `WORKFLOW` | Orchestration container for one or more sessions | `NAME`, `SCHEDULERNAME` |
| `FOLDER` | Logical grouping of all objects | `NAME`, `OWNER` |

## Target Outcomes

- Complete inventory of all PowerCenter objects (sources, targets, mappings, transformations, sessions, workflows) across all XML exports
- Data lineage documentation tracing source-to-target field mappings through each transformation chain
- Dependency graph showing which sessions/workflows depend on which mappings, and which mappings share sources or targets
- `POWERCENTER_ASSESSMENT.md` documenting the estate scope, complexity metrics (number of mappings, transformation types, source/target systems), and migration risk areas
- PR with all assessment artifacts

## What Participants Will Learn

- How Devin parses and understands vendor-specific XML metadata formats (Informatica PowerCenter `powrmart.dtd`)
- How Devin traces data lineage through transformation chains (Source → Expression → Filter → Lookup → Target)
- How Devin identifies cross-mapping dependencies and shared objects
- The importance of comprehensive estate assessment before starting a migration

## Devin Features Exercised

- Complex XML parsing and metadata extraction
- Data lineage analysis and documentation generation
- Dependency graph construction
- Large-file analysis (21 MB of XML across 11 exports)
- PR creation with structured assessment documentation

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## <a id="ts-informatica-powercenter"></a>ts-informatica-powercenter

**Repository:** [ts-informatica-powercenter](https://github.com/Cognition-Partner-Workshops/ts-informatica-powercenter)

Informatica PowerCenter 9.6.1 XML exports for a government HR data integration system (EHRP-to-BIIS). Contains 11 mapping exports (CPM, CPM_AFPS, CPM_CDC, CPM_NIH, CPM_OIG, LES, FDA_Leave, EHRP2BIIS_UPDATE, Pay_Calendar, Pseudossn, COMPTIME) in the `XML/` directory, plus Oracle SQL pre/post-load scripts and shell transfer workflows in `Transfer Scripts/`.

### Step 1: Paste into Devin

```
Analyze all PowerCenter XML exports in ts-informatica-powercenter/XML/. For each XML file, extract: (1) all SOURCE definitions with database, owner, and field details, (2) all TARGET definitions, (3) all MAPPING names with their transformation chains (list each transformation type and name in order), (4) all SESSION and WORKFLOW definitions. Produce a POWERCENTER_INVENTORY.md with a summary table of all objects by type, and a POWERCENTER_LINEAGE.md that documents the source-to-target data flow for each mapping. Also analyze the shell scripts in Transfer Scripts/ to document the orchestration pattern.
```

### Step 2: Research with Ask Devin

- *"What types of transformations are used across the PowerCenter XML exports in ts-informatica-powercenter? Which mappings are the most complex (most transformations, most source/target connections)?"*
- *"What Oracle schemas and tables appear as sources and targets across all the XML exports? Are there any shared tables that appear in multiple mappings?"*
- Use the analysis to identify which mappings are the highest-risk for migration (most complex, most dependencies)

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the overall structure. Cross-reference the XML exports with the shell transfer scripts and SQL scripts to build a complete picture of the data integration pipeline — not just the PowerCenter metadata but also the orchestration and pre/post-processing layers.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the inventory capture all object types? Are the lineage diagrams accurate for the transformation chains?
- **Leave a comment** asking Devin to add a migration complexity scoring matrix that rates each mapping by number of transformations, source count, and use of advanced features (Lookup, Joiner, Aggregator)
- **Watch Devin respond** and push a follow-up commit

### Key Takeaways

- **Non-invasive static analysis**: Devin extracts the full ETL estate from XML exports alone — no PowerCenter server connection, no repository database access, no runtime environment needed
- **Comprehensive lineage**: Transformation chains are traced from source fields through every intermediate step to target fields — this is the foundation for migration planning
- **Parallel assessment potential**: Each XML export (CPM, LES, FDA_Leave, etc.) can be analyzed independently, making this a candidate for [child-session parallelism](../../shared/general-themes/design-patterns-for-devin.md#pattern-3-divide-and-conquer-with-child-agents)
- **Migration readiness**: The assessment artifacts (inventory, lineage, complexity scores) are the inputs for scoping a migration project — effort estimation, risk identification, and sequencing

---

## Going Further

### Automation and Webhooks

Set up a CI trigger so that whenever new PowerCenter XML exports are added to the repository, a Devin session automatically updates the inventory and lineage documentation. See [Design Patterns → Event-Driven Triggers](../../shared/general-themes/design-patterns-for-devin.md#pattern-2-event-driven-triggers).

### Child Sessions for Scale

For large Informatica estates with dozens of mappings, use the [divide-and-conquer pattern](../../shared/general-themes/design-patterns-for-devin.md#pattern-3-divide-and-conquer-with-child-agents): a parent session inventories all XML exports, then spawns a child session per export. Each child extracts sources, targets, transformations, and lineage independently, opening its own PR.

### Scheduled Sessions

Schedule a recurring Devin session to re-analyze the PowerCenter estate and detect changes — useful during active migration projects where mappings are being modified. See [Platform Capabilities → Scheduled Sessions](../../shared/general-themes/platform-capabilities.md#scheduled-sessions).

### Shared Context Layer

Create organization-level [knowledge notes](../../shared/general-themes/architecture-strengths.md#shared-context-layer) documenting your Informatica assessment conventions (e.g., "Expression transformations with >10 output ports are high-complexity", "Mappings using Joiner + Aggregator require special attention during migration"). Every Devin session inherits these conventions.

### Team-Based Operation

Data engineers, ETL developers, and migration architects can all review the assessment PR simultaneously. Devin reads feedback from any reviewer and iterates — the assessment evolves collaboratively. See [Collaboration Model](../../shared/general-themes/collaboration-model.md).
