# ML Pipeline Setup

## Table of Contents

- [Quick Start](#quick-start)
- [Challenge](#challenge)
- [Target Outcomes](#target-outcomes)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty](#difficulty)
- [Estimated Time](#estimated-time)
- [Notes](#notes)
- [Repositories](#repositories)
  - [timesheet-app](#timesheet-app)
  - [uc-data-source-migration-jdbc-normalization](#uc-data-source-migration-jdbc-normalization)
- [Key Takeaways](#key-takeaways)
- [Going Further](#going-further)

---

## Quick Start

1. Pick a repository below
2. Paste the sample prompt into Devin
3. Observe how Devin scaffolds a training pipeline, integrates it with the existing application, and documents the setup
4. Review the PR and leave feedback — Devin will iterate on your comments

---

## Challenge

Add a machine learning component to an existing application — build training and inference pipelines with proper versioning and reproducibility, such as time prediction in the timesheet app or data quality scoring in the data migration pipeline.

## Target Outcomes

- ML pipeline with training, evaluation, and inference stages
- Model versioning and experiment tracking setup
- Reproducible training configuration (requirements, data splits, hyperparameters)
- Integration with the existing application (REST endpoint or CLI entry point)
- PR with ML pipeline code and documentation

## What Participants Will Learn

- How Devin scaffolds ML pipelines that integrate with existing application architectures
- How Devin handles ML-specific dependency management and environment setup
- How Devin structures training, evaluation, and inference as separable pipeline stages
- The importance of specifying data sources and model requirements in prompts
- How the **PR feedback loop** works — reviewers comment, Devin iterates, CI re-runs (see [Collaboration Model](../../reference/general-themes/collaboration-model.md))

## Devin Features Exercised

- ML framework knowledge and pipeline architecture
- Dependency management and environment configuration
- Codebase analysis for integration points
- PR creation with technical documentation
- **Devin Review** — can catch data leakage, missing validation, or reproducibility issues in ML code (see [Platform Capabilities → Devin Review](../../reference/general-themes/platform-capabilities.md#devin-review))

## Difficulty

Advanced

## Estimated Time

75 minutes

## Notes

- Participants should review the existing codebase first using Ask Devin to understand data schemas and integration points
- Good follow-up: pair with the Model Evaluation & Testing module for a complete ML workflow
- Encourage participants to iterate on the prompt — specifying model type, input features, and target variable produces better results
- Environment dependencies (Python packages, ML frameworks) can be configured in Devin's VM blueprint for faster startup in future sessions (see [Architecture Strengths → Shared Context Layer](../../reference/general-themes/architecture-strengths.md#shared-context-layer))

---

## Repositories

### <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

React + Node.js/Express timesheet application with historical work entry data suitable for building a task duration prediction model.

#### Step 1: Paste into Devin

```text
Add a machine learning pipeline to timesheet-app that predicts task duration based on historical timesheet data. Create a Python-based training pipeline under `ml/` that: (1) extracts features from the existing work entries (client, project type, description keywords, day of week), (2) trains a regression model to predict hours spent, (3) serializes the trained model, and (4) exposes a REST endpoint at GET /api/predict-duration that accepts task metadata and returns an estimated duration. Include a requirements.txt, a training script with configurable hyperparameters, and a README documenting the pipeline.
```

#### Step 2: Research with Ask Devin

- *"What data fields are available in the timesheet-app work entries table? Which fields would be useful features for predicting task duration?"*
- *"What is the backend architecture of timesheet-app? How should a Python ML service integrate with the existing Node.js/Express server?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the data model and API structure. Identify which historical data fields are most relevant for training a duration prediction model.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the pipeline include proper train/test splitting and feature engineering?
- **Leave a comment** asking Devin to add input validation on the prediction endpoint and handle edge cases (e.g., missing features)

---

### <a id="uc-data-source-migration-jdbc-normalization"></a>uc-data-source-migration-jdbc-normalization

**Repository:** [uc-data-source-migration-jdbc-normalization](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-jdbc-normalization)

Spring Boot loan service with legacy and modern schemas — ideal for building a data quality scoring model that validates migrated records.

#### Step 1: Paste into Devin

```text
Add a data quality scoring pipeline to uc-data-source-migration-jdbc-normalization. Create a Python-based ML pipeline under `ml/` that: (1) reads loan and borrower records from the H2 database or exported CSV, (2) engineers features for data quality (completeness, format consistency, value range checks, cross-field validation), (3) trains an anomaly detection model to flag potentially corrupted or incorrectly migrated records, and (4) outputs a scored report of records ranked by quality risk. Include a training script, requirements.txt, sample output report, and a README explaining the scoring methodology.
```

#### Step 2: Research with Ask Devin

- *"What are the key differences between the legacy CDW schema and the modern schema in uc-data-source-migration-jdbc-normalization? What data quality issues are most common during migration?"*
- *"What transformations happen in the LoanService translation layer? Which transformations are most likely to introduce data quality issues?"*

#### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the legacy-to-modern field mappings in `data/mappings/column_mappings.md`. These transformations define where data quality issues are most likely to occur.

#### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the quality scoring model account for the specific transformation rules (date parsing, amount parsing, status code expansion)?
- **Leave a comment** asking Devin to add a threshold configuration so users can tune sensitivity of the anomaly detection

---

## Key Takeaways

- Devin can scaffold end-to-end ML pipelines (training, evaluation, inference) that integrate with existing application architectures
- Reproducible configuration (requirements.txt, configurable hyperparameters, documented data sources) is critical for production ML
- The PR feedback loop lets reviewers evaluate ML-specific concerns (data leakage, feature engineering quality, model selection)
- Pairing with the Model Evaluation & Testing module creates a complete ML workflow
- Devin Review can catch ML-specific issues like data leakage or missing validation in pipeline code

---

## Going Further

- **Scheduled model retraining** — run Devin on a weekly schedule to retrain models on fresh data, compare performance against the previous baseline, and open a PR if the new model improves metrics (see [Platform Capabilities → Scheduled Sessions](../../reference/general-themes/platform-capabilities.md#scheduled-sessions))
- **Playbook-driven ML development** — create a playbook encoding your team's ML pipeline standards (data validation → feature engineering → training → evaluation → deployment) so every ML feature follows consistent practices (see [Platform Capabilities → Playbooks](../../reference/general-themes/platform-capabilities.md#playbooks))
- **Parallel pipeline development** — use child agents to build ML pipelines for multiple prediction targets simultaneously, each following the same pipeline playbook (see [Platform Capabilities → Child Agents](../../reference/general-themes/platform-capabilities.md#child-agents-divide-and-conquer))
