# Lab: Scheduled Security & Coverage Automation

## Objective

Set up a recurring Devin scheduled session that runs weekly security scans and test coverage checks across the OtterWorks monorepo, automatically opening PRs for any regressions — demonstrating the "capacity-constrained" automation pattern.

## Why This Matters

Security scans and test coverage checks are the classic "we should do this regularly but never do" tasks. Teams run them before releases, find a backlog of issues, and scramble. Devin's scheduled sessions turn this into a recurring automation: every week, Devin runs the scans, triages findings, fixes what it can, and opens PRs for human review.

This is the "capacity-constrained" trigger from the Devin design patterns: recurring O&M work that humans defer because other priorities always win.

## The Setup

### Step 1 — Understand the Baseline (10 min)

First, establish what the scans currently find. Ask Devin:

```
Run the OtterWorks security scanner with make security-scan
and the test coverage report with make test-coverage. Give
me a summary table: service name, critical CVEs found, high
CVEs found, test coverage percentage.
```

This is your baseline. A good scheduled automation catches regressions from this point forward.

### Step 2 — Design the Scheduled Task (10 min)

Think about what you want the weekly session to do:

1. Run `make security-scan` — check for new CVEs since last run
2. Run `make test-coverage` — check for coverage regressions
3. Compare against the baseline (previous week's results)
4. For new CRITICAL CVEs: attempt an automatic fix (upgrade dependency, run tests)
5. For coverage regressions: identify which files lost coverage and why
6. Open a PR with a weekly report and any fixes applied

### Step 3 — Create the Schedule (15 min)

Set up a Devin scheduled session:

1. Go to Devin settings → Scheduled Sessions
2. Create a new schedule:
   - **Name:** OtterWorks Weekly Security & Coverage
   - **Schedule:** Weekly (e.g., Monday 6:00 AM UTC)
   - **Repository:** otterworks
   - **Prompt:**

```
Weekly security and coverage audit for OtterWorks.

1. Run make security-scan. Parse the output for CRITICAL
   and HIGH findings.
2. Run make test-coverage. Record coverage percentages
   per service.
3. For any NEW critical CVEs (not in .trivyignore):
   - Identify the vulnerable dependency and service
   - Upgrade to the nearest patched version
   - Run the service tests to verify
4. For any coverage regressions below the per-service
   thresholds documented in docs/CI_STRATEGY.md:
   - Identify which files lost coverage
   - Add targeted tests for the uncovered code paths
5. Open a single PR titled "Weekly Security & Coverage
   Report - [date]" with:
   - Findings summary table
   - Any dependency upgrades applied
   - Any new tests added
   - Comparison to last week (if available)
```

### Step 4 — Verify the First Run (15 min)

Trigger the scheduled session manually to verify it works:
- Does it run the scans correctly?
- Does it parse findings accurately?
- Does it attempt reasonable fixes?
- Is the PR useful for human review?

Iterate on the prompt if the output is not what you expected.

## What Done Looks Like

- [ ] Established a baseline: ran security scans and coverage checks, recorded current state
- [ ] Designed the weekly task with clear steps and expected output
- [ ] Created a Devin scheduled session with the weekly prompt
- [ ] Triggered the first run and reviewed the output
- [ ] The session produced a PR with a findings summary and any fixes applied
- [ ] Can explain the value: this runs every week automatically, catching drift before it accumulates

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started

Start by running `make security-scan` manually in a Devin session to see what the output looks like. This tells you what the scheduled session needs to parse.

### Hint 2 — Prompt Engineering

The scheduled session prompt is critical — it runs unattended, so it needs to be precise. Include specific Makefile targets, output formats, and decision criteria (what counts as "new" vs "known"). Vague prompts produce inconsistent results.

### Hint 3 — Connecting to the SAST Pipeline

This scheduled session complements the event-driven SAST pipeline (D1). The SAST pipeline catches issues on PRs in real-time; the scheduled session catches issues that accumulate between PRs (new upstream CVEs, transitive dependency updates, coverage drift). Together they form a complete security automation strategy.

## Key Takeaways

- Scheduled sessions automate "should-do" work that teams defer — security scans, coverage checks, dependency audits
- The prompt is the methodology: a well-written scheduled prompt is effectively a Playbook that runs on cron
- Scheduled automation catches drift before it accumulates — weekly is better than quarterly fire drills
- Scheduled scans complement event-driven scans: one catches PR-level issues, the other catches ecosystem-level drift

## Time Budget

- ~45-60 minutes for setup and first run verification
- Advanced participants: create a second scheduled session for a different recurring task (e.g., dead code detection, documentation freshness)
