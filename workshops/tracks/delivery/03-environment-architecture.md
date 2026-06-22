# Environment & Architecture Decisions

<a id="toc"></a>
## Table of Contents

- [Ubuntu vs. Windows Host](#ubuntu-vs-windows-host)
- [Blueprint Configuration](#blueprint-configuration)
- [Network Connectivity](#network-connectivity)
- [Secret Management](#secret-management)
- [MCP Server Configuration](#mcp-server-configuration)
- [Knowledge Check](#knowledge-check)
- [Key Takeaways](#key-takeaways)

---

This section covers the tactical platform operations that determine whether Devin can build, test, and deliver in your project's environment.

<a id="ubuntu-vs-windows-host"></a>
## Ubuntu vs. Windows Host

Every Devin session runs on a virtual machine. The host OS determines what runtimes, build tools, and frameworks are available natively.

| Factor | Ubuntu (Linux) | Windows |
|--------|---------------|---------|
| **Default** | Yes — most sessions run on Ubuntu | Must be explicitly configured |
| **Language support** | Java, Python, Node.js, Go, Rust, Ruby, C/C++ | .NET Framework, PowerShell-dependent tooling, Windows-only SDKs |
| **.NET** | .NET Core / .NET 5+ (cross-platform) | .NET Framework 4.x (Windows-only) |
| **Build tools** | make, cmake, gradle, maven, npm, pip, cargo | MSBuild, NuGet, Visual Studio Build Tools |
| **Shell** | Bash | PowerShell, cmd |
| **Container support** | Docker native | Docker Desktop (WSL2 backend) |
| **Use when** | Most projects — it is the default for a reason | Legacy .NET Framework, PowerShell-heavy automation, Windows-specific tooling (e.g., certain IIS configurations, COM interop) |

**Decision matrix:**

```
Is the project .NET Framework (not .NET Core/5+)?
    ├── YES → Windows host
    └── NO
        │
Does the build require PowerShell or Windows-only tools?
    ├── YES → Windows host
    └── NO
        │
Does the project use COM interop or Windows-specific APIs?
    ├── YES → Windows host
    └── NO → Ubuntu host (default)
```

When in doubt, use Ubuntu. It covers the vast majority of modern development stacks and is the default for Devin sessions.

<a id="blueprint-configuration"></a>
## Blueprint Configuration

Blueprints are YAML configurations that define what is pre-installed on Devin's VM. They have two sections with distinct purposes:

| Section | When It Runs | What It's For | Persisted? |
|---------|-------------|---------------|------------|
| **`initialize`** | Once, when the snapshot is built | Installing software, compiling tools, downloading large artifacts | Yes — baked into the VM snapshot |
| **`maintenance`** | Every session start (after snapshot restore) | Refreshing ephemeral state: rotating tokens, updating caches, re-authenticating | No — runs fresh each time |

**What goes where:**

```yaml
# initialize — runs once, persists in snapshot
initialize:
  - apt-get update && apt-get install -y postgresql-client redis-tools
  - curl -fsSL https://deb.nodesource.com/setup_20.x | bash -
  - apt-get install -y nodejs
  - npm install -g pnpm@9
  - pip install pre-commit

# maintenance — runs every session start
maintenance:
  - aws sts get-caller-identity  # verify AWS creds are valid
  - gcloud auth print-access-token > /dev/null  # verify GCP auth
  - npm config set registry https://your-private-registry.com
```

**Org-level vs. repo-level blueprints:**

| Scope | When to Use | Example |
|-------|------------|---------|
| **Org-level** | Tools and runtimes shared across many repos | Node.js 20, Java 17, Docker, `pre-commit` |
| **Repo-level** | Project-specific dependencies and configurations | Gradle wrapper, specific Python packages, custom build scripts |

Org-level blueprints run *before* repo-level blueprints. A common pattern: org-level installs the language runtime; repo-level installs project dependencies.

**Testing blueprint changes:** Blueprint changes create a new snapshot build. You can verify by checking the build log URL provided when the snapshot is built. If a command fails during snapshot build, the snapshot will not update — the previous working snapshot remains active.

<a id="network-connectivity"></a>
## Network Connectivity

Devin's VM needs network access to reach your private resources (databases, APIs, artifact registries). The three common patterns:

| Pattern | How It Works | When to Use |
|---------|-------------|------------|
| **SSM Port Forwarding** (AWS) | Creates an encrypted tunnel from Devin's VM to a private VPC resource via AWS Systems Manager | RDS databases, internal APIs behind private subnets. No public IP required on the target. |
| **Cloud SQL Auth Proxy** (GCP) | Google's binary creates an mTLS tunnel to Cloud SQL instances | Any GCP Cloud SQL database. Handles certificate rotation automatically. |
| **VPN / Static Egress IPs** | Route Devin's traffic through a VPN connection or allowlist Devin's static egress IPs on your firewall | On-premise resources, third-party APIs with IP allowlisting, corporate network resources behind a VPN gateway |

**The three-layer connectivity model:**

1. **Network Path** — How does traffic get from Devin's VM to the target? (tunnel, VPN, direct internet)
2. **Transport Security** — Is the connection encrypted? (TLS, mTLS, SSH tunnel)
3. **Identity & Authentication** — How does Devin prove who it is? (IAM database auth, service account tokens, username/password from secrets)

For detailed configuration guides, see the [network connectivity patterns in automations-and-integrations](https://github.com/Devin-Samples/automations-and-integrations/tree/main/network-connectivity).

<a id="secret-management"></a>
## Secret Management

Secrets are credentials (API keys, database passwords, service account tokens) injected into Devin's session environment. They are never stored in prompts or code.

| Scope | Visibility | When to Use |
|-------|-----------|------------|
| **Org-scoped** | Available to all sessions in the org | Shared infrastructure credentials: artifact registry tokens, CI/CD service accounts, monitoring API keys |
| **User-scoped** | Available only to sessions created by one user | Personal access tokens, individual developer credentials |
| **Repo-scoped** | Available only to sessions working on a specific repo | Repo-specific API keys, database credentials for a specific service |

**How to reference secrets in prompts and blueprints:**

Use the `${SECRET_NAME}` syntax. The platform substitutes the actual value at runtime — you never see or type the real credential.

```yaml
# In a blueprint maintenance block:
maintenance:
  - echo "machine your-registry.com login _token password ${ARTIFACT_REGISTRY_TOKEN}" > ~/.netrc
```

**TOTP/2FA secrets:** For services requiring two-factor authentication, store TOTP secrets with a `_2FA_` prefix (e.g., `_2FA_JIRA_TOTP`). Devin generates time-based codes automatically at login.

**Best practice:** Use the narrowest scope possible. Org-scoped secrets are convenient but increase blast radius if compromised. Default to repo-scoped unless multiple repos need the same credential.

<a id="mcp-server-configuration"></a>
## MCP Server Configuration

MCP (Model Context Protocol) servers extend Devin's capabilities by connecting it to external tools — issue trackers, observability platforms, documentation systems, and databases.

| MCP Server | What It Enables | Example Use |
|------------|----------------|-------------|
| **Jira** | Read tickets, update status, link PRs | Devin reads acceptance criteria from a Jira ticket, implements, and links the PR back |
| **Datadog** | Query logs, traces, and metrics | Devin investigates an incident by querying Datadog for error patterns |
| **Confluence** | Read and update documentation | Devin reads architecture docs before implementing, updates docs after |
| **Azure DevOps** | Read work items, manage pipelines | Devin reads ADO work items and updates them with PR links |
| **Databases** (PostgreSQL, MySQL, etc.) | Query schemas, run migrations, validate data | Devin checks schema before writing migration scripts |

**Configuration:** MCP servers are configured once at the organization level in Devin's settings. Once configured, they are available to every session automatically — no per-session setup required.

**How they help:** Without MCP servers, Devin can only work with what is in the Git repository. With MCP servers, Devin can:
- Read the Jira ticket that defines the task (instead of you copy-pasting requirements into the prompt)
- Query production logs to understand an incident (instead of you pasting log snippets)
- Check database schemas before writing migrations (instead of guessing or relying on outdated ERD diagrams)
- Update documentation in Confluence after implementing a change (closing the docs-drift gap)

---

<a id="knowledge-check"></a>
## Knowledge Check

**Knowledge Check 3.1**
Q: Your project uses .NET Framework 4.8 with MSBuild. Which Devin host OS should you configure?
A) Ubuntu — it is the default and supports .NET
B) Windows — .NET Framework 4.x requires Windows
C) Either — .NET Framework runs on both
D) Ubuntu with Wine for .NET compatibility

*Answer: B — .NET Framework (as opposed to .NET Core/.NET 5+) is Windows-only. You must configure a Windows host for projects that depend on it.*

**Knowledge Check 3.2**
Q: You need to install Node.js 20 and pnpm for every session across 15 repos. Where should this go in the blueprint?
A) Repo-level `maintenance` block for each repo
B) Org-level `initialize` block
C) Repo-level `initialize` block for each repo
D) Org-level `maintenance` block

