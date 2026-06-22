# When to Use Devin — Quick Reference

> Comprehensive training: [Foundations — What to Give AI vs. Not](../../workshops/foundations/04-what-to-give-ai.md) | [Engineering Track — Task Selection Framework](../../workshops/training-tracks/engineering/04-task-selection-framework.md)

## Trigger Categories

| Category | Trigger | Examples |
|----------|---------|----------|
| **Event-Driven** | Signal from your toolchain | Incident alert → triage + fix PR; SAST finding → remediate + re-scan; Ticket tagged → implement + PR; CI failure → diagnose + fix |
| **Large-Scale Campaign** | N independent targets, same pattern | COBOL → Java (200 copybooks); Spring Boot 2→3 (50 services); Security backlog (500 findings across 80 repos); Test coverage (200 uncovered modules) |
| **Capacity-Constrained** | Recurring O&M deferred by humans | Weekly dep bumps; Monthly dead code cleanup; Doc refresh on code change; Quarterly license audit; Sprint-boundary accessibility audit |

## Sweet Spot Checklist

A task is a strong candidate for Devin when it is:

- [ ] **Well-defined** — clear acceptance criteria, verifiable outcomes (tests pass, scan clean, build succeeds)
- [ ] **Repetitive at scale** — same pattern applied to many targets (repos, modules, findings)
- [ ] **Lower priority but valuable** — work humans would do if they had unlimited time
- [ ] **Automatable end-to-end** — Devin can fetch context, implement, test, and submit for review
- [ ] **Safe to iterate** — works on branches, never pushes to main, humans approve the merge
- [ ] **Urgency-oriented** — driven by deadlines, license sunsets, compliance mandates, or cost pressure

## What Devin Needs to Succeed

| Prerequisite | Why |
|-------------|-----|
| Indexed codebases | Devin retrieves context from connected repos |
| Programmatic access | MCP servers, API keys, service accounts for your toolchain |
| Locally buildable code | Devin runs builds and tests on its VM to verify its work |
| Clear prompts | Repo names, file paths, acceptance criteria, expected output format |

## Key Rules
- The insight is not "replace engineers" but "unlock work that is currently blocked or deferred"
- Event-driven tasks respond in seconds — no human context-switching required
- Large-scale campaigns use parent-child agents — weeks of work compressed to days
