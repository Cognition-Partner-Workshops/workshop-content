# Orchestration Patterns in Practice

<a id="toc"></a>
## Table of Contents

- [Pattern 1: Single Agent](#pattern-1-single-agent)
- [Pattern 2: Parent-Child (Divide and Conquer)](#pattern-2-parent-child-divide-and-conquer)
- [Pattern 3: Event-Driven](#pattern-3-event-driven)
- [Pattern 4: Scheduled](#pattern-4-scheduled)
- [Knowledge Check](#knowledge-check)
- [Key Takeaways](#key-takeaways)

---

This section covers when and how to use each orchestration pattern. For the theoretical foundations, see [Agent Orchestration](../../foundations/03-agent-orchestration.md) and [Multi-Agent Workers](../../foundations/09-multi-agent-workers.md). Quick reference: [Platform Capabilities](../../../shared/general-themes/platform-capabilities.md).

<a id="pattern-1-single-agent"></a>
## Pattern 1: Single Agent

**When to use:** The default for most tasks. One prompt, one agent, one PR.

**Best for:**
- Bug fixes
- Feature implementation
- Version upgrades (single repo)
- Test generation
- Documentation updates
- Any self-contained task that does not need to be parallelized

**Example prompt:**

```
Fix the N+1 query in the articles feed endpoint in
my-api-service. The endpoint GET /api/articles/feed
currently executes one query per article to fetch the
author profile. Refactor to use a JOIN or batch fetch.

Verify by running: ./gradlew test
Confirm that the feed endpoint test response time
drops below 200ms for 100 articles (currently ~2s).
```

**When NOT to use:** When the same task needs to be applied to many targets (use parent-child) or when the task should run automatically in response to events (use event-driven) or on a schedule (use scheduled).

<a id="pattern-2-parent-child-divide-and-conquer"></a>
## Pattern 2: Parent-Child (Divide and Conquer)

**When to use:** For campaigns across N independent targets where each target follows the same methodology.

**Best for:**
- Framework upgrades across many microservices
- Security remediation across many repos
- Test generation across many modules
- Code style migration across many packages
- Any "do X to each of N things" pattern

**How to set it up:**

1. **Plan the decomposition** — Identify the list of targets (repos, modules, services) and verify each can be handled independently.
2. **Write a Playbook** — Encode the methodology so every child follows the same process. A Playbook ensures consistency even when targets have slight differences.
3. **Set concurrency limits** — Avoid overwhelming CI systems or API rate limits. Start with 5-10 concurrent children and adjust based on results.
4. **Write the parent prompt** — The parent analyzes scope, creates children, and monitors progress.

**Example parent prompt:**

```
Upgrade Spring Boot from 2.7 to 3.2 across the
following microservices: order-service, user-service,
notification-service, payment-service, inventory-service.

For each service:
1. Update Spring Boot version in build.gradle
2. Migrate javax.* imports to jakarta.*
3. Update deprecated API usage
4. Run ./gradlew test to verify
5. Document breaking changes in the PR description

Use the spring-boot-3-upgrade Playbook. Run up to
5 services in parallel.
```

**Monitoring children:** The parent agent tracks child progress. If a child fails, the parent can retry, adjust the approach, or escalate. You will see individual PRs from each child — review them like any other PR.

<a id="pattern-3-event-driven"></a>
## Pattern 3: Event-Driven

**When to use:** When work should happen automatically in response to external signals — no human needs to create a session.

**Common triggers:**

| Trigger Source | Event | Devin's Response |
|---------------|-------|-----------------|
| CI pipeline | Build fails on a branch | Read failure logs, diagnose, push fix |
| SAST tool | New HIGH/CRITICAL finding | Read advisory, remediate, re-scan to verify |
| Issue tracker | Ticket tagged for implementation | Read acceptance criteria, implement, link PR |
| Alerting system | Production alert fires | Query logs via MCP, investigate, open fix PR |

**Configuration walkthrough:**

Event-driven automations are configured in Devin's Automations settings. The basic flow:

1. **Define the trigger** — Which event source (GitHub webhook, Jira webhook, PagerDuty webhook)?
2. **Define the filter** — Which events should trigger a session (e.g., only CI failures on `main`, only Jira tickets tagged `Devin:Implementation`)?
3. **Define the prompt template** — What should Devin do when the event fires? Use template variables to inject event data (e.g., `{{branch_name}}`, `{{failure_log_url}}`).
4. **Set safeguards** — Filter out Devin's own events (prevent infinite loops), set maximum retry counts, use idempotency keys.

**Safeguard checklist:**
- [ ] Filter out events from `devin-ai-integration[bot]` to prevent infinite loops
- [ ] Set maximum sessions per trigger (e.g., 3 retries per CI failure)
- [ ] Use idempotency keys to prevent duplicate sessions from the same event
- [ ] Test with a single event before enabling for all events

<a id="pattern-4-scheduled"></a>
## Pattern 4: Scheduled

**When to use:** For recurring maintenance that should happen on a cadence without human initiation.

**Common schedules:**

| Task | Cadence | Why Scheduled? |
|------|---------|---------------|
| Dependency version bumps | Weekly (Monday morning) | Catches outdated packages before they accumulate |
| Dead code detection and cleanup | Monthly | Keeps codebase lean without consuming sprint capacity |
| Documentation freshness check | After major releases | Ensures docs stay in sync with code |
| License compliance audit | Quarterly | Meets compliance requirements without manual effort |
| Security scanning + remediation | Weekly | Reduces vulnerability backlog continuously |

**Configuration walkthrough:**

Scheduled sessions are configured in Devin's settings or via the API:

1. **Define the schedule** — Cron expression or human-readable recurrence (weekly, daily, etc.)
2. **Define the prompt** — What Devin should do on each run. Include verification steps.
3. **Define the target repo** — Which repository to work against.
4. **Set expectations for output** — Should Devin open a PR even if no changes are needed? Should it update a tracking document?

**Example scheduled session prompt:**

```
Check all npm dependencies in my-frontend-app for
available minor and patch updates. Run npm update to
apply non-breaking upgrades. Run npm test and
npm run build to verify nothing is broken. If any
upgrade breaks the build, revert that specific
upgrade and document why. Create a
DEPENDENCY_UPDATES.md summarizing what was updated.
Title the PR "chore: weekly dependency bump".
```

---

<a id="knowledge-check"></a>
## Knowledge Check

**Knowledge Check 5.1**
Q: You need to apply a security patch to 30 microservices. Which orchestration pattern should you use?
A) Single agent — run one session per repo manually
B) Parent-child — one parent plans the campaign, spawns 30 children
C) Event-driven — wait for security alerts on each repo
D) Scheduled — run the patch on a weekly cadence

*Answer: B — This is a classic divide-and-conquer scenario. The parent agent identifies the targets and methodology, then spawns children that execute in parallel — each with its own VM, branch, and PR.*

**Knowledge Check 5.2**
Q: You want Devin to automatically fix CI failures on your `main` branch. Which pattern is this?
A) Single agent
B) Parent-child
C) Event-driven
D) Scheduled

