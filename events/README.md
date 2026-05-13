# Events

This directory contains event-specific workshop instances. Each event links to one or more reusable workshops from `workshops/`, adds event-specific details (date, location, audience), and can override any workshop content for that specific instance.

## Directory Structure

```
events/
├── _template/
│   └── README.md              ← Copy this to start a new event
├── active/
│   └── YYYY-MM-DD-<event-id>/
│       └── README.md          ← Upcoming or in-progress events
├── archive/
│   └── YYYY-MM-DD-<event-id>/
│       └── README.md          ← Past events (ended)
└── README.md                  ← You are here
```

### Naming Convention

Event directories use the pattern **`YYYY-MM-DD-<event-id>`** where:
- `YYYY-MM-DD` is the event date (or `YYYY-MM` if only the month is known)
- `<event-id>` is a short slug describing the event (city, audience, or topic)

Examples: `2026-06-15-new-york`, `2026-07-10-virtual-security`, `2026-08-dc-platform`

### Active vs. Archive

| Directory | Contents | When to Use |
|-----------|----------|-------------|
| `active/` | Upcoming or currently running events | Event has not yet occurred |
| `archive/` | Past events | Event date has passed |

After an event ends, move its directory from `active/` to `archive/`.

## Workshop vs. Event

- **Workshops** (`workshops/`) are reusable reference templates — they define the labs, modules, repos, and structure.
- **Events** (`events/`) are specific instances — they reference workshops and add facilitator details, audience context, and overrides.

See [`workshops/README.md`](../workshops/README.md) for available workshop templates.

## How to Create a New Event

1. Copy `_template/` to `active/YYYY-MM-DD-<event-id>/`
2. Reference the workshop(s) this event is based on (from `workshops/`)
3. Edit the `README.md` to fill in event details and any overrides
4. Specify which repos need to be set up on Devin's machine
5. Note any runtime resources that need to be provisioned

## Time Budget Guidelines

| Workshop Duration | Recommended Challenges | Notes |
|------------------|----------------------|-------|
| 2 hours | 2–3 challenges | Pick from one category |
| Half day (4 hours) | 4–6 challenges | Mix 2 categories |
| Full day (8 hours) | 8–12 challenges | Mix 3+ categories, include advanced |
| Multi-day | All challenges | Full curriculum |

## Audience-Based Recommendations

| Audience | Recommended Categories | Entry Point |
|----------|----------------------|-------------|
| Developers | D (Feature Dev) + C (Migration) | D1 or D4 |
| QA Engineers | A (Quality) + D (Bug Fixing) | A1 or A2 |
| DevOps/Platform | E (DevOps) + B (Security) | E1 or E3 |
| Security Engineers | B (Security) + E (DevOps) | B2 or B3 |
| Architects | C (Migration) + E (DevOps) | C3 or C5 |
| Mixed/General | A + B + C + D | A1 (warm-up) → B1 → C2 → D4 |
| Executive | D4 + A1 + C2 | Quick wins with visible output |
| Mainframe/COBOL | C (Migration: MM12→MM13→MM14→MM1) | MM12 (discovery) |
