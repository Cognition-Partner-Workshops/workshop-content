# Workshop: Agentic AI — Desktop + Cloud

**Other variants:** [Cloud only](README.md)

## Overview

| | |
|---|---|
| **Focus** | Multi-agent systems, document processing, BDD test automation, anomaly detection |
| **Duration** | 2-4 hours |
| **Audience** | Platform engineering, QA, observability, and operations teams |
| **Delivery** | Devin Desktop + Devin Cloud |
| **Key Modules** | [Pod Remediation After Credential Rotation](../../modules/observability-sre/pod-remediation-credential-rotation.md), [Volume Anomaly Detection](../../modules/observability-sre/volume-anomaly-detection.md), [Document Review Automation](../../modules/technical-documentation/document-review-automation.md), [BDD Test Generation](../../modules/testing-qa/bdd-test-generation.md) |

## Platform Context

This workshop uses **Devin Desktop + Devin Cloud**. You will use Desktop as your primary interface — exploring code locally, delegating tasks to Cloud, and reviewing results without leaving the editor.

> **Tip:** The same playbooks, knowledge, and MCP integrations that power Cloud sessions are available to your local agents in Desktop. Everything you configure once benefits the whole platform.

## Workshop Narrative

This workshop explores agentic AI use cases — multi-agent coordination, approval-gated workflows, and intelligent automation. Each lab works with a purpose-built Python or Java codebase that implements a multi-agent pattern, and participants enhance it with Devin's help.

Unlike the cloud-only variant, this delivery uses Devin Desktop as the primary interface. You will explore code locally with Cascade or Devin Local, delegate implementation tasks to Devin Cloud, and review the resulting PRs directly in the editor — demonstrating the full local→cloud→review loop.

## Desktop Workflow

Every lab in this workshop follows a five-step Desktop workflow:

1. **Create a Space** — Open Devin Desktop and create a Space for this workshop. Spaces group sessions, PRs, and files for a single task.
2. **Explore with Cascade or Devin Local** — Use the local agent in Desktop (or Devin CLI in your terminal) to explore the codebase. This replaces the "Ask Devin" research steps from the cloud-only variant.
3. **Delegate to Devin Cloud** — Once you've refined the requirements, delegate the implementation task to Cloud. The cloud agent works autonomously on its own VM.
4. **Monitor in the Agent Command Center** — Switch to the Agent Command Center (Kanban view) to track progress across sessions. You can run multiple labs in parallel.
5. **Review PRs with one-click checkout** — When Cloud opens a PR, review it directly in Desktop. One-click checkout loads the branch in the editor — no manual `git fetch` or browser switching required.

## Getting the Most from This Workshop

A few tips to maximize your hands-on time:

- **Create one Space per lab.** This keeps sessions, PRs, and files organized. Each Space inherits your project context automatically.
- **Use Cascade for research first.** Before delegating to Cloud, use Cascade or Devin Local to explore the codebase and refine the requirements — better requirements produce better results.
- **Delegate early, review later.** Cloud agents work autonomously while you move on. Use the Agent Command Center to see at a glance what is in flight, blocked, or ready for review.
- **Leave PR comments to steer Devin.** After Devin Cloud opens a PR, leave comments directly in Desktop. Devin wakes up and iterates on your feedback.
- **Try multiple agents.** Desktop supports multiple agents via ACP (Agent Client Protocol). You can use Devin Local, Cascade, or third-party agents (Codex CLI, Claude Agent, Gemini CLI) side by side.
- **Browse DeepWiki while Cloud works.** Open the repo's DeepWiki page for architectural context while the cloud agent runs — it complements Cascade's code-level exploration with higher-level documentation.
- **Build up Devin's knowledge as you go.** When Devin suggests a Knowledge item, accept it — this shared context layer benefits both local and cloud sessions.

## Table of Contents

