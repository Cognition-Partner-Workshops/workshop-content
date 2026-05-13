# Workshop: Agentic AI Hands-on Workshop — Washington, DC (Session 2)

## Event Details

| | |
|---|---|
| **Date** | 2026-03-13 |
| **Location** | Washington, DC |
| **Event Site** | https://partner-workshops.devinenterprise.com |

## Featured Labs

This event features 4 structured labs using purpose-built repositories, focused on agentic AI patterns — multi-agent coordination, document processing, test automation, and anomaly detection:

### Lab 1 — Automated Pod Remediation After Credential Rotations (60 min)
- **Module:** [Pod Remediation After Credential Rotation After Credential Rotation](../../../modules/observability-sre/pod-remediation-credential-rotation.md)
- **Repository:** [uc-pod-remediation-credential-rotation](https://github.com/Cognition-Partner-Workshops/uc-pod-remediation-credential-rotation)
- **Objective:** Explore a multi-agent Python system that automates detection, approval, and remediation of pod failures caused by credential rotations — enhance it with emergency rotation detection

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

> Review the uc-pod-remediation-credential-rotation codebase. The rotation_monitor agent needs to be enhanced to support detecting rotations that happen outside the scheduled cron window (emergency rotations). Add a method `detect_emergency_rotations` that compares the last_rotated_at timestamp against the cron schedule and flags any rotation that occurred more than 24 hours before the next scheduled window. Add unit tests for the new method. Open a PR.

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What failure patterns beyond CrashLoopBackOff should the failure detector watch for after a credential rotation?"*
- *"How should the approval workflow handle timeouts — should it auto-escalate or auto-reject?"*
- Use the analysis to start a **second session** — try adding a new failure detection pattern or improving the ServiceNow integration

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the agent architecture and data flow between rotation monitoring, failure detection, approval, and remediation. Use what you learn to try different approaches:
- Have Devin add **retry logic with exponential backoff** to the remediation orchestrator
- Ask Devin to add **Prometheus metrics** for rotation detection latency and remediation success rate
- Try having Devin implement a **dry-run mode** that logs what would be remediated without taking action
- Ask Devin to add **integration test scaffolding** that mocks the Kubernetes API client

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR from step 1, practice the feedback loop:
- **Review the diff** — does the emergency rotation detection correctly parse cron schedules?
- **Leave a comment on the PR** asking Devin to fix something (e.g., *"Add integration test scaffolding that mocks the Kubernetes API client"* or *"The cron parsing doesn't handle edge cases like leap seconds"*)
- **Watch Devin respond** to your PR comment and push a fix — this is how real teams work with Devin
- Try leaving both general comments and inline code comments to see how Devin handles each

See the full challenge details for [Pod Remediation After Credential Rotation](../../../modules/observability-sre/pod-remediation-credential-rotation.md) for more ideas.

- **Target Outcomes (any of these count):**
  - Emergency rotation detection method with unit tests
  - Improved failure detection patterns beyond CrashLoopBackOff
  - Retry logic or dry-run mode for the remediation orchestrator
  - Integration test scaffolding with mocked Kubernetes API
  - PR with review comments and Devin's responses

### Lab 2 — Document Review Automation for Loan Processing (45 min)
- **Module:** [Document Review Automation](../../../modules/technical-documentation/document-review-automation.md)
- **Repository:** [uc-document-review-automation](https://github.com/Cognition-Partner-Workshops/uc-document-review-automation)
- **Objective:** Work with a multi-agent document review system — enhance the fuzzy matching comparator with an alternative string similarity strategy and evaluate accuracy

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

> Review the uc-document-review-automation codebase. The fuzzy_comparator currently uses Jaccard similarity on bigrams for name matching. Enhance it to also support Levenshtein distance as an alternative strategy, configurable via comparison_rules.yaml. Add unit tests comparing accuracy of both strategies on sample name pairs (with typos, abbreviations, and reordering). Open a PR.

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What document types are most likely to have extraction errors? How should the decision agent weight confidence differently by document type?"*
- *"Should the audit agent support structured query capabilities (e.g., find all reviews with confidence below 0.8)?"*
- Use the analysis to start a **second session** — try adding a new document type extractor or improving the decision engine thresholds

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the extraction-comparison-decision pipeline and the field routing logic. Use what you learn to try different approaches:
- Have Devin add a **batch processing CLI** that reads a directory of documents and outputs a comparison report
- Ask Devin to implement **configurable confidence thresholds** per document type
- Try having Devin add **phonetic matching** (Soundex or Metaphone) as a third comparison strategy
- Ask Devin to generate a **compliance audit report** summarizing all decisions with confidence scores

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR from step 1, practice the feedback loop:
- **Review the diff** — does the Levenshtein implementation handle Unicode characters correctly?
- **Leave a comment on the PR** asking Devin to fix something (e.g., *"Add a batch processing CLI that reads a directory of documents and outputs a comparison report"* or *"The Levenshtein distance should be normalized by string length"*)
- **Watch Devin respond** to your PR comment and push a fix — this is how real teams work with Devin

See the full challenge details for [Document Review Automation](../../../modules/technical-documentation/document-review-automation.md) for more ideas.

- **Target Outcomes (any of these count):**
  - Levenshtein distance comparator with YAML configuration
  - Unit tests comparing accuracy of Jaccard vs. Levenshtein on sample data
  - Batch processing CLI or additional comparison strategy
  - Configurable confidence thresholds per document type
  - PR with review comments and Devin's responses

### Lab 3 — BDD Test Case Generation for REST APIs (60 min)
- **Module:** [BDD Test Generation](../../../modules/testing-qa/bdd-test-generation.md)
- **Repositories:** [uc-bdd-test-generation-rest-api](https://github.com/Cognition-Partner-Workshops/uc-bdd-test-generation-rest-api), [ts-swagger-petstore](https://github.com/Cognition-Partner-Workshops/ts-swagger-petstore)
- **Objective:** Generate BDD test cases from a Swagger/OpenAPI specification and produce executable Cucumber tests covering happy paths, error cases, and edge cases

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

> Review the uc-bdd-test-generation-rest-api codebase. This is a Cucumber BDD framework for testing REST APIs. Add new Gherkin feature files that test a Petstore-style API (pets CRUD: create, read, update, delete, list). Include scenarios for: successful CRUD operations, validation errors (missing required fields), not-found cases, and pagination. Implement the corresponding step definitions. Open a PR.

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What Cucumber best practices should be followed for REST API testing — should scenarios be independent or can they share state?"*
- *"How should authentication be handled in the BDD scenarios — per-scenario setup or shared background?"*
- Use the analysis to start a **second session** — try generating tests from the `ts-swagger-petstore` OpenAPI spec directly

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the existing Cucumber configuration, step definition patterns, and how the framework maps Gherkin steps to REST API calls. Use what you learn to try different approaches:
- Have Devin generate **data-driven scenarios** using Cucumber Scenario Outlines with Examples tables
- Ask Devin to add **negative test scenarios** for authentication failures and rate limiting
- Try having Devin read the `ts-swagger-petstore` spec and **auto-generate feature files** for all endpoints
- Ask Devin to add a **test report generator** that produces HTML results

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR from step 1, practice the feedback loop:
- **Review the diff** — are the Gherkin scenarios readable by non-developers? Do they describe business behavior rather than implementation details?
- **Leave a comment on the PR** asking Devin to fix something (e.g., *"Add data-driven scenarios using Cucumber Scenario Outlines with Examples tables"* or *"The step definitions should use more descriptive method names"*)
- **Watch Devin respond** to your PR comment and push a fix — this is how real teams work with Devin

See the full challenge details for [BDD Test Generation](../../../modules/testing-qa/bdd-test-generation.md) for more ideas.

- **Target Outcomes (any of these count):**
  - Gherkin feature files covering CRUD operations, validation errors, and edge cases
  - Executable Cucumber step definitions integrated with Maven build
  - Data-driven scenarios using Scenario Outlines with Examples tables
  - Test coverage of the Petstore API specification
  - PR with review comments and Devin's responses

### Lab 4 — Volume-Based Anomaly Detection (60 min)
- **Module:** [Volume Anomaly Detection](../../../modules/observability-sre/volume-anomaly-detection.md)
- **Repository:** [uc-volume-anomaly-detection](https://github.com/Cognition-Partner-Workshops/uc-volume-anomaly-detection)
- **Objective:** Work with a multi-agent anomaly detection framework — enhance the seasonal detector to support a time-of-day-only mode and evaluate detection accuracy

#### Step 1: Paste into Devin (copy-paste this prompt into Devin)

> Review the uc-volume-anomaly-detection codebase. The seasonal detector currently builds baselines from hourly data bucketed by day-of-week. Enhance it to also support a "time-of-day only" mode that ignores day-of-week (useful for services with consistent daily patterns). Add a configuration option in detection_rules.yaml to toggle between modes. Add unit tests for both modes. Open a PR.

#### Step 2: Research with Ask Devin

While Devin works on step 1, open **AskDevin** and explore:
- *"What additional detection strategies beyond z-score and seasonal would improve anomaly detection accuracy? Should we add EWMA or CUSUM?"*
- *"How should the recommendation engine prioritize actions when multiple anomalies are detected simultaneously?"*
- Use the analysis to start a **second session** — try adding an EWMA detector or improving the service health correlation logic

#### Step 3 (Optional): Read the DeepWiki

Open the repo's **DeepWiki** page to understand the detection pipeline flow from raw transaction data through baseline building, anomaly detection, health correlation, and incident reporting. Use what you learn to try different approaches:
- Have Devin add a **CLI entrypoint** that processes the sample CSV data and prints detected anomalies
- Ask Devin to implement an **EWMA (Exponentially Weighted Moving Average)** detector as a third strategy
- Try having Devin add **visualization output** — generate a simple chart showing baseline vs. actual volumes with anomaly markers
- Ask Devin to add **configurable alerting thresholds** per service in the YAML rules

#### Step 4 (Optional): Review & Give Feedback

Once Devin opens a PR from step 1, practice the feedback loop:
- **Review the diff** — does the time-of-day mode correctly aggregate across all days of the week?
- **Leave a comment on the PR** asking Devin to fix something (e.g., *"Add a CLI entrypoint that processes the sample CSV data and prints detected anomalies"* or *"The baseline calculation should handle missing data points gracefully"*)
- **Watch Devin respond** to your PR comment and push a fix — this is how real teams work with Devin

See the full challenge details for [Volume Anomaly Detection](../../../modules/observability-sre/volume-anomaly-detection.md) for more ideas.

- **Target Outcomes (any of these count):**
  - Time-of-day-only seasonal detection mode with YAML configuration
  - Unit tests for both day-of-week and time-of-day modes
  - CLI entrypoint or additional detection strategy (EWMA, CUSUM)
  - Configurable alerting thresholds per service
  - PR with review comments and Devin's responses

## Additional Challenges

Participants may also attempt any challenge from the full [module catalog](../../../modules/) as creative inspiration. The labs above are the primary structured activities.

## Repos Required on Devin's Machine

- [ ] uc-pod-remediation-credential-rotation
- [ ] uc-document-review-automation
- [ ] uc-bdd-test-generation-rest-api
- [ ] ts-swagger-petstore
- [ ] uc-volume-anomaly-detection

## Context

- **Audience:** Technology teams — platform engineering, QA, observability, and operations
- **Focus:** Agentic AI patterns — multi-agent coordination, document processing, BDD test automation, and statistical anomaly detection

## Devin Features Checklist

Encourage participants to track their progress on the [Devin Features Appendix](../../../modules/devin-features/README.md) throughout the session.
