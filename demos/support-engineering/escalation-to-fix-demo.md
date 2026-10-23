# Customer Escalation to Merged Fix — Support Engineering Demo

A single linear demo for support and customer engineering: a customer ticket
arrives at 02:14 with a vague description, nobody is at a keyboard, and by the
time the on-call support engineer opens the queue there is a reproduction, a
root cause written in customer-safe language on the ticket, a fix PR with
regression tests, a sweep telling you where else the same defect lives, and a
Knowledge note so the next identical ticket is answered from known behavior.

The work runs on
[timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) —
a Node/Express + SQLite backend and a React 19 + Vite frontend with a real
Jest/Supertest suite — and fans out across three repositories.

<a id="toc"></a>
## Table of Contents

- [Quick Start](#quick-start)
- [Repositories](#repositories)
- [Before and After](#before-after)
- [Part 1 — Wire the Ticket Trigger](#part-1)
- [Part 2 — Triage and Reproduce a Description That Does Not Reproduce](#part-2)
- [Part 3 — Root Cause, Written Back to the Ticket](#part-3)
- [Part 4 — The Fix PR with Regression Tests](#part-4)
- [Part 5 — Devin Review on the Hotfix, at 2am](#part-5)
- [Part 6 — Duplicate-and-Pattern Sweep with Child Sessions](#part-6)
- [Part 7 — Knowledge So the Next Ticket Is Cheaper](#part-7)
- [What Still Needs a Human](#human-in-the-loop)
- [Key Takeaways](#key-takeaways)

---

<a id="quick-start"></a>
## Quick Start

Clone the app and get the verification gate green before anything else — this is
the gate every later step has to hold:

```bash
git clone https://github.com/Cognition-Partner-Workshops/timesheet-app
cd timesheet-app/backend
npm install
npm test        # Jest + Supertest — 8 suites, 161 tests, ~1s
```

```bash
cd ../frontend
npm install
npm run build   # tsc -b && vite build
```

The backend uses an in-memory SQLite database (`backend/src/database/init.js`),
so no external service is required. CI on the repo runs Node 20
(`.github/workflows/pr-checks.yml`).

---

<a id="repositories"></a>
## Repositories

- [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app) —
  client timesheet and billable-hours tracker. Backend: Express routes under
  `backend/src/routes/` (`workEntries.js`, `reports.js`, `clients.js`,
  `auth.js`), Joi validation in `backend/src/validation/schemas.js`, SQLite
  schema in `backend/src/database/init.js`, and Jest/Supertest specs under
  `backend/src/__tests__/routes/`. Frontend: `frontend/src/pages/`
  (`WorkEntriesPage.tsx`, `ReportsPage.tsx`, `DashboardPage.tsx`,
  `ClientsPage.tsx`) with the Axios client in `frontend/src/api/client.ts`.
  This is where the reported defect lives and where the fix lands.
- [calcom](https://github.com/Cognition-Partner-Workshops/calcom) — scheduling
  monorepo (Next.js web app, NestJS API v2, Prisma). Swept in Part 6 for the
  same date-boundary pattern.
- [eventflow-storefront](https://github.com/Cognition-Partner-Workshops/eventflow-storefront) —
  static storefront (vanilla JS served by Nginx). Also swept in Part 6.

---

<a id="before-after"></a>
## Before and After

| | Before | After |
|---|---|---|
| **Time to first response** | Ticket waits for business hours in the customer's region, then for a support engineer to read it | A triage comment with a reproduction is on the ticket minutes after it is filed, unattended |
| **Time to resolution** | Support reproduces (or fails to), escalates to engineering, engineering re-triages, a fix is scheduled | Reproduction, root cause, and a tested fix PR exist before the first human opens the ticket |
| **Escalation volume** | A "cannot reproduce" ticket becomes an engineering escalation | Tickets that Devin reproduces and patches typically never reach the engineering queue; the ones that do arrive with evidence attached |
| **Second identical ticket** | Re-triaged from scratch by whoever picks it up | Answered from a Knowledge note describing the known behavior and the shipped fix |

The point of this thread is the part no IDE assistant can do: the ticket
arrives when nobody is working, the reproduction requires an environment the
support engineer does not have, and the sweep spans repositories that no single
engineer owns.

---

<a id="part-1"></a>
## Part 1 — Wire the Ticket Trigger

The trigger is a Devin Automation. In this organization Jira is a connected
integration, so a ticket landing in the support project can start a session
with no human in the loop.

Open **Settings → Automations → New automation** and configure:

| Field | Value |
|---|---|
| Trigger | `jira:issue_created` |
| Condition | `data.project.id` `eq` your support project, `data.labels` `contains` `customer-escalation` |
| Action | `start_session` with the prompt below |
| Tools | MCP servers: `atlassian` (so the session can read and comment on the ticket) |
| Limits | `max_acu_limit` sized for a triage run; `invocations` capped per rolling window so a ticket storm cannot fan out unbounded |

The trigger payload carries the issue summary, description, labels, status,
project, and reporter. The session prompt reads the rest through the Atlassian
MCP server:

```
A customer escalation ticket was just filed in Jira. The trigger
payload contains the issue key, summary, description, and labels.

Repository: Cognition-Partner-Workshops/timesheet-app

Do the following without waiting for a human:

1. Read the full ticket with the Atlassian MCP server, including
   all comments and attachments.
2. Reproduce the reported behavior against the repo. Backend tests
   run with `npm test` in backend/ (Jest + Supertest). The frontend
   builds with `npm run build` in frontend/.
3. If you reproduce it, post a triage comment on the ticket with:
   the exact reproduction steps, the observed vs expected result,
   the file and line where the behavior originates, and a severity
   assessment. Write the comment for a support engineer, not a
   compiler.
4. If you cannot reproduce it, post what you tried, which
   environment variables and timezones you pinned, and the two most
   likely conditions that would produce the report.

Expected output: one Jira comment in the format above, plus a short
summary in the session with the reproduction command someone else
can run.
```

**If your support desk has no native trigger.** Ticket systems that Devin does
not integrate with natively (help-desk and ITSM tools) still work through the
`webhook:incoming` trigger: point the desk's outbound webhook at the
automation's webhook URL and the JSON body becomes the session's context. The
same automation shape applies — only the trigger type changes. The wiring is
identical to the CI-event path in
[Event-Driven SAST Remediation](../security/use-cases/event-driven-sast-remediation-demo.md).

Nothing about this step involves a person typing. That is the whole premise:
tickets arrive on the customer's clock, not the support team's.

---

<a id="part-2"></a>
## Part 2 — Triage and Reproduce a Description That Does Not Reproduce

The ticket that fires the automation reads like a real one:

```
Summary: Hours are logged against the wrong day

Description:
A few people on my team say the hours they enter show up on the
day before. It doesn't happen to everyone and it doesn't happen
every time. Our team is spread across Europe and the US. The
number of hours is right, just the date is off by one. Can you
check?
```

There are no reproduction steps, no timezone, no account, no timestamps. A
support engineer working in UTC will enter hours, see the correct date, and
close the ticket as "cannot reproduce" — which is exactly how this class of
defect survives to become an escalation.

Paste this into the session (or let the automation from Part 1 run it):

```
Repository: Cognition-Partner-Workshops/timesheet-app

A customer reports that logged hours sometimes appear on the day
before the day they selected, that it affects some team members
and not others, and that their team is spread across Europe and
the US. The date is wrong; the hours are right.

Reproduce this. Specifically:

1. Trace the full round trip of a work-entry date: the date picker
   in frontend/src/pages/WorkEntriesPage.tsx, the POST body it
   sends, the Joi schema in backend/src/validation/schemas.js, the
   INSERT in backend/src/routes/workEntries.js, the column type in
   backend/src/database/init.js, and the value rendered back in the
   entries list.
2. Run the round trip under several timezones (at minimum UTC,
   America/Chicago, Europe/Berlin, Asia/Tokyo) by setting TZ, and
   record what is posted, what is stored, and what is displayed in
   each.
3. Report the results as a table with one row per timezone and
   columns: TZ, date selected, date posted, value stored, date
   displayed.

Expected output: the table above plus the exact commands used, so
a support engineer can rerun it.
```

Devin pins the timezone rather than trusting its own environment, and the
report comes back looking like this:

| TZ | Selected | Posted | Stored | Displayed |
|---|---|---|---|---|
| `UTC` | Mar 15 | `2026-03-15` | `1773532800000` | 3/15/2026 |
| `America/Chicago` | Mar 15 | `2026-03-15` | `1773532800000` | **3/14/2026** |
| `Europe/Berlin` | Mar 15 | **`2026-03-14`** | `1773446400000` | 3/14/2026 |
| `Asia/Tokyo` | Mar 15 | **`2026-03-14`** | `1773446400000` | 3/14/2026 |

Two things are now clear that the ticket never said. The defect has *two*
halves — one on write, one on read — and it is invisible in UTC, which is why
the support engineer could not reproduce it and why "it doesn't happen to
everyone" was an accurate description of a real bug rather than noise.

---

<a id="part-3"></a>
## Part 3 — Root Cause, Written Back to the Ticket

The root cause is in three files:

- `frontend/src/pages/WorkEntriesPage.tsx:160` sends
  `formData.date.toISOString().split('T')[0]`. The picker holds local midnight;
  `toISOString()` converts to UTC first. At UTC+1 or later, local midnight is
  still the previous calendar day in UTC, so the *posted* date is a day early.
- `backend/src/validation/schemas.js:14` declares `date: Joi.date().iso()`, so
  the route receives a JavaScript `Date` rather than a `YYYY-MM-DD` string.
  `backend/src/routes/workEntries.js:104-105` binds that object into the
  INSERT, and the SQLite driver stores it as epoch milliseconds — UTC midnight
  — even though `backend/src/database/init.js:35` declares the column `DATE`.
- The list and dashboard render that instant with
  `new Date(entry.date).toLocaleDateString()`
  (`frontend/src/pages/WorkEntriesPage.tsx:236`,
  `frontend/src/pages/DashboardPage.tsx:132`,
  `frontend/src/pages/ReportsPage.tsx:235`). At UTC-6, UTC midnight is 7pm the
  previous evening, so the *displayed* date is a day early.

A calendar date is being carried as an instant in time. Every conversion in the
chain is individually reasonable and the composition is wrong.

Ask for the customer-facing version — support-safe language, no file paths, no
blame:

```
Post a comment on the Jira escalation ticket using the Atlassian
MCP server summarizing what we found, written for the customer's
support contact rather than for engineers.

Requirements for the comment:
- State the confirmed behavior in plain language and the exact
  conditions that trigger it (which users see it, and why some
  users never do).
- Give the customer a way to tell which of their existing entries
  are affected.
- State the interim workaround and its limits.
- State what we are changing and that a fix is in review, without
  naming files, line numbers, or internal components.
- No speculation about data loss, and no commitment to a release
  date.

Also add a second, internal-only comment with the technical root
cause and the affected file paths for the engineer who picks this
up.
```

Two audiences, one investigation: the customer gets behavior, conditions, and a
workaround; the internal comment carries the file-level detail. The ticket is
now the record of the investigation, so the time-to-first-response clock stops
before a human has read the ticket.

---

<a id="part-4"></a>
## Part 4 — The Fix PR with Regression Tests

The fix has to hold both halves — write and read — and it has to be provable in
CI rather than by an engineer changing their laptop's clock:

```
Repository: Cognition-Partner-Workshops/timesheet-app

Fix the off-by-one work-entry date defect so a selected calendar
date round-trips unchanged in any browser timezone.

Treat the work-entry date as a calendar date, never as an instant:

1. frontend/src/pages/WorkEntriesPage.tsx: replace the
   `toISOString().split('T')[0]` serialization at line 160 with
   local-calendar formatting (date-fns `format(date, 'yyyy-MM-dd')`
   — date-fns is already a dependency), and stop constructing
   `new Date(entry.date)` for display at lines 109 and 236.
2. frontend/src/pages/DashboardPage.tsx (line 132) and
   frontend/src/pages/ReportsPage.tsx (line 235): render the stored
   date without a timezone conversion. Put the parse/format helpers
   in one module under frontend/src/ and use it from all three
   pages rather than repeating the logic.
3. backend: keep the stored value a date-only string. Adjust the
   Joi rule in backend/src/validation/schemas.js so `date` reaches
   the route as a `YYYY-MM-DD` string, and make sure the INSERT and
   UPDATE paths in backend/src/routes/workEntries.js bind that
   string. Reject a request whose date is not a calendar date.
4. Do not change how `created_at` / `updated_at` timestamps are
   handled — those are genuine instants.

Regression tests, following the existing conventions in
backend/src/__tests__/routes/workEntries.test.js (Supertest against
the real router with the database module mocked):
- POST then GET returns the same calendar date that was sent.
- The stored bind parameter is the date-only string, asserted on
  the mocked db.run call.
- The same assertions pass with the process TZ pinned to UTC,
  Europe/Berlin, and America/Chicago.
- A malformed date is rejected with 400.

Report existing stored rows that were written as epoch
milliseconds as a migration question in the PR description — do
not write a data migration in this change.

The whole backend suite (`npm test` in backend/) must be green, and
`npm run build` in frontend/ must succeed.
```

Two things in that prompt matter as much as the fix. The tests pin `TZ`, so the
defect can never again be invisible to whoever runs CI. And the pre-existing
rows written as epoch milliseconds are surfaced as a question rather than
silently migrated — see [What Still Needs a Human](#human-in-the-loop).

---

<a id="part-5"></a>
## Part 5 — Devin Review on the Hotfix, at 2am

The PR exists at 02:40. Nobody who can approve it is awake. Devin Review is,
and it reviews the hotfix the same way it reviews everything else — reading the
diff against the repository's conventions and posting line comments on the PR.

On this PR the review has real work to do. A date fix is exactly the kind of
change where the diff looks small and the blast radius is not: the export paths
in `backend/src/routes/reports.js` read the same `date` column into CSV and PDF
output, and `backend/src/__tests__/routes/reports.test.js` asserts on that
output. Review feedback on a hotfix typically lands on the seams — a display
site that was missed, a test that pins the wrong timezone, an assertion that
encodes the old epoch value.

Close the loop in the same session:

```
Address every comment on the fix PR for
Cognition-Partner-Workshops/timesheet-app. For each one, either
push a commit that resolves it or reply on the PR explaining why
the current behavior is correct. Re-run `npm test` in backend/ and
`npm run build` in frontend/ after the changes and report the
results on the PR.
```

The loop runs the other direction too. When a support engineer or an
on-call developer pushes their own patch for a customer issue — the
one-line change made under pressure at 3am — request Devin as a reviewer on
that PR. Same reviewer, same standards, applied to human work:

```
Review the open pull request in
Cognition-Partner-Workshops/timesheet-app that changes work-entry
date handling. Check specifically whether the change is safe across
timezones: whether any local-to-UTC conversion remains on a
calendar date, whether the reports and dashboard read paths agree
with the write path, and whether the tests would fail if the
process timezone were UTC+9 instead of UTC.

Expected output: PR review comments on the specific lines, plus a
summary comment stating whether the change is safe to merge as a
hotfix.
```

A reviewer who is awake at 2am, applies the same bar to a hotfix as to a
planned change, and does not need the context reloaded is a team capability,
not a personal productivity tool.

---

<a id="part-6"></a>
## Part 6 — Duplicate-and-Pattern Sweep with Child Sessions

One ticket was filed. The real support question is how many *unfiled* tickets
share this root cause — the other places in this codebase where a calendar date
is carried as an instant, and whether sibling products have the same defect.

This is the fan-out step. One parent session, one child per target, results
aggregated back:

```
Act as the orchestrator for a duplicate-and-pattern sweep for the
work-entry date defect fixed in
Cognition-Partner-Workshops/timesheet-app.

The pattern: a calendar date (no time component) converted through
UTC — for example `toISOString().split('T')[0]` applied to a
locally-constructed Date, or `new Date(<date-only value>)` rendered
with toLocaleDateString — so the calendar day shifts with the
viewer's or server's timezone offset.

Spawn one child session per target below. Each child reports only
confirmed hits with exact file:line, a one-line description of the
user-visible symptom, and a classification of Affected or Benign
(a timestamp used in a filename or a log line is Benign).

Targets:
1. Cognition-Partner-Workshops/timesheet-app — every remaining site
   outside the fix: backend/src/routes/reports.js (CSV and PDF
   export), frontend/src/pages/ReportsPage.tsx,
   frontend/src/pages/DashboardPage.tsx,
   frontend/src/pages/ClientsPage.tsx.
2. Cognition-Partner-Workshops/calcom — apps/api/v1, apps/api/v2,
   apps/web/modules/bookings, packages/features/schedules,
   packages/features/bookings.
3. Cognition-Partner-Workshops/eventflow-storefront — public/app.js
   and public/ops.js.

Aggregate the children's findings into a single ranked table
(repository, file:line, symptom, Affected or Benign, suggested
owner) and post it as a comment on the Jira escalation ticket.
```

The aggregate is what makes this a support artifact rather than a code search.
In this repository set the sweep separates a small number of genuine hits from
a larger number of benign ones:

| Repository | Site | Classification |
|---|---|---|
| timesheet-app | `frontend/src/pages/DashboardPage.tsx:132`, `frontend/src/pages/ReportsPage.tsx:235` | Affected — same read-side shift, different page |
| timesheet-app | `frontend/src/pages/ReportsPage.tsx:61,81` | Benign — export filename timestamp |
| timesheet-app | `frontend/src/pages/ClientsPage.tsx:248`, `frontend/src/pages/ReportsPage.tsx:256` | Benign — `created_at` is a genuine instant |
| calcom | `apps/api/v2/src/ee/event-types/event-types_2024_06_14/transformers/internal-to-api/future-booking-limits.ts:22-23` | Affected — date-only API fields serialized through UTC |
| calcom | `companion/utils/gmailGoogleChipParser.ts:125` | Affected — all-day calendar dates converted through UTC |
| eventflow-storefront | `public/app.js:193,205`, `public/ops.js:139,192,226` | Benign — order and incident timestamps |

Coverage depends on repo structure — a sweep finds instances of the pattern it
was given, not every date defect that exists. What it does reliably is convert
"is this one customer or a hundred?" from a guess into a table with owners
attached, in one pass, while the on-call engineer is asleep. Sequentially this
is a week of engineering time nobody schedules.

---

<a id="part-7"></a>
## Part 7 — Knowledge So the Next Ticket Is Cheaper

The last step is the one that changes next quarter's numbers. The second
identical ticket should cost minutes, not a re-investigation:

```
Create a Devin Knowledge note capturing what we learned from this
escalation so future sessions answer the next occurrence from known
behavior.

Title it for the symptom a customer would report, not the fix.
Include: the customer-visible symptom, the two conditions that
produce it (positive UTC offset shifts the write, negative UTC
offset shifts the read), the reproduction command with pinned TZ,
the file paths that were changed, the PR link, and the customer-safe
explanation we posted on the ticket.

Trigger description: apply when a session is investigating a
reported wrong-date, off-by-one-day, or timezone-related defect in
Cognition-Partner-Workshops/timesheet-app.
```

That note is the shared context layer doing its job. It joins the other pieces
this thread already leaned on: the Atlassian MCP server that let the session
read and write the ticket, DeepWiki for orienting in an unfamiliar repository,
and the automation prompt itself, which is a reusable triage procedure — worth
promoting to a playbook once the team has run it a few times, so triage
sessions produce the same comment format.

The next ticket that says "dates are wrong" starts from the note: known
behavior, known fix, known workaround. The support team's answer stops
depending on which engineer picks it up.

---

<a id="human-in-the-loop"></a>
## What Still Needs a Human

Naming this honestly is part of the demo:

- **Merge approval on a customer-facing hotfix.** Devin produces a tested PR
  and a review; a human decides it ships.
- **The customer relationship.** The draft comment on the ticket is a draft. A
  support engineer owns tone, commitments, and anything touching an SLA,
  credit, or escalation to an account team.
- **Production data.** The rows already written as epoch milliseconds are a
  data-correction decision — which rows, whose data, and whether to notify —
  and that judgment belongs to a human, which is why Part 4 surfaced it as a
  question instead of writing a migration.
- **Severity and prioritization.** Devin can report the blast radius; whether a
  date shift is a Sev-2 for this customer depends on contract and context it
  does not have.
- **Production access.** The demo reproduces in a local environment with a
  pinned timezone. Reproducing against production data or systems stays behind
  the same access controls as any engineer.
- **Judgment on ambiguous reports.** Devin reproduced this one because the
  report contained a usable signal ("Europe and the US"). Reports with no
  signal still need a human to ask the customer the right question.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **The trigger is the product.** A ticket created a session; a session
  produced a reproduction, a root cause, and a PR. No engineer was awake for
  any of it. This is work that cannot happen in an editor, because the
  precondition for an editor is a person sitting in front of it.
- **Reproduction is the expensive part of support, and it is automatable.** The
  customer's description was vague and correct. Pinning `TZ` across four
  timezones turned "cannot reproduce" into a table showing that the defect has
  a write half and a read half and is invisible in UTC.
- **The ticket becomes the artifact.** Root cause in customer-safe language on
  the ticket, technical detail in an internal comment, sweep results appended
  as a table — time-to-first-response stops before a human reads the ticket.
- **The fix is trusted because it is gated.** Regression tests that pin the
  timezone mean the defect cannot reappear invisibly, and the pre-existing
  epoch-millisecond rows were escalated as a migration question rather than
  silently rewritten.
- **Devin Review works in both directions.** It reviewed the agent's hotfix at
  2am and applied the same standard to the human's 3am patch. The reviewer is
  on duty at 2am; the bar does not drop because of the hour.
- **Child sessions turn one ticket into a fleet answer.** The sweep separated
  genuine duplicates from benign timestamp formatting across three
  repositories in one pass and produced a ranked table with owners — the
  question a support manager actually asks, answered before the second ticket
  arrives.
- **The team gets better, not one engineer.** The Knowledge note, the triage
  prompt, and the MCP-connected ticket flow mean the next occurrence is
  answered from known behavior. Measured where support leadership measures:
  time-to-first-response, time-to-resolution, and how much of the queue ever
  reaches engineering.