*Answer: B — Tools shared across many repos belong in the org-level `initialize` block. They are installed once when the snapshot is built and persist across sessions. Putting them in `maintenance` would reinstall them every session start, wasting time.*

**Knowledge Check 3.3**
Q: You have a database password that only the `order-service` repo needs. What secret scope should you use?
A) Org-scoped — for convenience
B) User-scoped — it is your credential
C) Repo-scoped — narrowest scope that covers the need
D) Store it in the repository's `.env` file

*Answer: C — Use the narrowest scope possible. Repo-scoped secrets are only available to sessions working on that specific repo, minimizing blast radius. Never store secrets in files committed to the repo.*

**Knowledge Check 3.4**
Q: You configure a Jira MCP server at the org level. How do individual sessions access it?
A) Each session must be configured to use the MCP server
B) Users include a flag in their prompt to enable MCP
C) It is automatically available to every session — no per-session setup
D) Only sessions created by the user who configured it can use it

*Answer: C — MCP servers configured at the org level are part of Devin's shared context layer. They are automatically available to every session without additional configuration.*

<a id="key-takeaways"></a>
## Key Takeaways

- Use Ubuntu by default; use Windows only for .NET Framework, PowerShell-dependent tooling, or Windows-only APIs
- Blueprint `initialize` runs once (persists in snapshot); `maintenance` runs every session start (for ephemeral state)
- Use org-level blueprints for shared tools; repo-level for project-specific dependencies
- Reference secrets with `${SECRET_NAME}` — never put credentials in prompts or code
- MCP servers extend Devin beyond Git: issue trackers, observability, documentation, databases — configured once, available everywhere