*Answer: C — CI failures are external events that trigger Devin automatically. The event-driven pattern responds to webhooks from your CI system without human initiation.*

**Knowledge Check 5.3**
Q: When setting up event-driven automations, what is the most critical safeguard?
A) Setting a high concurrency limit for fast response
B) Filtering out Devin's own events to prevent infinite loops
C) Using detailed prompt templates
D) Connecting to multiple event sources simultaneously

*Answer: B — If Devin's own commits trigger CI, and CI failures trigger Devin, you get an infinite loop. Filtering out events from `devin-ai-integration[bot]` is the single most critical safeguard.*

**Knowledge Check 5.4**
Q: Your team wants dependency bumps done weekly without anyone remembering to trigger them. Which pattern fits?
A) Single agent — someone creates a session each Monday
B) Parent-child — a parent agent manages the schedule
C) Event-driven — triggered by new package releases
D) Scheduled — cron-based recurring session

*Answer: D — Scheduled sessions run on a cron expression without human initiation. Set it once and Devin opens a dependency bump PR every Monday morning automatically.*

<a id="key-takeaways"></a>
## Key Takeaways

- Single agent is the default — use it for most self-contained tasks
- Parent-child divides large campaigns across many targets; each child is independent with its own VM, branch, and PR
- Event-driven responds to external signals (CI failures, security findings, ticket assignments) automatically — no human creates the session
- Scheduled runs maintenance on a cadence (weekly dependency bumps, monthly dead code cleanup) without human initiation
- For event-driven patterns, always filter out Devin's own events to prevent infinite loops
