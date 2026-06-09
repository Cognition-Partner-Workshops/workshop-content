# Data Platform Migration with Devin — General Demo

Devin migrates legacy data estates — SAS, Sybase, Oracle, Teradata, Informatica,
and others — to modern platforms like Databricks, Snowflake, SQL Server, and
BigQuery with verifiable confidence. The universal pattern: **orient the estate,
convert one object live, verify parity through programmatic reconciliation, catch
and fix divergences, then fan out in parallel** using child sessions and a
reusable playbook.

## Table of Contents

- [The Pattern](#the-pattern)
- [The Verification Loop](#verification-loop)
- [Running It Live](#running-it-live)
- [Scaling](#scaling)
- [Key Takeaways](#key-takeaways)
- [Worked Examples](#worked-examples)

---

<a id="the-pattern"></a>
## The Pattern

Every data platform migration follows the same five-step arc, regardless of
source or target technology.

### 1. Orient

Devin reads the legacy estate and builds a map of objects, dependencies, and
constructs. For a SAS estate this means programs, macros, formats, and batch
orchestration; for a Sybase database it means stored procedures, views,
triggers, and functions. The output is a structured inventory that identifies
what needs conversion and flags platform-specific constructs (e.g., Sybase
outer-join syntax, SAS macro variables, Oracle PL/SQL packages).

### 2. Convert one object live

Pick a representative object — a SAS program, a stored procedure, a Teradata
BTEQ script — and have Devin convert it to the target platform. This first
conversion establishes the translation patterns and validates the toolchain
end to end: source parsing, target code generation, build, and deployment into
a namespaced environment.

### 3. Verification loop

Programmatic reconciliation proves parity between source and target.
Reconciliation controls check row counts, control totals, business-rule
invariants, and referential integrity. The key insight: **verification is
code, not manual inspection**. When reconciliation passes, trust is
quantitative.

### 4. Catch divergences

When reconciliation finds a mismatch — and it typically does on the first
pass — Devin investigates and fixes the root cause. This might be an outer
join converting to an inner join (row loss), a data type truncation, or a
locale-dependent sort order. The fix-and-reverify cycle runs until
reconciliation passes cleanly.

### 5. Fan out in parallel

With one object converted and the playbook proven, Devin spawns child sessions
to convert the remaining objects concurrently. Each child follows the same
playbook, operates on its own branch, and opens its own PR. The parent
monitors progress and handles failures.

---

<a id="verification-loop"></a>
## The Verification Loop

Verification is the core differentiator. Without it, migration is
a translation exercise with no confidence guarantee. With it, every converted
object carries a quantitative proof of parity.

**Reconciliation harness:** A Python/SQL framework that runs source-to-target
checks after each conversion. Controls are defined per-object (row count
match, column-sum parity, referential-integrity checks, business-rule
invariants) and execute against the namespaced target environment.

**Golden-file testing:** Where a live source runtime is unavailable, the legacy
code itself serves as the source of truth. Reconciliation validates against
control totals derived from the source code's logic and known-good seed data.

**Namespace isolation:** Every conversion run deploys into an isolated
namespace (`dev`, `test`, `ns_<session_id>`) so runs are safe to repeat and
safe to run concurrently. The before state (raw/seed data) is durable and
never overwritten; the after state is disposable.

---

<a id="running-it-live"></a>
## Running It Live

### Orient the legacy estate

Open the source repo and ask Devin to map it:

```
Using the <source-repo> repo, give me a map of the legacy estate:
tables, programs/procedures, views, macros/functions, and batch
orchestration. For each object, identify platform-specific constructs
that need conversion to <target-platform>. Output a structured
inventory as a Markdown table.
```

### Convert one object

Pick a representative object and kick off conversion:

```
Using the <target-repo> repo, convert <object-name> from
<source-platform> to <target-platform>. Follow the conversion
playbook. After conversion, run the reconciliation harness to
verify parity. If reconciliation fails, investigate and fix the
root cause, then re-run until it passes.
```

### Fan out across the estate

Once the first conversion is verified, scale to the full estate:

```
Using the <target-repo> repo and the conversion playbook, convert
the remaining <source-platform> objects to <target-platform>.
Use child sessions to parallelize — one object per session. Each
session should follow the playbook, run reconciliation, and
verify parity before completing.
```

---

<a id="scaling"></a>
## Scaling

**Playbooks as reusable conversion procedures:** The conversion methodology is
encoded in a Devin Playbook — a step-by-step procedure that every session
follows. One engineer writes the playbook; every subsequent conversion session
(human-triggered or child-spawned) applies the same proven process.

**Child sessions for parallelism:** The parent agent breaks the estate into
independent units (one program, one procedure, one table group) and spawns
a child session for each. Children work on their own VMs with their own
branches. Failures in one child do not affect others — the parent retries or
escalates as needed.

**Incremental progress:** Each child opens its own PR with reconciliation
results. The team reviews and merges independently. Progress is visible,
auditable, and resumable.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **One pattern, many platforms.** The orient → convert → verify → fan-out arc
  applies to SAS, Sybase, Oracle, Teradata, Informatica, COBOL, and other
  legacy stacks migrating to modern platforms.
- **Verification is code.** Programmatic reconciliation replaces manual
  inspection with quantitative proof of parity.
- **Namespace isolation makes it safe.** Every run deploys into a disposable
  namespace — safe to repeat, safe to run concurrently, safe to tear down.
- **Playbooks encode institutional methodology.** The conversion procedure is
  written once and reused across the estate — consistency at scale.
- **Child sessions unlock parallelism.** One agent becomes many, each
  following the same playbook, each opening its own verified PR.

---

<a id="worked-examples"></a>
## Worked Examples

For complete worked examples with repo-specific prompts, credentials setup, and
end-to-end runbooks:

- [SAS → dbt/Databricks](use-cases/sas-to-databricks-demo.md)
- [Sybase → SQL Server](use-cases/sybase-to-sqlserver-demo.md)

---

**Other variants:** [Desktop + Cloud](general.platform.md)
