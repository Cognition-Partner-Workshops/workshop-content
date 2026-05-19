# Workshop: Agentic AI

## Overview

| | |
|---|---|
| **Focus** | Multi-agent systems, document processing, BDD test automation, anomaly detection |
| **Duration** | 2-4 hours |
| **Audience** | Platform engineering, QA, observability, and operations teams |
| **Key Modules** | [Pod Remediation After Credential Rotation](../../modules/observability-sre/pod-remediation-credential-rotation.md), [Volume Anomaly Detection](../../modules/observability-sre/volume-anomaly-detection.md), [Document Review Automation](../../modules/technical-documentation/document-review-automation.md), [BDD Test Generation](../../modules/testing-qa/bdd-test-generation.md) |

## Workshop Narrative

This workshop explores agentic AI use cases — multi-agent coordination, approval-gated workflows, and intelligent automation. Each lab works with a purpose-built Python or Java codebase that implements a multi-agent pattern, and participants enhance it with Devin's help.

## Labs

### Lab 1 — Automated Pod Remediation After Credential Rotations

- **Module:** [Pod Remediation After Credential Rotation](../../modules/observability-sre/pod-remediation-credential-rotation.md)
- **Repository:** [uc-pod-remediation-credential-rotation](https://github.com/Cognition-Partner-Workshops/uc-pod-remediation-credential-rotation)
- **Objective:** Enhance a multi-agent Python system that automates detection, approval, and remediation of pod failures
- **Duration:** 60 min

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

> Review the uc-pod-remediation-credential-rotation codebase. The rotation_monitor agent needs to detect rotations that happen outside the scheduled cron window (emergency rotations). Add a method `detect_emergency_rotations` that compares last_rotated_at against the cron schedule and flags any rotation more than 24 hours before the next window. Add unit tests. Open a PR.

#### Step 2: Research with Ask Devin

- *"What failure patterns beyond CrashLoopBackOff should the failure detector watch for?"*
- *"How should the approval workflow handle timeouts?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page. Try adding retry logic, Prometheus metrics, dry-run mode, or integration test scaffolding.

#### Step 4 (Optional): Review & Give Feedback

Review the emergency rotation detection. Ask Devin to add integration tests or edge case handling.

**Target Outcomes:** Emergency rotation detection, improved failure patterns, retry logic or dry-run mode

---

### Lab 2 — Document Review Automation

- **Module:** [Document Review Automation](../../modules/technical-documentation/document-review-automation.md)
- **Repository:** [uc-document-review-automation](https://github.com/Cognition-Partner-Workshops/uc-document-review-automation)
- **Objective:** Enhance a multi-agent document review system with alternative string similarity strategies
- **Duration:** 45 min

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

> Review the uc-document-review-automation codebase. The fuzzy_comparator currently uses Jaccard similarity on bigrams. Enhance it to also support Levenshtein distance as an alternative strategy, configurable via comparison_rules.yaml. Add unit tests comparing accuracy of both strategies on sample name pairs. Open a PR.

#### Step 2: Research with Ask Devin

- *"What document types are most likely to have extraction errors?"*
- *"Should the audit agent support structured query capabilities?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page. Try adding batch processing CLI, phonetic matching, or configurable confidence thresholds.

#### Step 4 (Optional): Review & Give Feedback

Review the Levenshtein implementation. Ask Devin to add batch processing or additional comparison strategies.

**Target Outcomes:** Alternative comparison strategy, accuracy comparison tests, batch processing

---

### Lab 3 — BDD Test Case Generation

- **Module:** [BDD Test Generation](../../modules/testing-qa/bdd-test-generation.md)
- **Repositories:** [uc-bdd-test-generation-cucumber](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-cucumber), [ts-java-swagger-petstore](https://github.com/Cognition-Partner-Workshops/ts-java-swagger-petstore)
- **Objective:** Generate BDD test cases from OpenAPI specs and produce executable Cucumber tests
- **Duration:** 60 min

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

> Review the uc-bdd-test-generation-cucumber codebase. Add Gherkin feature files that test a Petstore-style API (pets CRUD). Include scenarios for successful operations, validation errors, not-found cases, and pagination. Implement step definitions. Open a PR.

#### Step 2: Research with Ask Devin

- *"What Cucumber best practices should be followed for REST API testing?"*
- *"How should authentication be handled in BDD scenarios?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page. Try generating data-driven scenarios, negative tests, or auto-generating feature files from the Petstore spec.

#### Step 4 (Optional): Review & Give Feedback

Review the Gherkin scenarios for readability. Ask Devin to add Scenario Outlines or test report generation.

**Target Outcomes:** Gherkin feature files, executable step definitions, data-driven scenarios

---

### Lab 4 — Volume-Based Anomaly Detection

- **Module:** [Volume Anomaly Detection](../../modules/observability-sre/volume-anomaly-detection.md)
- **Repository:** [uc-volume-anomaly-detection](https://github.com/Cognition-Partner-Workshops/uc-volume-anomaly-detection)
- **Objective:** Enhance a multi-agent anomaly detection framework with time-of-day-only seasonal detection
- **Duration:** 60 min

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

> Review the uc-volume-anomaly-detection codebase. The seasonal detector currently builds baselines by day-of-week. Enhance it to support a "time-of-day only" mode that ignores day-of-week. Add a configuration option in detection_rules.yaml. Add unit tests for both modes. Open a PR.

#### Step 2: Research with Ask Devin

- *"What additional detection strategies beyond z-score and seasonal would improve accuracy?"*
- *"How should the recommendation engine prioritize multiple simultaneous anomalies?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page. Try adding CLI entrypoints, EWMA detectors, visualization output, or configurable alerting thresholds.

#### Step 4 (Optional): Review & Give Feedback

Review the time-of-day mode. Ask Devin to add CLI processing or alternative detection strategies.

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
