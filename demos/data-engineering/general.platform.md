# Data Platform Migration with Devin — General Demo (Desktop + Cloud)

Devin migrates legacy data estates — SAS, Sybase, Oracle, Teradata, Informatica,
and others — to modern platforms like Databricks, Snowflake, SQL Server, and
BigQuery with verifiable confidence. This variant uses **Devin Desktop** as the
primary interface: explore the legacy estate locally, delegate conversion tasks
to Cloud, monitor parallel sessions in the Agent Command Center, and review
migration PRs with one-click checkout.

## Table of Contents

- [The Pattern (Desktop + Cloud)](#the-pattern)
- [The Verification Loop](#verification-loop)
- [Running It Live](#running-it-live)
- [Scaling with Agent Command Center](#scaling)
- [Key Takeaways](#key-takeaways)
- [Worked Examples](#worked-examples)

---

<a id="the-pattern"></a>
## The Pattern (Desktop + Cloud)

The migration arc is the same five steps — orient, convert, verify, catch
divergences, fan out — but Desktop changes *where* and *how* each step runs.

### 1. Orient (Desktop)

Use Devin Desktop to explore the legacy estate in your IDE. Open the source repo
in a Space, then ask Devin to map the objects, dependencies, and
platform-specific constructs. The structured inventory appears inline alongside
the source files — no context switching to a browser tab.

### 2. Convert one object (delegate to Cloud)

From Desktop, delegate the first conversion to a Cloud session. Devin spins up
a VM with the target platform's toolchain, runs the conversion, deploys into
a namespaced environment, and executes the reconciliation harness — all while
you continue working locally. Desktop shows the session status in the Agent
Command Center's Kanban board.

### 3. Verification loop (Cloud, monitored from Desktop)

Reconciliation controls run on the Cloud VM against the namespaced target
environment. Row counts, control totals, business-rule invariants, and
referential integrity checks execute automatically. Results stream back to
Desktop — pass/fail status is visible in the Agent Command Center without
opening the Cloud session.

### 4. Catch divergences (Cloud + Desktop review)

When reconciliation finds a mismatch, the Cloud session investigates and
proposes a fix. Use one-click PR checkout in Desktop to pull the fix branch
into your IDE, inspect the diff alongside the source code, and approve or
request changes — all without leaving your editor.

### 5. Fan out in parallel (Cloud child sessions, Desktop monitoring)

From Desktop, delegate the remaining conversions to Cloud. Devin spawns child
sessions — one per object — each following the same playbook. The Agent
Command Center shows every session's progress on a single Kanban board:
queued, running, awaiting review, merged.

---

<a id="verification-loop"></a>
## The Verification Loop

Verification runs identically to the Cloud-only variant — the reconciliation
harness, golden-file testing, and namespace isolation are infrastructure
concerns that execute on the Cloud VM regardless of the triggering interface.

What Desktop adds is **visibility without context switching**: reconciliation
results appear in the Agent Command Center, and failing checks link directly
to the relevant PR diff in your IDE.

---

<a id="running-it-live"></a>
## Running It Live

### Orient the legacy estate (Desktop)

Open the source repo in a Devin Desktop Space and explore the estate:

```
Using the <source-repo> repo, give me a map of the legacy estate:
tables, programs/procedures, views, macros/functions, and batch
orchestration. For each object, identify platform-specific constructs
that need conversion to <target-platform>. Output a structured
inventory as a Markdown table.
```

The inventory renders inline in your IDE. Pin it to the Space for reference
during the rest of the migration.

### Convert one object (delegate to Cloud)

Select a representative object and delegate conversion to a Cloud session:

```
Using the <target-repo> repo, convert <object-name> from
<source-platform> to <target-platform>. Follow the conversion
playbook. After conversion, run the reconciliation harness to
verify parity. If reconciliation fails, investigate and fix the
root cause, then re-run until it passes.
```

Monitor the session in the Agent Command Center. When the PR opens, use
one-click checkout to review the converted code in your IDE alongside the
original source.

### Fan out across the estate (delegate to Cloud)

Once the first conversion is verified, scale to the full estate from Desktop:

```
Using the <target-repo> repo and the conversion playbook, convert
the remaining <source-platform> objects to <target-platform>.
Use child sessions to parallelize — one object per session. Each
session should follow the playbook, run reconciliation, and
verify parity before completing.
```

The Agent Command Center Kanban board shows every child session's status.
Review PRs as they complete using one-click checkout — each PR includes
reconciliation results so you can verify parity before merging.

---

<a id="scaling"></a>
## Scaling with Agent Command Center

**Kanban visibility:** The Agent Command Center displays every active session —
parent and children — on a single board. Cards move through stages (queued,
running, awaiting review, merged) so the migration's overall progress is
visible at a glance.

**Spaces for organization:** Create a Devin Desktop Space for the migration
workstream. Pin the source repo, target repo, estate inventory, and playbook.
Every team member working on the migration shares the same organized view.

**One-click PR checkout:** When a child session opens a PR, click once in
Desktop to check out the branch in your IDE. Review the converted code, run
local tests, and approve — all without leaving the editor.

**Playbooks as reusable conversion procedures:** The conversion methodology is
encoded in a Devin Playbook. One engineer writes the playbook; every subsequent
Cloud session — delegated from Desktop or triggered by a child agent — applies
the same proven process.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **Desktop as mission control.** Explore legacy code locally, delegate
  conversions to Cloud, and monitor everything from the Agent Command Center.
- **One pattern, many platforms.** The orient → convert → verify → fan-out arc
  applies regardless of source or target technology.
- **Verification is code.** Programmatic reconciliation runs on Cloud VMs;
  results stream back to Desktop for review.
- **Agent Command Center for parallel visibility.** Track dozens of concurrent
  child sessions on a single Kanban board.
- **One-click PR checkout bridges Cloud and IDE.** Review migration PRs in your
  editor alongside the original source code.
- **Spaces organize the workstream.** Pin repos, inventories, and playbooks in
  a shared workspace the whole team can use.

---

<a id="worked-examples"></a>
## Worked Examples

For complete worked examples with repo-specific prompts, credentials setup, and
end-to-end runbooks:

- [SAS → dbt/Databricks](use-cases/sas-to-databricks-demo.md)
- [Sybase → SQL Server](use-cases/sybase-to-sqlserver-demo.md)

---

**Other variants:** [Cloud only](general.md)
