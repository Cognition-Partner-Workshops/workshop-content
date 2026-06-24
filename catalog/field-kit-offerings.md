# Field Kit Offerings Map

Context and a cataloging plan for the **discrete offerings** in this repo, so [Field Kit](https://github.com/Cognition-Partner-Workshops/field-kit) can ingest them as Packets.

This is the bridge document: it names what `workshop-content` offers, maps each offering to Field Kit's [Packet schema](https://github.com/Cognition-Partner-Workshops/field-kit/blob/main/data/schema.json), and specifies how Field Kit can keep each Packet's contents in sync automatically.

- **Source repo:** `Cognition-Partner-Workshops/workshop-content`
- **Raw base:** `https://raw.githubusercontent.com/Cognition-Partner-Workshops/workshop-content/main/`
- **Tree base:** `https://github.com/Cognition-Partner-Workshops/workshop-content/tree/main/`

---

## The Seven Offering Types

This repo splits content by **how it is consumed**. Each type is a distinct kind of Field Kit Packet.

| # | Offering | What it is | Count | Suggested `packetType` | `audience` | `technicalLevel` |
|---|----------|-----------|-------|------------------------|-----------|------------------|
| 1 | **Courses** | Reading-first enablement, sequential by role (no coding) | 4 (Foundations + 3 tracks) | `Course` *(new)* | Enablement, role-specific | 100–200 |
| 2 | **Workshops** | Hands-on, choose-your-own-adventure lab collections (no solutions/assessments) | 16 | `Hands On Lab` (collection) | Engineering, Systems Integration | 200–300 |
| 3 | **Labs** | Individual hands-on lab modules — the building blocks workshops compose | 85 (13 disciplines) | `Hands On Lab` | Engineering | 100–400 |
| 4 | **Demos** | Facilitator-led linear showcases, persona-targeted | 11 (3 categories) | `Demo` | Sales, Solutions | 200 |
| 5 | **Reference** | Shared lookup cards on how Devin works + runtime run instructions | 7 cards + 1 | `Solution Brief` / `Toolkit` | All | 100–200 |
| 6 | **Catalog** | Machine-readable repo inventory + upstream provenance | 1 (15 clusters) | `Toolkit` | Enablement, Engineering | n/a |
| 7 | **Events** | Non-reusable, network/laptop-specific event instances | varies | *(do not catalog — internal)* | — | — |

> **Schema gap:** Field Kit's `packetType` examples don't include a reading-course type. Add `"Course"` to the `packetType` examples and create an issue template for it, or catalog Courses as `Reference Architecture`/`Solution Brief` in the interim.

---

## How Field Kit Should Sync Each Offering

Field Kit Packets support `contentGroups` that fetch a raw README and parse it (`parser: markdown-table` or `directory-list`). The index READMEs in this repo are built to be parsed that way — point each Packet's content group at the raw README and Field Kit stays current without manual re-entry.

| Packet | `sourceUrl` (raw README) | `parser` | Notes |
|--------|--------------------------|----------|-------|
| Courses index | `courses/README.md` | `markdown-table` | Level 1 + Level 2 tables (course, focus, duration) |
| Labs catalog | `labs/README.md` | `markdown-table` | "All Modules at a Glance" tables include difficulty, time, repos |
| Workshops index | `workshops/README.md` | `markdown-table` | Start Here + By Technical Domain + By Technical Role tables |
| Repo inventory | `catalog/repos.md` | `markdown-table` | Cluster table + per-repo entries |

For the curated entry points (one Packet each), use inline `items` instead of a fetched table — see per-offering detail below.

---

## 1. Courses (reading enablement)

Sequential, low-optionality. Tree: [`courses/`](../courses/).

| Course | Path | `technicalLevel` | `audience` |
|--------|------|------------------|-----------|
| Foundations | `courses/foundations/` | 100 — Strategic / 200 — Technical Overview | All (everyone starts here) |
| Sales Track | `courses/tracks/sales/` | 100 — Strategic | Sales, Leadership, FinOps |
| Solutions Track | `courses/tracks/solutions/` | 200–300 | Solutions, Architects |
| Engineering Track | `courses/tracks/engineering/` | 200–300 | Engineering |

Suggested single Packet `courses-foundations` (Foundations) plus one Packet per track. Each track README declares its own sections, modalities, and gaps.

## 2. Workshops (hands-on collections)

Choose-your-own-adventure. Tree: [`workshops/`](../workshops/).

| Workshop | Path | Level |
|----------|------|-------|
| General | `workshops/general/` | 200 |
| **OtterWorks** (flagship) | `workshops/otterworks/` | 300 — provisioned in runtime infra; maximal, consistent coverage |
| By Technical Domain (9) | `workshops/by-tech-domain/` | 200–300 |
| By Technical Role (5) | `workshops/by-tech-role/` | 200–300 |

OtterWorks is the **north-star** offering: tag it distinctly (`custom: ["flagship", "runtime-provisioned"]`) since it's the one app maintained for maximal coverage. Most attendees self-route into By Technical Domain / By Technical Role.

## 3. Labs (building blocks)

85 modules across 13 disciplines. Each is a self-contained challenge with a paste-into-Devin prompt. These compose into Workshops, so catalog Labs as one parseable Packet (`labs-catalog`) fed by `labs/README.md`, rather than 85 individual Packets — unless Field Kit wants module-level discoverability, in which case use `directory-list` per discipline.

## 4. Demos (facilitator-led)

11 showcases across data-engineering, migration, and security. Tree: [`demos/`](../demos/). One Packet per demo, `packetType: Demo`, `audience: [Sales, Solutions]`. These are linear "follow along" scripts, not labs.

## 5. Reference (shared lookup)

[`reference/general-themes/`](../reference/general-themes/) (7 cards) + [`reference/runtime-resources.md`](../reference/runtime-resources.md). Low priority for standalone cataloging; better attached to other Packets as `resources.setupGuide` / supporting material.

## 6. Catalog (repo inventory)

[`catalog/repos.md`](repos.md) + [`upstream-map.yaml`](upstream-map.yaml) — the source of `github-repo` resources that lab Packets link to as their `labPackage`.

## 7. Events

[`events/`](../events/) are non-reusable instances with venue/network specifics. Do **not** publish to Field Kit; if needed, catalog as `discoverability: Internal` and `publishState: Archived`.

---

## Suggested Packet Tag Defaults

| Field | Values used in this repo |
|-------|--------------------------|
| `productLine` | Devin Cloud, Devin Desktop, Devin CLI |
| `technicalDomain` | Modernization, Security, Data Engineering, DevOps/CI-CD, Cloud & Infrastructure, Observability/SRE, Testing & QA, Architecture, AI & ML, Documentation, Compliance |
| `language` | COBOL, Java, .NET/C#, Python, TypeScript/JS, Go, Rust, SAS, PL/SQL, T-SQL, Dart/Flutter |
| `audience` | Sales, Solutions, Engineering, Systems Integration, Leadership, FinOps, Enablement |
| `technicalLevel` | 100 — Strategic, 200 — Technical Overview, 300 — Practitioner, 400 — Expert |

Keep this map in step with the index READMEs — if an offering's structure changes, update both the README table (so Field Kit's parser stays accurate) and this document.
