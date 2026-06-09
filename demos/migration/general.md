# Framework & API Migration with Devin — General Demo

Devin migrates legacy frameworks and APIs — MuleSoft, Struts, EJB, WCF, SOAP,
and similar — to modern equivalents (Spring Boot, .NET Core, REST/gRPC, React,
etc.) with contract verification. The universal pattern: orient, convert, verify,
fan out.

## Table of Contents

- [The Pattern](#the-pattern)
- [The Verification Loop](#the-verification-loop)
- [Running It Live](#running-it-live)
- [Scaling](#scaling)
- [Key Takeaways](#key-takeaways)

---

<a id="the-pattern"></a>
## The Pattern

Every framework migration follows the same five-step sequence, regardless of
source or target technology:

### 1. Orient

Devin reads the legacy codebase, maps endpoints/flows/components, and identifies
framework-specific constructs. It builds an internal model of the API surface:
route paths, request/response schemas, middleware, data access patterns, and
authentication flows.

### 2. Convert one unit live

Translate one endpoint, controller, or component to the target framework —
complete with tests. This first conversion establishes the mapping between old
and new: naming conventions, dependency injection style, error handling idioms,
and DTO patterns.

### 3. Contract verification

Tests run against an API specification (OpenAPI, WSDL, or contract tests) to
prove behavioral parity. The spec is the source of truth — endpoint existence,
request/response schemas, HTTP status codes, and error formats must match.

### 4. Catch divergences

When contract tests fail, Devin investigates the source, identifies the gap
between legacy behavior and the new implementation, and fixes it. This loop
repeats until the converted unit passes all contract checks.

### 5. Fan out in parallel

Child sessions convert remaining units concurrently using the proven playbook
from steps 1–4. Each child works independently — its own VM, its own branch, its
own PR — following the same conversion procedure.

---

<a id="the-verification-loop"></a>
## The Verification Loop

The verification loop is what distinguishes an AI-driven migration from a
best-effort code translation. Without it, you get code that looks right but may
not behave identically.

**How it works:**

1. The API specification (OpenAPI, WSDL, RAML, or a hand-written contract test
   suite) defines the expected behavior
2. Each converted endpoint is tested against this spec immediately after
   conversion
3. Failures trigger investigation: Devin reads the original framework's source
   to understand the intended behavior, then adjusts the new implementation
4. The loop continues until the converted unit passes — only then does Devin
   move to the next unit or open a PR

**What it proves:**

- Endpoint existence and routing
- Request/response schema conformance
- HTTP status codes and error formats
- Authentication and authorization behavior
- Query parameter and path variable handling

---

<a id="running-it-live"></a>
## Running It Live

Start by pointing Devin at the legacy codebase and the target project scaffold.
The scaffold should compile but have no (or minimal) business logic — Devin
fills in the implementation.

**Orient over the legacy estate:**

```
Read the legacy codebase in <source-repo>. Map every
endpoint/flow/component: route paths, HTTP methods, request and
response schemas, authentication mechanisms, and data access
patterns. Produce a summary of the API surface.
```

**Convert a single endpoint with verification:**

```
Using the API surface map, convert the <endpoint-name> endpoint
from <source-framework> to <target-framework> in <target-repo>.
Write the controller, service, and repository layers. Run the
contract tests in <test-directory> to verify parity against the
API spec at <spec-path>. Fix any failures before finishing.
```

**Fan out the remaining endpoints:**

```
Using the playbook at <playbook-path>, convert all remaining
endpoints from <source-framework> to <target-framework>. Spawn a
child session for each endpoint. Each child should follow the same
convert-then-verify pattern and open its own PR.
```

---

<a id="scaling"></a>
## Scaling

### Playbooks as reusable conversion procedures

Once the first endpoint conversion succeeds, the methodology is captured in a
playbook — a step-by-step procedure that encodes the mapping rules, test
commands, and verification criteria. Every subsequent conversion follows this
playbook, producing consistent results regardless of which team member (or child
session) executes it.

### Child sessions for parallel endpoint conversion

A parent session breaks the remaining work into independent units (one per
endpoint, controller, or module) and spawns a child session for each. Children
run concurrently on separate VMs, each opening its own PR. Failures in one child
do not affect others — the parent monitors progress and retries or escalates as
needed.

This pattern scales linearly: a 50-endpoint migration runs 50 children in
parallel, each completing independently and producing a verified PR.

---

<a id="key-takeaways"></a>
## Key Takeaways

- **One pattern fits many migrations** — MuleSoft, Struts, EJB, WCF, SOAP — the
  orient/convert/verify/fan-out sequence applies universally
- **Contract tests are the safety net** — the API spec is the source of truth;
  without verification, a migration is a guess
- **Playbooks make it repeatable** — capture the conversion procedure once, apply
  it to every unit
- **Child sessions make it parallel** — each endpoint converts independently,
  scaling linearly with the number of units
- **The scaffold is the starting state** — begin with a compiling but empty
  target project; Devin fills in the implementation

---

For a complete worked example, see
[MuleSoft → Spring Boot](use-cases/mulesoft-to-spring-boot-demo.md).

**Other variants:** [Desktop + Cloud](general.platform.md)