- [Lab 1 — Automated Pod Remediation After Credential Rotations](#lab-1--automated-pod-remediation-after-credential-rotations)
- [Lab 2 — Document Review Automation](#lab-2--document-review-automation)
- [Lab 3 — BDD Test Case Generation](#lab-3--bdd-test-case-generation)
- [Lab 4 — Volume-Based Anomaly Detection](#lab-4--volume-based-anomaly-detection)
- [Repos Required](#repos-required)
- [Key Takeaways](#key-takeaways)

---

## Labs

### Lab 1 — Automated Pod Remediation After Credential Rotations

- **Module:** [Pod Remediation After Credential Rotation](../../modules/observability-sre/pod-remediation-credential-rotation.md)
- **Repository:** [uc-pod-remediation-credential-rotation](https://github.com/Cognition-Partner-Workshops/uc-pod-remediation-credential-rotation)
- **Objective:** Enhance a multi-agent Python system that automates detection, approval, and remediation of pod failures
- **Duration:** 60 min

#### Step 1: Explore with Cascade

Open the `uc-pod-remediation-credential-rotation` repo in your Space. Use Cascade or Devin Local to explore the codebase:

- *"Walk me through the rotation_monitor agent — what does it currently detect?"*
- *"What failure patterns beyond CrashLoopBackOff should the failure detector watch for?"*
- *"How should the approval workflow handle timeouts?"*

#### Step 2: Delegate to Devin Cloud

Once you understand the codebase, delegate the implementation to Cloud. In your Space, click **Delegate to Cloud** and provide the task:

```
Review the uc-pod-remediation-credential-rotation codebase.
The rotation_monitor agent needs to detect rotations that happen
outside the scheduled cron window (emergency rotations). Add a
method `detect_emergency_rotations` that compares last_rotated_at
against the cron schedule and flags any rotation more than 24 hours
before the next window. Add unit tests.
```

#### Step 3: Monitor in Agent Command Center

Switch to the Agent Command Center while Cloud works. You'll see your session move through statuses: Running → PR Opened → Awaiting Review.

> **While you wait:** Open the repo's DeepWiki page for architectural context. Consider adding retry logic, Prometheus metrics, dry-run mode, or integration test scaffolding as follow-up tasks.

#### Step 4: Review PR in Desktop

When Devin Cloud opens a PR, it appears in your Space. Use one-click checkout to load the branch in your editor and review the emergency rotation detection. Leave inline comments if you want Devin to iterate.

**Target Outcomes:** Emergency rotation detection, improved failure patterns, retry logic or dry-run mode

---

### Lab 2 — Document Review Automation

- **Module:** [Document Review Automation](../../modules/technical-documentation/document-review-automation.md)
- **Repository:** [uc-document-review-automation](https://github.com/Cognition-Partner-Workshops/uc-document-review-automation)
- **Objective:** Enhance a multi-agent document review system with alternative string similarity strategies
- **Duration:** 45 min

#### Step 1: Explore with Cascade

Open the `uc-document-review-automation` repo in your Space. Use Cascade to explore:

- *"How does the fuzzy_comparator work? What similarity algorithm does it use?"*
- *"What document types are most likely to have extraction errors?"*
- *"Should the audit agent support structured query capabilities?"*

#### Step 2: Delegate to Devin Cloud

Delegate the implementation to Cloud:

```
Review the uc-document-review-automation codebase. The
fuzzy_comparator currently uses Jaccard similarity on bigrams.
Enhance it to also support Levenshtein distance as an alternative
strategy, configurable via comparison_rules.yaml. Add unit tests
comparing accuracy of both strategies on sample name pairs.
```

#### Step 3: Monitor in Agent Command Center

Track this session alongside any others you have running. The Kanban board shows status at a glance.

> **While you wait:** Open the repo's DeepWiki page. Consider batch processing CLI, phonetic matching, or configurable confidence thresholds as follow-up tasks.

#### Step 4: Review PR in Desktop

Review the Levenshtein implementation with one-click checkout. Ask Devin to add batch processing or additional comparison strategies by leaving PR comments.

**Target Outcomes:** Alternative comparison strategy, accuracy comparison tests, batch processing

---

### Lab 3 — BDD Test Case Generation

- **Module:** [BDD Test Generation](../../modules/testing-qa/bdd-test-generation.md)
- **Repositories:** [uc-bdd-test-generation-cucumber](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-cucumber), [ts-java-swagger-petstore](https://github.com/Cognition-Partner-Workshops/ts-java-swagger-petstore)
- **Objective:** Generate BDD test cases from OpenAPI specs and produce executable Cucumber tests
- **Duration:** 60 min

#### Step 1: Explore with Cascade

Open the `uc-bdd-test-generation-cucumber` repo in your Space. Use Cascade to explore:

- *"What Cucumber best practices should be followed for REST API testing?"*
- *"How should authentication be handled in BDD scenarios?"*
- *"What does the existing test structure look like?"*

#### Step 2: Delegate to Devin Cloud

Delegate the implementation to Cloud:

```
Review the uc-bdd-test-generation-cucumber codebase. Add Gherkin
feature files that test a Petstore-style API (pets CRUD). Include
scenarios for successful operations, validation errors, not-found
cases, and pagination. Implement step definitions.
```

#### Step 3: Monitor in Agent Command Center

Use the Agent Command Center to monitor this session. If you have multiple labs running in parallel, the Kanban view shows each session's progress independently.

> **While you wait:** Open the repo's DeepWiki page. Consider generating data-driven scenarios, negative tests, or auto-generating feature files from the Petstore spec as follow-up tasks.

> **ACP note:** Multiple agents can work simultaneously in Desktop. You can have Cascade running locally for research while Cloud agents execute implementation tasks — each is tracked independently in the Agent Command Center.

#### Step 4: Review PR in Desktop

Review the Gherkin scenarios with one-click checkout. Check readability and ask Devin to add Scenario Outlines or test report generation via PR comments.

**Target Outcomes:** Gherkin feature files, executable step definitions, data-driven scenarios

---

### Lab 4 — Volume-Based Anomaly Detection

- **Module:** [Volume Anomaly Detection](../../modules/observability-sre/volume-anomaly-detection.md)
- **Repository:** [uc-volume-anomaly-detection](https://github.com/Cognition-Partner-Workshops/uc-volume-anomaly-detection)
- **Objective:** Enhance a multi-agent anomaly detection framework with time-of-day-only seasonal detection
- **Duration:** 60 min

#### Step 1: Explore with Cascade

Open the `uc-volume-anomaly-detection` repo in your Space. Use Cascade to explore:

- *"How does the seasonal detector build baselines? What dimensions does it use?"*
- *"What additional detection strategies beyond z-score and seasonal would improve accuracy?"*
- *"How should the recommendation engine prioritize multiple simultaneous anomalies?"*

#### Step 2: Delegate to Devin Cloud

Delegate the implementation to Cloud:

```
Review the uc-volume-anomaly-detection codebase. The seasonal
detector currently builds baselines by day-of-week. Enhance it to
support a "time-of-day only" mode that ignores day-of-week. Add a
configuration option in detection_rules.yaml. Add unit tests for
both modes.
```

#### Step 3: Monitor in Agent Command Center

Track progress in the Agent Command Center. By this point you may have multiple sessions running — the Kanban board shows the status of each.

> **While you wait:** Open the repo's DeepWiki page. Consider adding CLI entrypoints, EWMA detectors, visualization output, or configurable alerting thresholds as follow-up tasks.

#### Step 4: Review PR in Desktop

Review the time-of-day mode with one-click checkout. Ask Devin to add CLI processing or alternative detection strategies via PR comments.

**Target Outcomes:** Time-of-day detection mode, additional detection strategies, CLI entrypoint

## Repos Required

- [ ] uc-pod-remediation-credential-rotation
- [ ] uc-document-review-automation
- [ ] uc-bdd-test-generation-cucumber
- [ ] ts-java-swagger-petstore
- [ ] uc-volume-anomaly-detection

## Key Takeaways

- **"Multi-agent patterns"** — each lab demonstrates a different agentic AI coordination pattern
- **"Devin enhances existing agents"** — participants work with pre-built agent frameworks and extend them
- **"Real-world applications"** — pod remediation, document review, test generation, and anomaly detection
- **"Local→Cloud→Review loop"** — explore locally with Cascade, delegate to Cloud, review PRs in Desktop without context switching
- **"Agent Command Center"** — monitor multiple sessions on a single Kanban board, managing parallel work at scale
- **"ACP extensibility"** — multiple agents (Devin Local, Cascade, third-party) work side by side in one environment
