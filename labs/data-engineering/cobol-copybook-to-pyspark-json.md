# COBOL Copybook to PySpark/JSON

## Table of Contents

- [Challenge](#challenge)
- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Starting Points](#starting-points)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [ts-cobol-carddemo](#ts-cobol-carddemo)
- [Going Further](#going-further)

---

## Challenge

Given COBOL copybooks (record layout definitions) and their corresponding fixed-width feed files, generate PySpark ingestion scripts and JSON schemas that accurately parse and load the mainframe data. The copybooks define field names, types (DISPLAY, COMP, COMP-3), offsets, and lengths — participants use Devin to translate these into modern data engineering artifacts without deep mainframe expertise.

This is a **non-invasive, static analysis** exercise: Devin reads the copybook definitions and feed files directly from the repository — no mainframe access, no runtime environment, and no changes to the source system are required.

## Quick Start

1. Open Devin and connect to the **ts-cobol-carddemo** repository
2. Paste the [Step 1 prompt](#step-1-paste-into-devin) into Devin
3. Watch Devin parse the COBOL copybook, generate PySpark schemas, and open a PR with ingestion scripts

## Repositories

- [ts-cobol-carddemo](#ts-cobol-carddemo)

---

## Starting Points

The repo contains several copybook → feed file pairs of varying complexity:

| Copybook | Feed File | Record Size | Fields | Complexity | Good For |
|----------|-----------|-------------|--------|------------|----------|
| `CVACT01Y.cpy` | `acctdata.txt` | 300B | Account ID, balances (S9V99), dates, zip, FILLER | Medium | First attempt — signed numerics + FILLER handling |
| `CUSTREC.cpy` | `custdata.txt` | 500B | Customer PII, addresses, SSN, FICO score | Medium | Largest record — many text + numeric fields |
| `CVACT02Y.cpy` | `carddata.txt` | 150B | Card number, CVV, embossed name, expiry, status | Low-Medium | Quick win — simple types |
| `CVACT03Y.cpy` | `cardxref.txt` | 50B | Card ↔ Customer ↔ Account linkage | Low | Smallest record — good warm-up |
| `CVTRA01Y.cpy` | `tcatbal.txt` | 50B | Nested group key (ACCT-ID + TYPE-CD + CD), signed balance | Medium | Nested COBOL groups → struct types |

Copybooks are in `app/cpy/`, feed files in `app/data/ASCII/`.

## Target Outcomes

- PySpark ingestion script(s) that read fixed-width feed files using copybook-derived schemas
- JSON schema files describing record layouts (field names, types, offsets, lengths, scale)
- Validation output comparing parsed PySpark DataFrame row counts and sample values against the raw feed file
- PR with generated configs, ingestion code, and a `COPYBOOK_PARSING_NOTES.md` documenting type-mapping decisions (e.g., COMP-3 → DecimalType, PIC X → StringType)

## What Participants Will Learn

- How Devin reads COBOL copybooks and extracts field-level metadata (PIC clauses, levels, FILLER)
- How Devin maps COBOL data types to PySpark types (PIC 9 → IntegerType, PIC S9V99 → DecimalType, PIC X → StringType)
- How Devin generates fixed-width parsing logic from byte offsets
- How Devin handles COBOL-specific constructs (signed numerics, FILLER padding, nested group levels)
- The value of automated schema generation for mainframe data migration

## Devin Features Exercised

- Domain-specific code generation (COBOL → PySpark/JSON)
- Schema inference from legacy data definitions
- Fixed-width file parsing logic generation
- PR creation with technical documentation
- DeepWiki for codebase exploration (understanding copybook → program relationships)
- Ask Devin for pre-session research on COBOL data types

## Difficulty

Intermediate

## Estimated Time

45 minutes

---

## <a id="ts-cobol-carddemo"></a>ts-cobol-carddemo

**Repository:** [ts-cobol-carddemo](https://github.com/Cognition-Partner-Workshops/ts-cobol-carddemo)

AWS CardDemo — a simulated mainframe credit card management system (Apache 2.0). Contains 62 COBOL copybooks defining record layouts, 9 ASCII fixed-width feed files, 30+ COBOL batch programs, and JCL job definitions. The copybook → feed file pairs provide clean raw material for PySpark schema and ingestion code generation.

### Step 1: Paste into Devin

```
Analyze the COBOL copybook app/cpy/CVACT01Y.cpy in ts-cobol-carddemo. This defines the ACCOUNT-RECORD layout (300 bytes) with fields like ACCT-ID (PIC 9(11)), ACCT-CURR-BAL (PIC S9(10)V99), dates (PIC X(10)), and FILLER. Generate: (1) a PySpark script that reads app/data/ASCII/acctdata.txt as a fixed-width file using the copybook-derived schema, (2) a JSON schema file describing each field's name, COBOL PIC clause, PySpark type, byte offset, and length. Validate by printing the DataFrame schema and first 5 rows. Then repeat for CUSTREC.cpy → custdata.txt and CVACT02Y.cpy → carddata.txt. Include a COPYBOOK_PARSING_NOTES.md documenting your type-mapping decisions.
```

### Step 2: Research with Ask Devin

- *"What COBOL data types are used across the copybooks in ts-cobol-carddemo/app/cpy/? Which copybooks use signed numerics (S9), implied decimals (V99), or COMP/COMP-3 packed fields?"*
- *"Which copybooks in ts-cobol-carddemo have corresponding feed files in app/data/ASCII/? Map each copybook to its feed file and record length."*
- Use the analysis to identify which copybook → feed file pairs to prioritize

### Step 3 (Optional): Read the DeepWiki

Open the DeepWiki page for ts-cobol-carddemo to understand the application domain (credit card management), how copybooks relate to COBOL programs, and which batch programs read which feed files. This context helps validate whether the generated PySpark schemas capture the right business semantics.

### Step 4 (Optional): Review and Give Feedback

- **Review the diff** — do the PySpark schemas correctly map COBOL PIC clauses to appropriate Spark types? Are byte offsets calculated correctly accounting for FILLER?
- **Leave a comment** asking Devin to add a second ingestion variant that reads the EBCDIC files in `app/data/EBCDIC/` with proper encoding conversion
- **Try extending** the exercise by asking Devin to generate a unified PySpark pipeline that reads all 5 feed files and joins them on ACCT-ID to produce a denormalized customer-account-card DataFrame

### Key Takeaways

- **Non-invasive analysis**: Devin reads copybook definitions and feed files directly — no mainframe runtime, no JCL execution, no EBCDIC conversion needed for the core exercise
- **Construct-level mapping**: Every COBOL PIC clause type (DISPLAY, COMP, COMP-3, signed, implied decimal) maps to a documented PySpark equivalent
- **Automated schema generation**: Manual copybook-to-schema translation is error-prone and tedious — Devin handles byte-offset arithmetic and type mapping systematically
- **Parallel migration potential**: With multiple copybook → feed file pairs, a [child-session pattern](../../shared/general-themes/design-patterns-for-devin.md#pattern-3-divide-and-conquer-with-child-agents) can process them all in parallel — one Devin session per copybook

---

## Going Further

### Automation and Webhooks

Set up a CI trigger so that whenever new copybook files are added to the repository, a Devin session automatically generates the corresponding PySpark ingestion scripts and JSON schemas. See [Design Patterns → Event-Driven Triggers](../../shared/general-themes/design-patterns-for-devin.md#pattern-2-event-driven-triggers).

### Child Sessions for Scale

For estates with dozens of copybook → feed file pairs, use the [divide-and-conquer pattern](../../shared/general-themes/design-patterns-for-devin.md#pattern-3-divide-and-conquer-with-child-agents): a parent session inventories all copybooks, then spawns a child session per copybook. Each child generates its PySpark script and JSON schema independently, opening its own PR.

### Scheduled Sessions

Schedule a weekly Devin session to re-validate generated schemas against the latest feed files — catching drift if record layouts change upstream. See [Platform Capabilities → Scheduled Sessions](../../shared/general-themes/platform-capabilities.md#scheduled-sessions).

### Shared Context Layer

Create organization-level [knowledge notes](../../shared/general-themes/architecture-strengths.md#shared-context-layer) documenting your COBOL-to-PySpark type-mapping conventions (e.g., "COMP-3 fields always map to DecimalType with explicit precision"). Every future Devin session inherits these conventions automatically — no re-explaining required.

### Team-Based Operation

Multiple team members can review the generated PySpark schemas simultaneously via PR comments. Devin reads feedback from any reviewer and iterates. See [Collaboration Model → Multi-User Communication](../../shared/general-themes/collaboration-model.md#multi-user-communication).
