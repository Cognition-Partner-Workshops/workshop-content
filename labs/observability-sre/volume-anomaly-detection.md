# Volume Anomaly Detection

## Table of Contents

- [Quick Start](#quick-start)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Repositories](#repositories)
  - [uc-volume-anomaly-detection](#uc-volume-anomaly-detection)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

Copy the **Step 1** prompt below into a new Devin session targeting the uc-volume-anomaly-detection repo. No prerequisites beyond repo access.

---

## Challenge

Implement volume-based anomaly detection for early identification of production issues after releases. This exercises Devin's ability to work with time-series analysis, statistical detection algorithms, and multi-agent coordination for observability.

## Target Outcomes

- Z-score and seasonal decomposition detectors that identify abnormal transaction volume patterns
- Service health correlation agent that maps anomalies to upstream/downstream dependencies
- Recommendation engine that suggests corrective actions based on runbook knowledge
- Incident insight agent that consolidates findings into actionable reports
- PR with detection algorithms, configuration, and unit tests

## What Participants Will Learn

- How Devin implements statistical anomaly detection (z-score, seasonal baselines)
- How Devin builds service dependency correlation logic
- Devin's ability to create knowledge-based recommendation systems
- How to evaluate AI-generated observability and alerting code

## Devin Features Exercised

- Statistical algorithm implementation (z-score, seasonal decomposition)
- Multi-agent Python development with shared data models
- Configuration-driven architecture (YAML rule definitions)
- Unit test generation for detection algorithms
- PR creation with test evidence

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

---

## Repositories

### <a id="uc-volume-anomaly-detection"></a>uc-volume-anomaly-detection

**Repository:** [uc-volume-anomaly-detection](https://github.com/Cognition-Partner-Workshops/uc-volume-anomaly-detection)

Python anomaly detection framework with z-score and seasonal detection, service health correlation, and incident reporting.

#### Step 1: Paste into Devin

```
Review the uc-volume-anomaly-detection codebase. The seasonal detector currently builds baselines from hourly data bucketed by day-of-week. Enhance it to also support a "time-of-day only" mode that ignores day-of-week (useful for services with consistent daily patterns). Add a configuration option in detection_rules.yaml to toggle between modes. Add unit tests for both modes.
```

#### Step 2: Research with Ask Devin

- *"What additional detection strategies beyond z-score and seasonal would improve anomaly detection accuracy? Should we add EWMA or CUSUM?"*
- *"How should the recommendation engine prioritize actions when multiple anomalies are detected simultaneously?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the detection pipeline flow from raw transaction data through baseline building, anomaly detection, health correlation, and incident reporting. Identify gaps in the current detection coverage.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the time-of-day mode correctly aggregate across all days of the week?
- **Leave a comment** asking Devin to add a CLI entrypoint that processes the sample CSV data and prints detected anomalies

---

## Key Takeaways

- Statistical anomaly detection (z-score, seasonal decomposition) is algorithmic work that Devin implements accurately when the requirements are specific — specifying the bucketing strategy and configuration format in the prompt leads to better results
- Multi-agent architectures (detector → correlator → recommender → reporter) map well to Devin's ability to work across multiple files with clear interfaces
- Configuration-driven detection rules (YAML) make the system extensible without code changes — a pattern Devin generates naturally when prompted

## Going Further

Volume anomaly detection connects directly to **incident-response automation** (see [When to Use Devin → Event-Driven](../../reference/general-themes/when-to-use-devin.md)):

- **Alert-triggered anomaly investigation** — When the anomaly detector fires an alert (via Prometheus AlertManager or a custom webhook), trigger a Devin session that correlates the anomaly with recent deployments, configuration changes, and upstream service health — producing a triage report before the on-call engineer is paged
- **Scheduled baseline recalibration** — Run a recurring Devin session that recomputes seasonal baselines from the latest production data, adjusts detection thresholds, and opens a PR with updated `detection_rules.yaml` — ensuring the detector adapts to changing traffic patterns
- **Detection strategy expansion** — Use a Devin session to implement additional detection algorithms (EWMA, CUSUM, isolation forest) as pluggable strategies, following the same configuration-driven pattern as the existing z-score and seasonal detectors
