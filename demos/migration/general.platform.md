# Framework & API Migration with Devin — Desktop + Cloud Demo

Devin migrates legacy frameworks and APIs — MuleSoft, Struts, EJB, WCF, SOAP,
and similar — to modern equivalents (Spring Boot, .NET Core, REST/gRPC, React,
etc.) with contract verification. This variant uses Devin Desktop as the primary
interface: explore legacy code locally, delegate conversions to Cloud, monitor
parallel work in the Agent Command Center, and review migration PRs with
one-click checkout.

## Table of Contents

- [The Pattern (Desktop + Cloud)](#the-pattern)
- [The Verification Loop](#the-verification-loop)
- [Running It Live](#running-it-live)
- [Scaling](#scaling)
- [Key Takeaways](#key-takeaways)

---

<a id="the-pattern"></a>
## The Pattern (Desktop + Cloud)

The same five-step sequence as the Cloud-only variant, but with Desktop as the
orchestration layer:

### 1. Orient — explore locally with Cascade

Open the legacy codebase in Devin Desktop. Use Cascade (the in-IDE agent) to
navigate framework-specific constructs: XML flow definitions, WSDL bindings,
EJB descriptors, or route configuration files. Cascade provides inline
explanations and builds your mental model before delegating any work to Cloud.

### 2. Convert one unit — delegate to Cloud

From Desktop, delegate the first endpoint conversion to a Cloud session. The
Cloud agent runs on its own VM with the full build toolchain — compiling,
running tests, and iterating until the contract tests pass. Monitor progress in
the Agent Command Center (Kanban view) without leaving your IDE.

### 3. Contract verification

The Cloud session runs contract tests against the API spec (OpenAPI, WSDL, or
contract test suite) and reports back. Review results directly in the Agent
Command Center — green checks appear on the session card when verification
passes.

### 4. Catch divergences

When contract tests fail, the Cloud session investigates and fixes
automatically. Use one-click PR checkout in Desktop to pull the in-progress
branch locally and compare the old framework code side-by-side with the new
implementation if you want to inspect the fix before it lands.

### 5. Fan out in parallel — monitor in Agent Command Center

Delegate remaining endpoints to Cloud as parallel child sessions. The Agent
Command Center shows each conversion as a card on the Kanban board — track
progress across the full migration from a single view. Use Spaces to organize
the migration workstream: group related sessions, track PRs, and manage review
queues.

---

<a id="the-verification-loop"></a>
## The Verification Loop

Identical to the Cloud-only variant — the API spec is the source of truth and
contract tests prove behavioral parity. The difference is visibility: Desktop
surfaces test results and PR status directly in the IDE, so you see pass/fail
without switching to the browser.

**What it proves:**

- Endpoint existence and routing
- Request/response schema conformance
- HTTP status codes and error formats
- Authentication and authorization behavior
- Query parameter and path variable handling

**Desktop advantage:** One-click checkout lets you diff the old framework
implementation against the new one locally — useful for understanding edge cases
the contract tests catch.

---

<a id="running-it-live"></a>
## Running It Live

### Explore the legacy codebase in Desktop

Open the source repo in Devin Desktop. Use Cascade to ask questions about the
legacy framework constructs:

```
Explain the flow definitions in src/main/mule/. Map each flow
to its HTTP endpoint, method, and response schema. Which flows
share data access components?
```

Cascade responds inline — no context-switching to a browser or separate tool.

### Delegate the first conversion to Cloud

From Desktop, delegate the conversion task to a Cloud session:

```
Convert the <endpoint-name> endpoint from <source-framework>
to <target-framework> in <target-repo>. Write the controller,
service, and repository layers. Run the contract tests in
<test-directory> to verify parity against the API spec at
<spec-path>. Fix any failures before finishing.
```

The Cloud session appears in the Agent Command Center. Monitor its progress
while continuing other work in the IDE.

### Fan out remaining endpoints from Desktop

Once the first conversion verifies, delegate the remaining endpoints as
parallel Cloud sessions:

```
Using the playbook at <playbook-path>, convert all remaining
endpoints from <source-framework> to <target-framework>. Spawn
a child session for each endpoint. Each child should follow the
same convert-then-verify pattern.
```

Each child appears as a separate card in the Agent Command Center Kanban board.

### Review migration PRs with one-click checkout

As PRs land, use one-click checkout in Desktop to pull each branch locally.
Compare the original framework code side-by-side with the new implementation.
Approve, request changes, or comment — all from the IDE.

---

<a id="scaling"></a>
## Scaling

### Playbooks as reusable conversion procedures

The first successful conversion becomes a playbook — a step-by-step procedure
that encodes the mapping rules, test commands, and verification criteria. Every
subsequent Cloud session follows this playbook, producing consistent results.

### Spaces for migration workstream organization

Use Spaces in Desktop to group migration-related sessions, PRs, and notes into
a single workspace. Track which endpoints are converted, which are in progress,
and which still need assignment — all from one view.

### Agent Command Center for parallel monitoring

The Kanban view shows every child session's status at a glance. Failed
conversions surface immediately — click through to inspect logs, retry, or
escalate. No need to open multiple browser tabs or track session URLs manually.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **Desktop as orchestration layer** — explore legacy code locally, delegate
  heavy conversion work to Cloud, review results without context-switching
- **Agent Command Center for visibility** — Kanban view shows parallel
  conversion progress at a glance
- **One-click checkout for review** — pull migration PRs locally to diff old
  vs. new framework code side-by-side
- **Spaces for organization** — group the entire migration workstream in one
  workspace
- **Same verification approach** — contract tests run in Cloud, results
  surface in Desktop

---

For a complete worked example, see
[MuleSoft → Spring Boot](use-cases/mulesoft-to-spring-boot-demo.md).

**Other variants:** [Cloud only](general.md)
