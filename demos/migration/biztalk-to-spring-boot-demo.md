# BizTalk → Spring Boot — Integration Map Migration Demo

A single linear demo that shows Devin migrating Microsoft BizTalk Server maps
to verified Spring Boot 3.x transforms: orient over an unfamiliar BizTalk
estate, migrate one map live, prove parity against the output BizTalk itself
recorded, catch a real divergence and fix it, then fan the remaining verifiable
maps out in parallel. Each migration lands as a PR with a green build and a
green parity gate.

The prompts below invoke the `!migrate-biztalk-map` Devin Playbook — the
reusable migration procedure — whose source lives in the code repo at
[`uc-integration-migration-biztalk-to-spring-boot/.workshop/playbooks/migrate-biztalk-map-to-spring-boot.devin.md`](https://github.com/Cognition-Partner-Workshops/uc-integration-migration-biztalk-to-spring-boot/blob/main/.workshop/playbooks/migrate-biztalk-map-to-spring-boot.devin.md).
The repo-specific build and parity mechanics come from that repo's Skill
(`.agents/skills/biztalk-to-spring-boot-migration/SKILL.md`).

## Table of Contents

- [Quick Start](#quick-start)
- [Repository](#repository)
- [Before, After, and the Verification Loop](#before-after)
- [The Migration Arc](#migration-arc)
- [Part 1 — Devin Does the Migration](#part-1)
  - [Act 1 — Orient over the BizTalk estate](#act-1)
  - [Act 2 — Migrate one map live, with verification](#act-2)
  - [Act 3 — Fan out in parallel](#act-3)
  - [Act 4 — Confidence = programmatic verification](#act-4)
- [Part 2 — Run the Produced Artifact](#part-2)
- [Confirming Completion in Spring Boot](#confirm-spring-boot)
- [Concurrent Runs](#concurrent)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

From the repo root:

```bash
python3 tools/parity/inventory.py                 # rebuild the map → fixture inventory
python3 tools/parity/parity.py list               # the 6 maps with a recorded input + BizTalk output
cd spring-boot-app && ./mvnw -q verify && cd ..   # build the (empty) Spring Boot target
python3 tools/parity/parity.py run --all          # parity gate over every migrated map
```

Prerequisites: Java 17, Maven (wrapper included), Python 3.

---

<a id="repository"></a>
## Repository

- [uc-integration-migration-biztalk-to-spring-boot](https://github.com/Cognition-Partner-Workshops/uc-integration-migration-biztalk-to-spring-boot) — one repo holds both sides. The BizTalk estate (60 samples: 169 `.btm` maps, 211 `.xsd` schemas, 34 `.odx` orchestrations, 10 `.btp` pipelines, C# functoids, recorded sample messages) is the read-only "before". `spring-boot-app/` is the Spring Boot 3.5 target, `tools/parity/` is the verification harness, and `.workshop/playbooks/` + `.agents/skills/` hold the playbook source and Skill.

---

<a id="before-after"></a>
## Before, After, and the Verification Loop

| | Code | Evidence |
|---|---|---|
| **Before** | `main`: the BizTalk estate, `spring-boot-app/` with the `MapTransform` interface, `XsltMapTransform` base class, `MapRegistry`, and a `--map=<Name> <input.xml>` CLI — but **no maps migrated**; `tools/parity/` harness; playbook source; Skill | `tools/parity/fixtures.json`: 169 maps, of which 6 are `paired` — a recorded input instance and the `*_output.xml` BizTalk produced |
| **After** | a PR branch per migration adding a `MapTransform` bean (XSLT carried over via Saxon, or a Java rewrite), a JUnit test, and a runner entry in `tools/parity/runners.json` — built live by Devin | `parity.py run <map-id>` prints `PASS`: migrated output equals BizTalk's recording in canonical XML |

The **before** state is deliberately an empty target: the Spring Boot app
builds and starts, `--map=MapPerson` answers `unknown map 'MapPerson';
registered: []`. What Devin does **live** is read the `.btm` (a UTF-16 XML
document of links, functoids and inline XSLT), the XSDs it references, and
the recorded messages, then produce a transform that the gate accepts.

> **On "parity":** BizTalk only runs on Windows with the BizTalk SDK, so there
> is no live BizTalk in this environment. Parity means the migrated transform,
> fed the exact input instance the original developer tested the map with
> (`<TestMapInputInstanceFilename>` in the `.btproj.user`), produces the exact
> document BizTalk produced (the `*_output.xml` next to it), compared as
> canonical XML. Maps without that recorded evidence are listed as
> `input-only` / `unresolved` and are a human task: someone has to supply a
> golden before they can be verified.

---

<a id="migration-arc"></a>
## The Migration Arc

BizTalk is not just code: much of an integration's behavior lives in XML and
visual artifacts. A BizTalk flow

`Receive Location → Pipeline → Map → Orchestration → Send Port`

becomes, roughly,

`Adapter/Listener → Parser + Validation → Mapper → Service/Workflow → Adapter/Client`

in Spring Boot. Devin is given the whole solution (`.btproj`, `.odx`, `.btm`,
`.xsd`, `.btp`, C# functoids, sample messages) rather than "rewrite BizTalk in
Java", and the work is done one flow at a time. The arc that makes this a good
agentic problem — *reverse engineer behavior → discover dependencies →
reconstruct architecture → implement → test equivalence* — is the arc of this
demo:

| Migration step | Where it happens in this demo |
|---|---|
| **1. Inventory first** — a dependency graph of ports, pipelines, schemas, maps, orchestrations, helper code | Act 1: Devin explains the estate and runs `tools/parity/inventory.py`, which splits the 169 maps into verifiable and not |
| **2. Describe behavior before generating code** — semantic migration, not literal translation | Act 2: the `!migrate-biztalk-map` playbook has Devin read the recorded input/output (`parity.py show`), the `.btm` links and functoids, and the XSDs, and record a strategy (XSLT carry-over vs. Java rewrite) before touching code |
| **3. Define the Spring target architecture** — do not force a 1:1 Spring equivalent for every BizTalk abstraction | `main`: `spring-boot-app/` fixes the target — a `MapTransform` interface, Saxon-backed `XsltMapTransform`, a `MapRegistry`, and a CLI. A `.btm` becomes a Spring bean, not a translated `.btm` |
| **4. Implement incrementally** — models, transforms, adapters, logic, tests | Acts 2 and 3: one map per branch and PR, each with a JUnit/XMLUnit test and a runner entry; five in parallel from one orchestrator |
| **5. Use the old system as the oracle** — identical messages in, compare outputs, build the comparison into regression tests | Act 4 and Part 2: the parity gate replays the exact input the BizTalk developer tested with and diffs against BizTalk's recorded output as canonical XML |

Scope of this demo: it exercises the **Map** stage of the arc (`.btm` →
`MapTransform`), where the recorded Test Map evidence gives a hard oracle.
Pipelines, orchestrations (`.odx`), ports, and adapters are inventoried in
Act 1 but not migrated here, and the oracle is BizTalk's recorded output
rather than a live BizTalk side by side — there is no BizTalk runtime on
Linux. Those are the pieces where a real engagement depends on the customer's
environment: bindings, external systems (SQL Server, MQ, SFTP, SOAP), and
representative messages for the flows that have no recording.

---

<a id="part-1"></a>
## Part 1 — Devin Does the Migration

<a id="act-1"></a>
### Act 1 — Orient over the BizTalk estate

Ask Devin to explain an estate nobody in the room has worked with. This is
the **inventory** step of the arc: before any code, Devin builds the picture of
what exists, what depends on what, and which artifacts carry behavior in
configuration and visual designers rather than in code.

```
Using the uc-integration-migration-biztalk-to-spring-boot repo, give me a map of the BizTalk estate for someone who has never used BizTalk: what a .btm map, .xsd schema, .odx orchestration, .btp pipeline, and .btproj.user file each are, how a map compiles to XSLT and where inline XSLT/C# functoids live inside the .btm, and which artifacts are deterministic enough to translate mechanically versus which need a human decision. Then run python3 tools/parity/inventory.py and python3 tools/parity/parity.py list and explain, from tools/parity/fixtures.json, why only some of the 169 maps can be verified against BizTalk's own recorded output and what a human would have to supply for the rest.
```

Expected: a tour of the estate — maps as visual XSLT with `<Links>` (field
lineage) and `<Functoids>` (inline XSLT / C#), schemas as plain XSD with
BizTalk annotations, orchestrations as XLANG/s workflows, pipelines as staged
codecs — plus the verifiability split: 6 maps `paired` (input + BizTalk output
recorded), 46 `input-only`, 116 with no Test Map evidence at all. Devin
identifies the paired maps as the ones that can be migrated with proof, and
the rest as needing goldens from the team. The inventory also sets the
boundary of the autonomous work: maps with an oracle are Devin's; `.odx`
orchestrations, pipelines, and ports need bindings, external systems, and
sample traffic from the team before they can be verified.

<a id="act-2"></a>
### Act 2 — Migrate one map live, with verification

The core beat. Paste the playbook prompt for `MapPerson`. Devin reads the map
and its XSDs, writes the Spring Boot transform, builds, runs the parity gate,
catches a divergence, fixes it, and produces a PR with the `PASS` evidence.

The prompt does not say "convert `MapPerson.btm` to Java". It names the map,
the oracle (the recorded BizTalk output), and the gate, and the playbook makes
Devin **describe behavior before generating code**: read the recorded
input/output pair, read the `.btm` links and functoids and the XSDs they
reference, then choose and record a strategy — XSLT carried over via Saxon
when the map is links plus XSLT functoids, a Java rewrite when it depends on
C# scripting functoids. The intent of the map is implemented as an ordinary
Spring bean; the `.btm` is not mechanically converted.

```
!migrate-biztalk-map Migrate the BizTalk map working-with-maps/grouping-pattern-selecting-distinct-nodes/Sample3.MapPerson (Working-with-Maps/Grouping-Pattern-Selecting-distinct-nodes/SelectDistinctValues/Sample3/MapPerson.btm) into spring-boot-app/ in uc-integration-migration-biztalk-to-spring-boot. Namespace: migration/map-person. Treat the recorded BizTalk output in tools/parity/fixtures.json as the source of truth, run the parity gate with tools/parity/parity.py, and keep iterating until it prints PASS.
```

**The verification beat (the real divergence).** `MapPerson` groups `Person`
records by `Name`. A plausible reading of the map emits one `Nationality` and
the first `Email` per distinct name. BizTalk's recorded output for `Person1`
has **two** `Nationality` elements and the **last** `Email`, because the map's
`NationalityTemplate` iterates every match while `EmailTemplate` keeps
`position()=last()`. The gate catches it:

```
FAIL working-with-maps/grouping-pattern-selecting-distinct-nodes/Sample3.MapPerson: output differs from .../Msg/MapPerson_output.xml
  --- biztalk-expected
  +++ migrated-actual
     <Person>
       <Name>Person1</Name>
       <Nationality>Portuguese</Nationality>
  -    <Nationality>English</Nationality>
  -    <Email>email2@email</Email>
  +    <Email>email1@email</Email>
```

Carry BizTalk's XSLT semantics over exactly, flag it with a `// BizTalk parity:`
note, re-run:

```bash
python3 tools/parity/parity.py run working-with-maps/grouping-pattern-selecting-distinct-nodes/Sample3.MapPerson -- java -jar spring-boot-app/target/biztalk-migration-0.1.0-SNAPSHOT.jar --map=MapPerson {input}
# PASS working-with-maps/grouping-pattern-selecting-distinct-nodes/Sample3.MapPerson
```

The point: "compiles and the Java reads cleanly" review would have shipped a
transform that drops data the downstream system receives today; the recorded
BizTalk output caught it. The full write-up is in the playbook → *Worked
example*.

<a id="act-3"></a>
### Act 3 — Fan out in parallel

The other five verifiable maps are independent, so launch a session per map.
Each follows the same playbook and produces its own PR with a green gate.
This is **implement incrementally** at the level of the whole estate: one
flow per session, one PR per flow, each carrying its own regression test, so
the BizTalk implementation stays in place until every flow has been proven.

| Session | Map id (`tools/parity/fixtures.json`) | BizTalk pattern |
|---|---|---|
| 1 | `working-with-maps/grouping-pattern-selecting-distinct-nodes/Sample1.MapListParteners` | Grouping — distinct nodes |
| 2 | `working-with-maps/grouping-pattern-selecting-distinct-nodes/Sample2.DataMap` | Grouping — distinct nodes |
| 3 | `working-with-maps/looping-pattern/X1EDISample.Maps.Enrollment_to_5010_834` | Looping — EDI 834 enrollment |
| 4 | `working-with-maps/muenchian-grouping-and-sorting-without-losing-map-functionalities/Sample1.MapOrderUsingCount` | Muenchian grouping |
| 5 | `working-with-maps/name-value-transformation-pattern-hierarchical-schema-to-name-value-pair/Solution3.NameValueSolution3` | Hierarchical → name/value |

Each session uses its own namespace branch (`migration/map-list-parteners`,
`migration/data-map`, …) so the parallel builds never collide.

#### Parallelize from a single session (parent → child)

Run one **orchestrator** session that spawns a child per map and monitors
them. Paste:

```
Act as the orchestrator for a BizTalk-to-Spring-Boot map migration in Cognition-Partner-Workshops/uc-integration-migration-biztalk-to-spring-boot, using child Devin sessions to parallelize the work. Spawn one child Devin session per map below; give each child the repo, its own namespace branch (migration/child1, child2, ...), and tell it to follow the !migrate-biztalk-map playbook: treat the recorded BizTalk output in tools/parity/fixtures.json as the source of truth, reproduce it exactly, register its runner in tools/parity/runners.json, and iterate until python3 tools/parity/parity.py run <map-id> prints PASS. Maps: (1) working-with-maps/grouping-pattern-selecting-distinct-nodes/Sample1.MapListParteners (2) working-with-maps/grouping-pattern-selecting-distinct-nodes/Sample2.DataMap (3) working-with-maps/looping-pattern/X1EDISample.Maps.Enrollment_to_5010_834 (4) working-with-maps/muenchian-grouping-and-sorting-without-losing-map-functionalities/Sample1.MapOrderUsingCount (5) working-with-maps/name-value-transformation-pattern-hierarchical-schema-to-name-value-pair/Solution3.NameValueSolution3. After launching, monitor the child sessions until each map has a PR with a green build and a PASS from the parity gate. Summarize the results and call out any BizTalk-faithful quirks the children had to reproduce (repeated elements, dropped fields, ordering) rather than "fix".
```

The children write to their own namespace branches; this is the same verified
migration loop as a single session — run five times at once, from one parent.

**What the fan-out typically surfaces.** Not every paired map passes, and that
is the point of the gate. In a recorded run, four of the five children reached
`PASS` (two as XSLT carry-over, two as Java rewrites of C# scripting functoids,
one of which pins the `DateTime.UtcNow` wall-clock the original Test Map run
captured), while `MapListParteners` **failed on exactly one line**: the
BizTalk-recorded output contains `Real Madrid`, a value that appears nowhere in
the recorded input (which has `DemoCompany2`, as the sample's own README
documents). No faithful transform can pass that fixture. The child kept the
map-faithful XSLT, pinned both facts in tests, left the fixture untouched, and
handed the decision back: re-record the golden on BizTalk, or accept the input
as authoritative. Bad evidence is caught the same way a bad migration is.

<a id="act-4"></a>
### Act 4 — Confidence = programmatic verification

**Use the old system as the oracle.** The same representative message goes
into the migrated transform that went into BizTalk, and the outputs are
compared — and that comparison is checked in as a regression test, not run
once by hand. The gates that make every PR trustworthy:

- **Build** (`./mvnw -q verify` in `spring-boot-app/`): compiles, runs the
  JUnit/XMLUnit fixture tests, confirms the Spring context starts.
- **Parity gate** (`python3 tools/parity/parity.py run <map-id>` / `--all`):
  the migrated transform's output versus BizTalk's own recorded output, as
  canonical XML — element order, namespaces, repeated elements, and text all
  significant. `PASS` or a unified diff.
- **Devin Review**: an automated reviewer on every PR.

A migration is "done" when the parity gate prints `PASS` on the PR branch — not
when the XSLT looks right.

---

<a id="part-2"></a>
## Part 2 — Run the Produced Artifact

Check out a migration branch and run the migrated map against the recorded
BizTalk input:

```bash
git fetch origin && git checkout migration/map-person
cd spring-boot-app && ./mvnw -q verify && cd ..

# Run the migrated map from the CLI
java -jar spring-boot-app/target/biztalk-migration-0.1.0-SNAPSHOT.jar --map=MapPerson \
  Working-with-Maps/Grouping-Pattern-Selecting-distinct-nodes/Msg/InputPersons.xml

# Compare with what BizTalk produced
python3 tools/parity/parity.py show working-with-maps/grouping-pattern-selecting-distinct-nodes/Sample3.MapPerson
python3 tools/parity/parity.py run --all
```

---

<a id="confirm-spring-boot"></a>
## Confirming Completion in Spring Boot

The milestone is complete when four things are true: the Spring Boot target
**builds**, the **parity gate is green**, the migrated output **matches
BizTalk's recording** including its quirks, and the **before is untouched**.

**1. The app builds.** `./mvnw -q verify` in `spring-boot-app/` exits green on
the migration branch.

**2. The parity beat — the quirk is reproduced.** `--map=MapPerson` on the
recorded input emits two `Nationality` elements and `email2@email` for
`Person1` — exactly what `Msg/MapPerson_output.xml` holds. This is the
"verification caught a wrong migration" proof, visible as data.

**3. The gate — every registered map PASS.** `python3 tools/parity/parity.py
run --all` prints one `PASS` per map in `tools/parity/runners.json`.

**4. The before is untouched.** `main` still has an empty `MapRegistry`
(`registered: []`), `runners.json` is `{}`, and nothing under `Working-with-*/`
changed. Migrated code lives on `migration/*` branches, ready for review.

---

<a id="concurrent"></a>
## Concurrent Runs

Each migration targets its own namespace branch, so multiple runs — and the
fan-out in Act 3 — coexist with no collisions:

```bash
git checkout -b migration/alice    # run 1
git checkout -b migration/bob      # run 2
```

Fixtures are read-only and the CLI is stateless, so concurrent builds and gate
runs do not conflict.

---

<a id="key-takeaways"></a>
## Key Takeaways

- The value on display is **Devin doing the migration**: reading an unfamiliar BizTalk estate (UTF-16 `.btm` maps, functoid XSLT, XSD schemas), migrating maps off a reusable playbook, and proving each one against BizTalk's own recorded output — not a finished artifact to run.
- **Confidence comes from programmatic verification.** The parity gate compares canonical XML against evidence BizTalk produced, and the demo shows a real divergence (grouped nationalities and last-wins email) being caught and fixed. "Looks right" review would have missed it.
- **The recorded BizTalk output is the source of truth**: migrations reproduce it faithfully, quirks flagged rather than silently "improved"; redesigning the message is a separate, deliberate decision.
- **The harness also tells you what cannot be verified.** 6 of 169 maps have recorded evidence; the inventory names the other 163 so the human work (supplying goldens, deciding orchestration/pipeline strategy) is explicit rather than discovered mid-migration.
- Migrations are **independent and parallelizable** — one session per map, or one orchestrator fanning out children, each producing its own gated PR. Namespace branches and an untouched `main` make the story safe to repeat.
- **The hard part is not C# to Java.** It is reverse-engineering behavior from XML and visual artifacts, discovering dependencies, fitting the result to a Spring architecture, and proving equivalence — work that needs an agent that can roam the repository, run builds and gates, and iterate on failures, not a code converter.
- **Where Devin runs autonomously and where it is blocked** is visible in the inventory: maps with recorded evidence are migrated and proven end to end; orchestrations, pipelines, ports, and unrecorded maps wait on the team for bindings, external systems, and sample traffic.
