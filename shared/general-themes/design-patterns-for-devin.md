# Design Patterns for Devin

Proven patterns for structuring work, codebases, and processes so that Devin delivers high-quality results consistently.

## Pattern 1: Locally Buildable and Testable Code

**Principle:** If a human developer can clone the repo and run `make test` (or equivalent) with no external dependencies, Devin can too.

**Why it matters:** Devin verifies its own changes by running your build and test suite on its VM. If the build requires a VPN, a licensed IDE, a proprietary database, or manual setup steps that cannot be automated, Devin cannot complete the feedback loop. The more self-contained your build is, the tighter Devin's verify-and-iterate cycle becomes.

**How to apply:**
- Containerize dependencies (Docker Compose for databases, message brokers, caches)
- Use in-memory or file-based databases for tests (SQLite, H2, Testcontainers)
- Provide seed data scripts that create a working test environment from scratch
- Document the build/test command in the README (`npm test`, `./gradlew check`, `dotnet test`)
- Use environment variables for configuration — Devin injects these via the platform's secrets management layer (configured once at the org level, available to every session)

## Pattern 2: Event-Driven Triggers

**Principle:** Connect Devin to your existing event sources so it responds to signals automatically — no human has to remember to invoke it.

**Architecture:**
```
Event Source (CI, alerting, issue tracker)
    ↓ webhook / API call
Trigger Layer (GitHub Actions, Azure Function, Lambda)
    ↓ Devin API: POST /sessions
Devin Session (autonomous execution)
    ↓ PR / comment / status update
Review Gate (human approval, CI checks)
```

**Common triggers:**
- CI pipeline failure → Devin reads logs, pushes fix
- SAST finding above threshold → Devin remediates
- Work item tagged → Devin implements
- Alert fires → Devin investigates and triages
- Schedule (cron) → Devin performs routine maintenance

**Key safeguards:**
- Filter out Devin's own events to prevent infinite loops (check PR author, skip `devin-ai-integration[bot]`)
- Set a maximum retry count per trigger to avoid runaway sessions
- Use idempotency keys to prevent duplicate sessions from the same event

## Pattern 3: Divide and Conquer with Child Agents

**Principle:** For large-scale work, a parent agent breaks the problem into independent units and spawns a child agent for each one.

**When to use:**
- Migrating N modules/services/jobs (each is independent)
- Remediating N security findings across M repos
- Generating tests for N uncovered files
- Applying the same refactoring pattern across many codebases

**Architecture:**
```
Parent Agent
├── Analyzes scope (list of targets)
├── Creates playbook (reusable methodology)
├── Spawns Child Agent 1 → target A → PR
├── Spawns Child Agent 2 → target B → PR
├── ...
├── Spawns Child Agent N → target N → PR
└── Monitors progress, handles failures
```

**Best practices:**
- Define a clear, repeatable unit of work for each child
- Use playbooks to encode the methodology so every child follows the same process
- Set a maximum concurrency to avoid overwhelming CI or API rate limits
- Have the parent agent check child results and escalate failures

## Pattern 4: Human-in-the-Loop via PR Feedback

**Principle:** Devin proposes changes via pull requests. Humans review, comment, and approve. Devin iterates based on feedback.

**The loop:**
1. Devin opens a PR with its implementation
2. Reviewers (human or Devin Review) inspect the diff
3. Reviewers leave comments requesting changes
4. Devin reads the comments, makes changes, pushes new commits
5. CI re-runs, Devin monitors results
6. Repeat until approved and merged

**Why this works:**
- The PR is a familiar, auditable artifact that fits existing workflows
- Multiple team members can contribute feedback to the same session
- Devin hibernates between interactions — no wasted compute while waiting for review
- The merge decision always belongs to a human

## Pattern 5: Toolchain-Agnostic Stubs

**Principle:** Design integration architectures with replaceable tool slots. The pattern stays the same; the specific tool is pluggable.

**Example — Security Scanning Pipeline:**
```
[SAST Tool] → webhook → [Trigger Layer] → Devin API → [Remediation Session]
```

The `[SAST Tool]` slot can be filled by:
- SonarQube / SonarCloud
- Checkmarx
- Fortify
- Snyk
- Trivy
- Any tool that produces findings in a parseable format

**How to apply:**
- Document the interface contract (webhook payload shape, findings format)
- Provide reference implementations for 2-3 popular tools
- Include a "bring your own tool" guide showing how to adapt the pattern

## Pattern 6: Context Layer Configuration

**Principle:** Invest in configuring Devin's shared context layer once to benefit every future session.

**Components:**
- **Environment configurations (VM blueprints)** — Pre-built machine images with dependencies, language runtimes, tools, and startup scripts baked in. Sessions boot ready to build, not waiting for installation
- **Knowledge notes** — Coding standards, architecture decisions, team conventions, domain glossary. Devin retrieves these automatically when relevant to the task
- **Playbooks** — Step-by-step procedures for recurring tasks (deploy, migrate, audit). Devin follows playbooks precisely, ensuring consistent execution across team members
- **MCP servers** — External tool connections (Jira, Datadog, Confluence, Azure DevOps) that persist across sessions. Configure once, use everywhere
- **Secrets** — Scoped credentials (API keys, service account tokens, database passwords) injected at session start. No credentials in prompts or code
- **Git connections** — Repository access that applies to all sessions in the organization

**ROI:** The upfront investment in context configuration pays dividends across every subsequent session. A well-configured Devin organization starts every task with the right tools, knowledge, credentials, and access — no per-session setup friction. This is the mechanism behind the [clean-room + shared context](architecture-strengths.md#shared-context-layer) design: runtime isolation for security, persistent configuration for productivity.
