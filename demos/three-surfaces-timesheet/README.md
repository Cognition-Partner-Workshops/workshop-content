# Three Surfaces of Devin — Full-Stack Feature Demo

A single linear demo that walks through Devin Desktop, Devin CLI, and Devin
Cloud on one relatable full-stack application: building a "Projects" management
feature for `timesheet-app` (React + Node.js/Express + SQLite). The narrative
starts with interactive exploration, progresses through planning, hands off to
autonomous execution, and ends with parallel Cloud sessions and the PR feedback
loop — showing how the three surfaces connect into one workflow.

<a id="toc"></a>
## Table of Contents

- [Prerequisites](#prerequisites)
- [Suggested Timing](#suggested-timing)
- [Repositories](#repositories)
- [How the Surfaces Relate](#how-the-surfaces-relate)
- [Part 1 — Devin Desktop: Explore and Prototype](#part-1)
- [Part 2 — Devin CLI: Understand and Plan](#part-2)
- [Part 3 — Handoff to Devin Cloud](#part-3)
- [Part 4 — Devin Cloud: Autonomous Execution and PR Review](#part-4)
- [Part 5 — Parallel Cloud Sessions: Team of Agents](#part-5)
- [If the Audience Is More Technical](#more-technical)
- [Key Takeaways](#key-takeaways)

---

<a id="prerequisites"></a>
## Prerequisites

| Requirement | Detail |
|-------------|--------|
| **Devin Desktop** | Installed and signed in ([download](https://devin.ai/download)) |
| **Devin CLI** | Installed: `curl -fsSL https://cli.devin.ai/install.sh \| bash` |
| **Devin Cloud** | Access to the Devin web app with a configured org |
| **Repository** | [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) cloned locally and available in the org's GitHub connection |
| **Node.js** | v18+ (for running the app locally if desired) |

Clone the repo before starting:

```bash
git clone https://github.com/Cognition-Partner-Workshops/timesheet-app.git
cd timesheet-app
```

---

<a id="suggested-timing"></a>
## Suggested Timing

| Time | Section | Surface |
|------|---------|---------|
| 0:00 – 0:05 | [Part 1](#part-1) — Explore the codebase, ask questions in Chat mode | Desktop |
| 0:05 – 0:10 | [Part 2](#part-2) — `/ask` to explain code, `/plan` the Projects feature | CLI |
| 0:10 – 0:13 | [Part 3](#part-3) — `/handoff` the plan to Cloud | CLI → Cloud |
| 0:13 – 0:20 | [Part 4](#part-4) — Watch the Cloud session work, review the PR | Cloud |
| 0:20 – 0:25 | [Part 5](#part-5) — Kick off parallel sessions for additional features | Cloud |
| 0:25 – 0:30 | Wrap-up, [technical deep-dive](#more-technical) if time allows | — |

Total: ~25–30 minutes.

---

<a id="repositories"></a>
## Repositories

- [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) — React 19 + Node.js/Express + SQLite full-stack application. Existing features: user authentication (JWT), client management (CRUD), hourly work entries, reports with CSV/PDF export. The "Projects" feature does not exist yet — Devin builds it live.

---

<a id="how-the-surfaces-relate"></a>
## How the Surfaces Relate

```
┌─────────────────────────────────────────────────────────────┐
│                     Devin Platform                           │
├───────────────┬───────────────────────┬─────────────────────┤
│  Devin Cloud  │    Devin Desktop      │     Devin CLI       │
│  (Web App)    │    (IDE / Windsurf)   │     (Terminal)      │
│               │                       │                     │
│  Autonomous   │  Interactive coding   │  Local agent in     │
│  background   │  with AI pair +       │  your terminal +    │
│  agent on     │  cloud delegation     │  cloud delegation   │
│  its own VM   │                       │                     │
└───────┬───────┴───────────┬───────────┴──────────┬──────────┘
        │                   │                      │
        └───────────────────┼──────────────────────┘
                            │
              Shared context: Knowledge, Secrets,
              AGENTS.md, MCP, Git connections
```

- **Devin Desktop** — interactive pair programming in the IDE. Best for exploring unfamiliar code and prototyping changes with real-time editor context.
- **Devin CLI** — a local agent in the terminal. Best for quick questions, planning, and iterative refinement before committing to execution.
- **Devin Cloud** — autonomous background execution on its own VM. Best for full implementations, long-running tasks, and parallel work across repos.

All three share the same platform context and can delegate work to Cloud. This demo walks through them in that order: explore → plan → execute.

> For comprehensive feature tours, see
> [Devin Desktop](../../courses/foundations/product/local/devin-desktop.md),
> [Devin CLI](../../courses/foundations/product/local/07-devin-cli.md), and
> [Devin Cloud](../../courses/foundations/product/cloud/devin-cloud.md).
> For a comparison of when to use each surface, see
> [Cloud vs. Local Agents](../../reference/general-themes/cloud-vs-local-agents.md).

---

<a id="part-1"></a>
## Part 1 — Devin Desktop: Explore and Prototype

Open `timesheet-app` in Devin Desktop. The IDE indexes the workspace
automatically, giving Devin Local full context over the project.

### 1.1 — Chat Mode: understand the codebase

Open Devin Local (`Cmd/Ctrl+L`) and switch to **Chat** mode. Chat mode answers
questions without modifying files — ideal for orientation.

```
What is the architecture of this application? Walk me through the
backend route structure in backend/src/routes/, the database schema
in backend/src/database/init.js, and how the React frontend in
frontend/src/pages/ connects to the API layer in frontend/src/api/.
```

Expected: Devin Local maps the Express routes (`auth.js`, `clients.js`,
`workEntries.js`, `reports.js`), the SQLite schema (users, clients,
work_entries tables), and the React pages that call the API through Axios
hooks.

### 1.2 — Chat Mode: scope the new feature

Still in Chat mode, ask about what a Projects feature would require:

```
If I wanted to add a "Projects" management feature — CRUD for projects
with fields like name, description, client assignment, start date, and
status (active/completed/on-hold) — what existing patterns in this
codebase should I follow? What files would need to change?
```

Expected: Devin Local identifies the patterns — the route file structure,
the database table creation pattern in `init.js`, the React page and
component conventions, and the API hook patterns — and produces a list of
files to create or modify.

### 1.3 — Code Mode: prototype a small change (optional)

Switch to **Code** mode. Code mode creates and modifies files in your
workspace — useful for quick prototyping.

```
Add the Projects database table to backend/src/database/init.js,
following the same pattern used for the clients and work_entries
tables. Include columns for: id, name, description, client_id
(foreign key to clients), start_date, status, created_at, updated_at.
```

Expected: Devin Local edits `init.js` directly in the workspace, adding
the `CREATE TABLE IF NOT EXISTS projects` statement. The change is
visible immediately in the editor diff — accept or revert with one click.

> **The point:** Desktop gives you real-time, interactive exploration and
> small edits with full editor context. The AI sees your open files,
> cursor position, and terminal output — no need to re-explain.

---

<a id="part-2"></a>
## Part 2 — Devin CLI: Understand and Plan

Switch to the terminal. Navigate to the `timesheet-app` directory and
start Devin CLI.

```bash
cd timesheet-app
devin
```

### 2.1 — Ask Mode: explain existing code

Use `/ask` to get a quick, one-shot explanation without modifying anything:

```
/ask How does the authentication middleware in
backend/src/middleware/auth.js work? How are JWT tokens issued
in backend/src/routes/auth.js and validated on protected routes?
```

Expected: Devin CLI reads the files and explains the JWT flow — token
creation on login, middleware validation on protected routes, and how the
user ID is attached to `req.user`. One-shot answer, no file changes.

### 2.2 — Plan Mode: design the full feature

Use `/plan` to create a detailed implementation plan **without executing**:

```
/plan Add a "Projects" management feature to this application.
Users should be able to create, view, edit, and delete projects.
Each project has a name, description, client assignment (linked to
existing clients), start date, and status (active/completed/on-hold).
Add the backend API endpoints in a new backend/src/routes/projects.js,
the database schema changes in backend/src/database/init.js, the
frontend UI page in frontend/src/pages/ProjectsPage.tsx, and tests
for the backend endpoints. Follow the existing patterns in the
codebase for the data model, API structure, and React components.
```

Expected: Devin CLI produces a step-by-step implementation plan: database
schema, Express route file, validation logic, React page with Material UI
components, API hooks, router integration, and test file. Review the plan
in the terminal — no code is written yet.

> **The point:** CLI gives you a tight interactive loop. `/ask` for instant
> answers, `/plan` to see the full approach before any code is written.
> You stay in control, in your terminal, iterating until the plan is right.

---

<a id="part-3"></a>
## Part 3 — Handoff to Devin Cloud

The plan is solid, but the full implementation — backend + frontend + tests +
database changes — is a multi-file, multi-step task. Hand it off to Cloud.

### 3.1 — Handoff from CLI

From the same Devin CLI session, use `/handoff`:

```
/handoff Implement the Projects feature plan we just created.
Add the full backend API (routes, validation, database schema),
the frontend UI (ProjectsPage with CRUD operations), API hooks,
and backend tests. Follow existing codebase patterns for
timesheet-app. The repo is
Cognition-Partner-Workshops/timesheet-app.
```

The Cloud session inherits the context from your CLI conversation — it
knows the codebase structure, the plan, and the patterns you discussed.
The git branch carries over.

### 3.2 — Alternative: Delegate from Desktop

If you are still in Devin Desktop, use the **Delegate to Cloud** button
in the Devin Local panel instead. Click it and paste the same
implementation prompt. The Cloud session picks up the workspace context.

> **The point:** the handoff is the bridge. Start locally — explore,
> prototype, plan with immediate feedback. When the task is scoped and
> ready for full execution, delegate to Cloud. Context carries over;
> you do not re-explain.

---

<a id="part-4"></a>
## Part 4 — Devin Cloud: Autonomous Execution and PR Review

Switch to the Devin web app. The Cloud session is running on its own VM
— you can watch it work in real time or move on to other tasks.

### 4.1 — Watch the session (optional)

Open the session in the web app. Devin is:
1. Reading the codebase and the plan context from the handoff
2. Creating `backend/src/routes/projects.js` with CRUD endpoints
3. Adding the `projects` table to `backend/src/database/init.js`
4. Adding validation in `backend/src/validation/`
5. Creating `frontend/src/pages/ProjectsPage.tsx` with Material UI
6. Wiring the new page into the React router and navigation
7. Writing tests in `backend/src/__tests__/`
8. Running the test suite to verify

### 4.2 — Review the PR

When Devin finishes, it opens a PR. This is where the feedback loop
begins:

1. **Read the PR diff** — Devin has implemented the full feature following
   existing codebase conventions
2. **PR Review analyzes the PR automatically** — it flags potential issues
   like missing validation, error handling gaps, or security concerns
3. **Leave a comment on the PR** to iterate:

```
The project name field needs a maximum length check — add
validation for max 100 characters in the backend. Also add
error handling for the case where a client_id references a
client that doesn't exist.
```

Devin wakes up, reads the comment, addresses the feedback, and pushes a
new commit. The PR Review agent re-analyzes the updated code.

> **The point:** Cloud Devin works autonomously while you do other work.
> The PR is the collaboration surface — leave comments, Devin fixes, the
> review agent provides automatic quality gates. This is the production
> workflow.

---

<a id="part-5"></a>
## Part 5 — Parallel Cloud Sessions: Team of Agents

The Projects feature is one task. In production, teams run many Devin
sessions concurrently across different workstreams. Show this by kicking
off parallel sessions on `timesheet-app` for additional features.

### 5.1 — Launch parallel sessions

Open new Devin Cloud sessions with these prompts:

**Session 2 — Team Workload Dashboard:**

```
Add a "Team Workload Dashboard" to
Cognition-Partner-Workshops/timesheet-app. Create a new page
that shows: which users have logged the most hours this week,
which clients have the most active work entries, and a summary
of total hours by day for the current week. Add a backend API
endpoint that calculates these aggregations from the existing
work_entries and clients tables. Add a frontend React page
with Material UI charts or tables. Follow existing codebase
patterns.
```

**Session 3 — Recurring Entries:**

```
Add a "Recurring Entries" feature to
Cognition-Partner-Workshops/timesheet-app. Users should be
able to mark a work entry as recurring (daily, weekly, or
monthly). Add a recurring_entries table, backend API endpoints
for CRUD operations on recurring entry templates, and a
frontend UI to manage them. When viewing work entries, show
which ones were created from a recurring template. Follow
existing codebase patterns. Write backend tests.
```

### 5.2 — The "team of agents" moment

Three sessions are now running in parallel — each on its own VM, each
working autonomously on a different feature for the same application.
This mirrors real enterprise usage: instead of one developer context-
switching between tasks, three agents work simultaneously and each
delivers a reviewed PR.

Switch between sessions in the web app to monitor progress. As PRs
arrive, review each one — the same feedback loop applies to all three.

---

<a id="more-technical"></a>
## If the Audience Is More Technical

For a technical audience that wants to see Devin on a larger, more
complex codebase, point to the flagship polyglot monorepo:

- [otterworks](https://github.com/Cognition-Partner-Workshops/otterworks) — a collaborative file storage and document editing platform (Google Drive + Google Docs equivalent). 11 backend microservices in 8 languages (Go, Java, Rust, Python, Node.js, Kotlin, Scala, Ruby, C#), 2 frontends (React/Next.js + Angular), orchestrated via Docker Compose with full observability (Prometheus, Grafana, Jaeger).

### Cloud DeepWiki + multi-agent parallelism

Open the otterworks repo's **DeepWiki** page in Devin Cloud. DeepWiki
typically maps the service topology, language boundaries, and inter-service
communication patterns — showing how Devin builds understanding of a
complex polyglot estate (coverage depends on repo structure).

### Cross-language impact analysis

Paste this prompt into a Devin Cloud session to demonstrate cross-language
understanding:

```
Analyze the Cognition-Partner-Workshops/otterworks repository.
Identify all API contracts between the services — which service
calls which, what protocols they use (REST, gRPC, message queues),
and where the contract definitions live. Then assess: if I change
the file metadata schema in the Rust file-service, which other
services (in other languages) would be affected? Produce a
dependency map and an impact report.
```

### Multi-agent parallelism on otterworks

For the "team of agents at scale" moment, launch parallel sessions
targeting different services in the monorepo — one session per language,
each working independently:

```
Run a security dependency audit on the Go api-gateway service in
Cognition-Partner-Workshops/otterworks. Check go.mod for known
CVEs, upgrade vulnerable dependencies, and run the service tests
to verify nothing breaks. The service is in services/api-gateway/.
```

```
Run a security dependency audit on the Python document-service in
Cognition-Partner-Workshops/otterworks. Check pyproject.toml for
known CVEs, upgrade vulnerable dependencies, and run the service
tests with poetry run pytest to verify. The service is in
services/document-service/.
```

```
Run a security dependency audit on the Node.js collab-service in
Cognition-Partner-Workshops/otterworks. Check package.json for
known CVEs, upgrade vulnerable dependencies, and run the service
tests with npm test to verify. The service is in
services/collab-service/.
```

Three sessions across three languages, each producing a verified PR — one
agent per service, running in parallel on separate VMs. This is the
enterprise pattern: Devin handles polyglot maintenance at scale while the
team focuses on architecture and product decisions.

---

<a id="key-takeaways"></a>
## Key Takeaways

1. **Three surfaces, one platform.** Desktop, CLI, and Cloud are not
   competing tools — they are complementary entry points for the same
   agent. Context (Knowledge, Secrets, AGENTS.md, MCP, Git) is shared
   across them.

2. **Match the surface to the task.** Desktop for interactive exploration
   with editor context. CLI for quick answers and planning in the
   terminal. Cloud for autonomous, long-running, or parallelizable work.

3. **The handoff is the bridge.** Start locally — explore, prototype,
   plan with immediate feedback. When the scope is clear, delegate to
   Cloud. Context and git branch carry over; no re-explanation needed.

4. **The PR is the collaboration surface.** Cloud Devin delivers work as
   PRs. The PR Review agent provides automatic quality gates. Human
   reviewers leave comments; Devin wakes up and addresses them. This
   closed loop is the production workflow.

5. **Parallel sessions unlock team-scale throughput.** In production,
   organizations run many Devin sessions concurrently — feature
   development, bug fixes, maintenance, and security remediation, each
   on its own VM. One engineer can oversee many agents working
   simultaneously.

6. **Knowledge compounds over time.** Conventions discovered during
   sessions become Knowledge items. Future sessions — on any surface —
   benefit automatically from the shared context layer.
