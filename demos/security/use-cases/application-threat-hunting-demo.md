# Application Security Threat Hunting with Runtime Verification

Devin proactively identifies multi-step attack chains in application code,
confirms exploitability using its runtime environment, then remediates and
proves the fix — before any scanner flags the vulnerability.

---

## Table of Contents

- [What Makes This Different](#what-makes-this-different)
- [Prerequisites](#prerequisites)
- [Act 1: Frame the Threat Model](#act-1-frame-the-threat-model)
- [Act 2: Launch Threat Analysis](#act-2-launch-threat-analysis)
- [Act 3: Runtime Exploitation Confirmation](#act-3-runtime-exploitation-confirmation)
- [Act 4: Remediate and Verify](#act-4-remediate-and-verify)
- [Key Takeaways](#key-takeaways)

---

<a id="what-makes-this-different"></a>
## What Makes This Different

Static analysis (SAST) tools find known patterns — SQL injection, XSS,
hardcoded secrets. They cannot reason about **multi-step attack chains** that
span multiple code paths, services, or authorization boundaries.

Devin has two properties that change the threat model:

1. **Intelligence** — traces call paths across controllers, middleware, and
   service layers to identify exploitable sequences that no single-pattern
   scanner would flag.
2. **Runtime environment** — executes the application, constructs the attack
   chain, and confirms exploitability programmatically. This is not theoretical
   analysis; it is demonstrated exploitation in a controlled dev environment.

The combination means: identify → confirm → remediate → re-test, all in one
session with evidence at each step.

---

<a id="prerequisites"></a>
## Prerequisites

- A repository with a running web application (API or full-stack)
- The application must be buildable and runnable in Devin's environment
  (Docker Compose or native build)
- An environment blueprint that boots the application on session start

Any application with authentication, authorization, and internal service
communication is suitable. Multi-service architectures surface more
interesting chains.

---

<a id="act-1-frame-the-threat-model"></a>
## Act 1: Frame the Threat Model

Open the repository. Note the surface area:

- Authentication mechanism (JWT, session cookies, OAuth)
- Authorization model (role-based, resource-based, none)
- Internal service communication (REST between services, message queues)
- Input validation boundaries (controllers, middleware, DTOs)
- Data access patterns (direct DB queries, ORM, stored procedures)

These are the boundaries where attack chains form. A vulnerability at one
boundary becomes exploitable when it chains to another.

Common multi-step attack patterns to look for:

| Chain | Steps | Impact |
|-------|-------|--------|
| SSRF → Internal API → Data exfiltration | Unvalidated URL input → internal service call → sensitive data returned | Confidentiality breach |
| Auth bypass → Privilege escalation | Missing auth check on internal endpoint → admin-level access | Full system compromise |
| IDOR → Bulk data access | Predictable resource IDs → no ownership check → enumerate all records | Data breach |
| Open redirect → Token theft | Unvalidated redirect → OAuth callback manipulation → token capture | Account takeover |
| Mass assignment → Role elevation | Unprotected fields in update endpoint → modify own role | Privilege escalation |

---

<a id="act-2-launch-threat-analysis"></a>
## Act 2: Launch Threat Analysis

Prompt Devin to analyze the application for exploitable attack chains:

```
Analyze this application for multi-step security attack chains.

Focus areas:
1. Authentication and authorization boundaries — are there
   endpoints missing auth checks that are reachable from
   authenticated paths?
2. Input validation — are there paths where user input flows
   to internal service calls without validation?
3. Resource access — are there IDOR vulnerabilities where
   predictable IDs allow access to other users' data?
4. Redirect and callback handling — are there open redirects
   that could be chained with OAuth flows?

For each finding:
- Describe the full attack chain (step by step)
- Identify the affected files and line numbers
- Rate the severity (Critical/High/Medium)
- Explain why a pattern-matching scanner would miss this

Produce THREAT_ANALYSIS.md with your findings.
```

Observe Devin's approach:

- Reads authentication middleware to understand how auth is enforced
- Maps which endpoints have auth checks and which don't
- Traces input parameters from controllers through to database queries
- Identifies internal-only endpoints that are reachable via chained calls
- Checks redirect handling for validation gaps

The output is `THREAT_ANALYSIS.md` — a structured report with each attack
chain documented, severity rated, and scanner-gap explained.

---

<a id="act-3-runtime-exploitation-confirmation"></a>
## Act 3: Runtime Exploitation Confirmation

This is the differentiating step. Devin has a runtime environment — it can
actually **execute** the attack chain against the running application.

Prompt Devin to confirm exploitability:

```
Using the findings from THREAT_ANALYSIS.md, confirm
exploitability of the top-severity attack chains.

For each chain:
1. Start the application in the dev environment
2. Construct the attack sequence (HTTP requests, payloads)
3. Execute the attack chain step by step
4. Document the result (did the exploit succeed?)
5. Capture evidence (response bodies, status codes, data accessed)

Produce EXPLOITATION_EVIDENCE.md with:
- The exact HTTP requests used
- Response evidence showing the exploit worked
- The data or access gained through the chain

This is for defensive purposes — confirming the vulnerability
exists so we can fix it with confidence.
```

Observe Devin:

- Boots the application using Docker Compose or native build
- Seeds test data (users, resources) to create realistic conditions
- Constructs HTTP requests following the attack chain
- Executes each step, capturing responses
- Documents whether the exploit succeeded or was blocked

The evidence is concrete: "This request sequence, against the running
application, produced this unauthorized data access." Not theoretical — proven.

---

<a id="act-4-remediate-and-verify"></a>
## Act 4: Remediate and Verify

Now prompt Devin to fix the confirmed vulnerabilities and prove the fix:

```
Remediate the confirmed attack chains from
EXPLOITATION_EVIDENCE.md.

For each confirmed vulnerability:
1. Implement the fix (authorization check, input validation,
   rate limiting, etc.)
2. Re-run the exact same attack chain from the evidence
3. Confirm the attack is now blocked (expected: 401/403 or
   validation error)
4. Run the full test suite to confirm no regressions
5. Document the fix and re-test evidence

Open a PR with:
- The security fixes
- REMEDIATION_REPORT.md showing before/after evidence
- All existing tests still passing
```

Observe the full loop:

- Devin implements targeted fixes at the correct authorization boundary
- Re-runs the same exploit sequence from Act 3
- Attack chain now fails at the remediation point (401, 403, validation error)
- Full test suite passes — no regressions introduced
- PR includes both the fix and the proof that it works

The PR tells the complete story: "Here's the attack chain. Here's proof it
worked. Here's the fix. Here's proof it's now blocked. Here are the passing
tests."

---

<a id="key-takeaways"></a>
## Key Takeaways

- **Proactive vs. reactive** — this pattern finds vulnerabilities before
  scanners flag them. Multi-step attack chains are invisible to pattern-matching
  tools.

- **Runtime confirmation changes the conversation** — "we think this might be
  exploitable" becomes "we confirmed this is exploitable, here's the evidence."
  This eliminates false-positive triage overhead.

- **The intelligence property in action** — reasoning across code paths,
  understanding authorization models, constructing multi-step exploits. This is
  not pattern matching; it is security engineering.

- **Complete evidence chain** — the PR contains: threat analysis → exploitation
  evidence → remediation → re-test proof. Audit-ready from the moment it's
  opened.

- **Remediation is verified, not assumed** — the same exploit that succeeded
  before the fix is re-run after the fix to prove it's blocked. Confidence is
  programmatic.

- **Dev environment isolation** — all exploitation happens in Devin's isolated
  runtime. No production risk. No external exposure. The attack chain runs
  against the dev application only.
