# 5. Full Platform Configuration

<a id="toc"></a>

- [5.1 Git Connections](#51-git-connections)
- [5.2 Environment Blueprints](#52-environment-blueprints)
- [5.3 DeepWiki](#53-deepwiki)
- [5.4 Knowledge Notes](#54-knowledge-notes)
- [5.5 Playbooks](#55-playbooks)
- [5.6 MCP Servers](#56-mcp-servers)
- [5.7 Secrets Management](#57-secrets-management)
- [5.8 Automations](#58-automations)
- [5.9 Configuration Order Summary](#59-configuration-order-summary)
- [Knowledge Checks](#knowledge-checks)
- [Key Takeaways](#key-takeaways)

---

This section walks through configuring a complete Devin organization from scratch — every component of the shared context layer in the order you should set them up.

## 5.1 Git Connections

**What it is:** Repository access configured at the org level. Once connected, every session in the organization can clone and push to connected repos immediately.

**When to configure:** First. Without Git connections, Devin cannot access any code.

**Configuration steps:**
1. Navigate to Settings → Git Connections in the Devin web app
2. Connect your Git provider (GitHub, GitLab, Azure DevOps, Bitbucket)
3. Select which repositories or organizations Devin can access
4. Verify access by starting a test session that clones a repo

**Example configuration:**
- Connect the organization's GitHub account
- Grant access to specific repositories (not all — follow least-privilege)
- Verify with: start a session and ask "Clone `<org>/<repo>` and list the top-level directory structure"

**Common mistakes:**
- Granting access to all repos when only a subset is needed
- Forgetting to connect repos in secondary organizations (e.g., shared libraries in a different GitHub org)
- Not verifying the connection works before configuring downstream components

## 5.2 Environment Blueprints

**What it is:** YAML configuration defining the VM setup for Devin sessions — dependencies, language runtimes, tools, startup scripts. Sessions boot from cached snapshots built from these blueprints.

**When to configure:** Second. After Git connections, configure the environment so sessions start ready to build.

**Configuration steps:**
1. Navigate to Settings → Environment in the Devin web app
2. Create a blueprint with `initialize` (persistent software install) and `maintenance` (per-session startup) sections
3. Build a snapshot from the blueprint
4. Test by starting a session and verifying all tools are available

**Example configuration:**

```yaml
initialize:
  - apt-get update && apt-get install -y postgresql-client redis-tools
  - curl -fsSL https://deb.nodesource.com/setup_20.x | bash -
  - apt-get install -y nodejs
  - npm install -g pnpm@9

maintenance:
  - echo "Session started at $(date)" >> /tmp/session.log
```

**Common mistakes:**
- Putting ephemeral state (database connections, API tokens) in `initialize` instead of `maintenance`
- Not testing the snapshot build — a broken blueprint blocks all future sessions
- Including secrets directly in the blueprint YAML (use `$VARIABLE_NAME` syntax instead)
- Forgetting that `initialize` runs once (at snapshot build time) while `maintenance` runs at every session start

## 5.3 DeepWiki

**What it is:** Auto-generated architectural documentation for indexed repositories. When Devin connects to a repo, DeepWiki indexes the codebase and produces navigable documentation covering structure, key components, patterns, and dependencies — giving Devin codebase understanding before it reads a single source file. Coverage depends on repo structure and complexity.

**When to configure:** Automatic upon Git connection. Verify indexing has completed for critical repos before relying on context retrieval.

**Configuration steps:**
1. Connect repos via Git Connections (Section 5.1) — DeepWiki indexes automatically
2. Navigate to DeepWiki in the Devin web app to verify documentation has been generated
3. Review the generated pages for key repos — confirm accuracy of architectural descriptions
4. For private or complex repos, verify that DeepWiki captured the correct module boundaries and dependencies

**What DeepWiki provides:**
- Repository structure overview (directory layout, entry points)
- Component relationship maps (which modules depend on which)
- Pattern identification (frameworks used, architectural style)
- API surface documentation (endpoints, interfaces, contracts)

**Common mistakes:**
- Assuming DeepWiki has indexed a repo immediately after connecting — allow time for processing
- Not verifying DeepWiki accuracy for domain-specific or unconventional repo structures
- Forgetting that DeepWiki coverage depends on repo structure — very large monorepos or repos with minimal documentation may have partial coverage

## 5.4 Knowledge Notes

**What it is:** Persistent, human-curated context — coding standards, architecture decisions, team conventions, domain glossary — that Devin retrieves automatically based on task relevance.

**When to configure:** After the environment is ready, add the context that makes Devin's output match your team's standards.

**Configuration steps:**
1. Navigate to Settings → Knowledge in the Devin web app
2. Create notes with a trigger description (when should this note be retrieved?) and content (what should Devin know?)
3. Start a session on a relevant task and verify the Knowledge note appears in context

**Example configuration:**

| Note Name | Trigger | Content |
|-----------|---------|---------|
| "Coding Standards" | "When writing code in any repo" | "Use TypeScript strict mode. Prefer functional components. Import from `@/lib` not relative paths..." |
| "API Conventions" | "When creating or modifying API endpoints" | "Use RESTful naming. Return 404 with JSON body. Pagination via cursor, not offset..." |
| "Testing Policy" | "When writing or modifying tests" | "Unit tests use Vitest. E2E tests use Playwright. Minimum 80% branch coverage for new code..." |

**Common mistakes:**
- Writing notes that are too broad (trigger: "always") — they dilute relevance
- Writing notes that are too specific (trigger: "when modifying `src/auth/login.ts`") — they rarely fire
- Not including concrete examples in the note content — abstract guidance produces abstract code
- Duplicating information that already exists in the codebase (README, CONTRIBUTING.md)

## 5.5 Playbooks

**What it is:** Repeatable, step-by-step procedures that encode institutional methodology. When Devin follows a playbook, every session applies the same process regardless of who triggers it.

**When to configure:** After Knowledge notes define the "what" (standards), Playbooks define the "how" (process).

**Configuration steps:**
1. Navigate to Settings → Playbooks in the Devin web app (or commit a `.devin/playbooks/` file in the repo)
2. Write the playbook as a step-by-step procedure with verification criteria at each step
3. Test by invoking the playbook in a session and verifying it follows every step

**Example configuration:**

A security remediation playbook:
1. Run the configured SAST tool and capture output
2. Parse findings — filter to HIGH and CRITICAL severity
3. For each finding: identify the correct file, determine the fix type (dependency upgrade vs. code change)
4. Apply the fix in the correct manifest or source file
5. Run affected tests locally
6. Push the fix
7. Verify CI re-scan shows reduced findings
8. If findings persist after 2 iterations, open an escalation issue

**Common mistakes:**
- Writing vague steps ("fix the issue") instead of concrete actions ("upgrade the dependency in `package.json` to the latest patch version")
- Omitting verification steps — without "run tests after fixing," the playbook may produce untested code
- Not specifying escalation criteria — playbooks that do not define "when to stop" can loop
- Making playbooks too long — break complex workflows into composable sub-playbooks

## 5.6 MCP Servers

**What it is:** Model Context Protocol — pre-configured integrations that let Devin call external tools (Jira, Datadog, Confluence, Azure DevOps, Slack) as if they were local. Configured once, available to every session.

**When to configure:** After the core development workflow is set up, add the external tool integrations your team uses.

**Configuration steps:**
1. Navigate to Settings → MCP Servers in the Devin web app
2. Add the server configuration (connection URL, authentication)
3. Test by starting a session and asking Devin to query the integrated tool

**Example configuration:**

| MCP Server | Purpose | Use Cases |
|-----------|---------|-----------|
| Jira | Issue tracking | Read ticket details, update status, link PRs |
| Datadog | Observability | Query logs and traces during incident investigation |
| Confluence | Documentation | Read existing docs for context, update docs as part of implementation |
| Azure DevOps | Work items + pipelines | Read work items, trigger pipelines, check build status |

**Common mistakes:**
- Not testing the MCP connection before using it in a production workflow
- Granting broader permissions than needed (e.g., write access to Jira when only read is required)
- Configuring MCP servers that require VPN access without setting up network connectivity first
- Not updating MCP server credentials when they rotate

## 5.7 Secrets Management

**What it is:** Scoped credentials (API keys, service account tokens, database passwords) injected into session environments at start. Secrets never appear in prompts, logs, or code.

**When to configure:** After integrations are identified, provision the credentials they require.

**Configuration steps:**
1. Navigate to Settings → Secrets in the Devin web app
2. Add secrets with appropriate scope (org-wide, user-specific, or repo-specific)
3. Reference secrets in blueprints as `$SECRET_NAME` — never paste values directly
4. Verify by starting a session and confirming the secret is available as an environment variable

**Example configuration:**

| Secret Name | Scope | Purpose |
|-------------|-------|---------|
| `GITHUB_TOKEN` | Org | Git operations (typically auto-configured via Git connections) |
| `DATABRICKS_TOKEN` | Org | Databricks workspace access for data engineering tasks |
| `SONAR_TOKEN` | Repo | SonarQube/SonarCloud scanning for specific repos |
| `JIRA_API_TOKEN` | Org | Jira MCP server authentication |

**Common mistakes:**
- Using personal tokens instead of service account credentials (creates dependency on individual employees)
- Setting org-wide scope for secrets that should be repo-specific (violates least-privilege)
- Embedding secret values directly in blueprint YAML instead of using `$SECRET_NAME` references
- Not rotating secrets on a schedule — service account tokens expire

## 5.8 Automations

**What it is:** Webhooks, scheduled sessions, and trigger rules configured at the organization level. Automations create Devin sessions in response to external events without human initiation.

**When to configure:** Last. After everything else is in place, add the automation layer that ties it together.

**Configuration steps:**
1. Navigate to Automations in the Devin web app
2. Describe the automation in natural language (trigger, action, safeguards)
3. Configure trigger conditions (which events, which repos, which branches)
4. Set safeguards (invocation limits, ACU caps)
5. Test by triggering the event and verifying the automation creates a session

**Example configuration:**

```
When a security scan check run fails on any repo in the
organization, start a Devin session that reads the scan
findings, triages HIGH and CRITICAL issues, remediates
them, runs tests, and pushes the fix.

Conditions:
- Only trigger on check_run conclusion = failure
- Only for check runs named "security-scan"
- Skip if PR author is devin-ai-integration[bot]

Safeguards:
- Max 3 invocations per PR per hour
- 50 ACU limit per session
- Escalate after 2 failed attempts
```

**Common mistakes:**
- No invocation limits — allows runaway loops
- No ACU caps — unbounded cost per session
- Missing loop prevention — Devin's own pushes re-trigger the automation
- Overly broad trigger conditions (all repos, all events) — creates noise
- No escalation policy — automation retries forever on unfixable issues

## 5.9 Configuration Order Summary

```
1. Git Connections     → Devin can access code
2. Environment         → Sessions boot ready to build
3. DeepWiki            → Devin understands the codebase architecture
4. Knowledge           → Devin follows your standards
5. Playbooks           → Devin follows your processes
6. MCP Servers         → Devin integrates with your tools
7. Secrets             → Devin authenticates to your systems
8. Automations         → Devin responds to events autonomously
```

Each layer depends on the previous ones. Automations (8) only work if Devin can access code (1), build it (2), understand it (3), follow standards (4), execute processes (5), query tools (6), and authenticate (7).

---

## Knowledge Checks

**Knowledge Check 5.1**
Q: A client asks: "We configured a Devin Automation but it is not firing when our CI fails. What should we check?" What is the most likely issue?
A) "Devin is broken"
B) "Check the trigger conditions — the check run name in the automation filter may not match the actual CI job name, or the event type (check_run vs. workflow_run) may be wrong"
C) "Automations do not work with CI"
D) "The secret expired"
*Answer: B — Trigger condition mismatch is the most common automation issue. The check run name, event type, repo filter, or branch filter does not match the actual event being emitted.*

**Knowledge Check 5.2**
Q: Where should database connection passwords be stored in a Devin organization?
A) "Directly in the environment blueprint YAML"
B) "In a Knowledge note"
C) "In the Secrets management layer, referenced in blueprints as $SECRET_NAME"
D) "In the playbook"
*Answer: C — Secrets must be stored in the Secrets management layer and referenced by variable name. Never embed secret values directly in blueprints, Knowledge notes, or playbooks.*

**Knowledge Check 5.3**
Q: What is the difference between `initialize` and `maintenance` in an environment blueprint?
A) "They are the same"
B) "`initialize` runs once at snapshot build time (persistent installs); `maintenance` runs at every session start (ephemeral state refresh)"
C) "`initialize` is for secrets; `maintenance` is for tools"
D) "`maintenance` runs weekly; `initialize` runs once"
*Answer: B — `initialize` installs persistent software (baked into the snapshot); `maintenance` handles per-session startup (e.g., refreshing tokens, starting background services). Putting ephemeral state in `initialize` causes stale data.*

**Knowledge Check 5.4**
Q: In what order should you configure a new Devin organization?
A) "Automations first, then Git connections"
B) "Git Connections → Environment → DeepWiki → Knowledge → Playbooks → MCP Servers → Secrets → Automations"
C) "Secrets first, then everything else"
D) "Any order is fine"
*Answer: B — Each layer depends on the previous ones. Automations (last) only work if Devin can access code (first), build it, understand it, follow standards, execute processes, query tools, and authenticate.*

## Key Takeaways

- **Configuration is a one-time investment** — one engineer sets up the shared context layer; every subsequent session from any team member inherits it
- **Order matters:** Git access → build environment → DeepWiki → knowledge → playbooks → integrations → secrets → automations. Each layer depends on the previous ones
- **Secrets never go in plaintext** — use `$SECRET_NAME` references in blueprints, scope secrets to minimum necessary access, use service accounts over personal tokens
- **Test each layer before proceeding** — a broken blueprint blocks all sessions; a misconfigured MCP server causes silent failures. Verify each component works before building on top of it
