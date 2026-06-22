# Pod Remediation After Credential Rotation

## Table of Contents

- [Quick Start](#quick-start)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Repositories](#repositories)
  - [uc-pod-remediation-credential-rotation](#uc-pod-remediation-credential-rotation)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

Copy the **Step 1** prompt below into a new Devin session targeting the uc-pod-remediation-credential-rotation repo. No prerequisites beyond repo access.

---

## Challenge

Implement automated detection and remediation of pod failures caused by credential rotations. This exercises Devin's ability to work with Kubernetes manifests, observability patterns, and multi-agent orchestration code that coordinates detection, approval, and remediation steps.

## Target Outcomes

- Rotation monitoring agent that detects recently rotated secrets and identifies affected deployments
- Failure detection agent that scans pods for CrashLoopBackOff and credential-related error patterns
- Approval workflow integration with ServiceNow (or mock) for change request creation and polling
- Remediation orchestrator that performs rolling restarts upon approval
- PR with agent implementations, Kubernetes RBAC, and unit tests

## What Participants Will Learn

- How Devin navigates multi-agent Python codebases with Kubernetes integration
- How Devin implements approval-gated remediation workflows
- Devin's ability to work with K8s RBAC, deployment manifests, and pod lifecycle management
- How to evaluate AI-generated observability and remediation code

## Devin Features Exercised

- Multi-file Python development across agents, models, and utilities
- Kubernetes manifest authoring (RBAC, Deployments, ConfigMaps)
- Integration pattern implementation (ServiceNow API client)
- Unit test generation for domain models
- PR creation with architectural documentation

## Difficulty

Advanced

## Estimated Time

60 minutes

---

## Repositories

### <a id="uc-pod-remediation-credential-rotation"></a>uc-pod-remediation-credential-rotation

**Repository:** [uc-pod-remediation-credential-rotation](https://github.com/Cognition-Partner-Workshops/uc-pod-remediation-credential-rotation)

Python multi-agent system for Kubernetes credential rotation monitoring and automated pod remediation.

#### Step 1: Paste into Devin

```
Review the uc-pod-remediation-credential-rotation codebase. The rotation_monitor agent needs to be enhanced to support detecting rotations that happen outside the scheduled cron window (emergency rotations). Add a method `detect_emergency_rotations` that compares the last_rotated_at timestamp against the cron schedule and flags any rotation that occurred more than 24 hours before the next scheduled window. Add unit tests for the new method.
```

#### Step 2: Research with Ask Devin

- *"What failure patterns beyond CrashLoopBackOff should the failure detector watch for after a credential rotation?"*
- *"How should the approval workflow handle timeouts — should it auto-escalate or auto-reject?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the agent architecture and data flow between rotation monitoring, failure detection, approval, and remediation. Identify which agent interactions could benefit from better error handling.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the emergency rotation detection correctly parse cron schedules?
- **Leave a comment** asking Devin to add integration test scaffolding that mocks the Kubernetes API client

---

## Key Takeaways

- Multi-agent orchestration (detect → approve → remediate) is a pattern Devin handles well because each agent has a clear interface and responsibility
- Credential rotation remediation is high-urgency, low-complexity work — exactly the kind of task where automated response reduces mean time to recovery
- Approval-gated workflows show how Devin can participate in change management processes without bypassing human controls

## Going Further

Credential rotation remediation is a textbook case of **incident-response automation** (alert → Devin investigates → Devin remediates) (see [When to Use Devin → Event-Driven](../../shared/general-themes/when-to-use-devin.md)):

- **Alert-triggered pod remediation** — Connect Kubernetes alerting (Prometheus AlertManager, PagerDuty) to the Devin API. When a CrashLoopBackOff alert fires after a credential rotation, Devin investigates the pod logs, correlates with recent secret changes, and executes the remediation workflow — creating a change request, waiting for approval, and performing the rolling restart
- **Scheduled rotation health checks** — Run a recurring Devin session after each credential rotation window that verifies all affected pods restarted successfully and are passing health checks. If any pods are unhealthy, Devin opens an incident and begins remediation
- **Playbook-driven remediation** — Encode the detect-approve-remediate workflow as a Devin playbook so that every rotation event triggers the same validated process, regardless of which team member is on call
