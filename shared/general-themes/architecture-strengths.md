# Architecture Strengths — Quick Reference

> Comprehensive training: [Solutions Track — Platform Architecture Mastery](../../workshops/training-tracks/solutions/01-platform-architecture-mastery.md)

## Architecture Pillars

| Pillar | What It Means |
|--------|--------------|
| **Clean-Room Execution** | Each session runs on its own isolated VM — no lateral movement, ephemeral by default, reproducible from scratch, service account identity |
| **Shared Context Layer** | Persistent org-wide configuration flows into every session (see below) |
| **Context Retrieval** | Devin pulls from indexed repos, DeepWiki, MCP servers, shell, browser, and Knowledge notes — it does not rely on assumptions |
| **Shell & Tool Access** | Full Linux VM — build locally, run infrastructure tools, connect to remote systems, install what it needs |
| **Verification Model** | Local testing → CI monitoring → PR as review gate → external system validation |
| **Team Integration** | Org-wide config, PR-based communication, session continuity, full audit trail |

<a id="shared-context-layer"></a>

## Shared Context Layer

| Component | What It Provides | Configured By |
|-----------|-----------------|---------------|
| **Environment blueprints** | Pre-built VM images with dependencies, runtimes, tools | Platform team (once) |
| **Knowledge notes** | Coding standards, architecture decisions, domain glossary — retrieved automatically | Engineers (iteratively) |
| **Playbooks** | Repeatable procedures encoding institutional methodology | Senior engineers |
| **MCP servers** | Pre-configured integrations (Jira, Datadog, Confluence, etc.) | Platform team (once) |
| **Secrets** | Scoped credentials via platform secrets management — never in prompts or code | Security team |
| **Git connections** | Org-level repo access — all sessions clone and push immediately | Admin (once) |

**Design insight:** Clean-room isolation for security + shared context layer for productivity. Each VM is sandboxed, but org knowledge flows in automatically.

## Verification Model

1. **Local testing** — unit tests, integration tests, linting, type checking on the session VM
2. **CI monitoring** — watches CI checks, reads failure logs, pushes fixes, re-checks (multiple iterations)
3. **PR as review gate** — Devin never merges its own work; humans inspect the diff
4. **External validation** — connects to staging environments or external test systems

## Key Rules
- Security by default — no lateral movement between sessions, scoped credentials, ephemeral VMs
- The shared context layer is a one-time investment — one engineer configures, every session benefits
- The merge decision is always human — Devin proposes, you approve
