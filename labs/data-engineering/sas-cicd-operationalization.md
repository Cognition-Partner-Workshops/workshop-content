# SAS CI/CD & Operationalization — From Control-M to Databricks Workflows

## Table of Contents

- [Quick Start — Jump Straight In](#quick-start)
- [Challenge](#challenge)
- [Repositories](#repositories)
- [What Participants Will Learn](#what-participants-will-learn)
- [Devin Features Exercised](#devin-features-exercised)
- [Difficulty & Estimated Time](#difficulty--estimated-time)
- [Hands-On Labs](#hands-on-labs)
  - [Lab 1: CI/CD Comparison Analysis (both repos)](#lab-1-cicd-comparison-analysis)
  - [Lab 2: Extend CI/CD with Deployment Stage (uc-data-migration-sas-to-databricks)](#lab-2-extend-cicd-with-deployment-stage)
  - [Lab 3: Add Monitoring and Alerting (uc-data-migration-sas-to-databricks)](#lab-3-add-monitoring-and-alerting)
- [Key Takeaways](#key-takeaways)
- [Going Further — Automation & Scale](#going-further)
- [Notes](#notes)

---

<a id="quick-start"></a>
## Quick Start — Jump Straight In

Already familiar with Devin? Copy the prompt below into a Devin session and go.

```
Analyze the batch orchestration in ts-sas-legacy-analytics/BatchJobs/ and the
CI/CD artifacts in uc-data-migration-sas-to-databricks/ (specifically
.github/workflows/, workflows/, .pre-commit-config.yaml, and Makefile). Produce
a comparison document showing SAS DevOps vs dbt/Databricks DevOps across these
5 dimensions: (1) Version Control — how code is stored and promoted,
(2) Testing — how quality is validated before deployment, (3) Deployment — how
code moves from development to production, (4) Scheduling — how jobs are
orchestrated and triggered, (5) Monitoring — how failures are detected and
handled. For each dimension, document the legacy SAS approach, the modern
dbt/Databricks approach, and the specific artifacts in each repo that
demonstrate the pattern.
```

Then continue to [Lab 2](#lab-2-extend-cicd-with-deployment-stage) and [Lab 3](#lab-3-add-monitoring-and-alerting) when ready.

---

<a id="challenge"></a>
## Challenge

Migrate the CI/CD and operationalization layer from a legacy SAS environment — Control-M batch scheduling, manual .spk deployment, no version control, no automated testing — to a modern dbt/Databricks pipeline with GitHub Actions CI, Databricks Workflows, pre-commit hooks, dbt tests, and Git-native version control.

Legacy SAS estates typically have no CI/CD: code is deployed by copying files, tested manually by running programs and inspecting logs, and scheduled via external orchestrators (Control-M, LSF, Platform Suite for SAS) with no integration to source control. Devin analyzes both the legacy operational model and the modern target CI/CD artifacts to produce a comprehensive comparison and extend the pipeline with production deployment capabilities.

<a id="repositories"></a>
## Repositories

- [ts-sas-legacy-analytics](https://github.com/Cognition-Partner-Workshops/ts-sas-legacy-analytics) — Legacy SAS analytics environment with batch orchestrators in `BatchJobs/` that demonstrate Control-M integration patterns: sequential job chaining, error handling with conditional restart, dependency-based execution order, and manual deployment via .spk packages
- [uc-data-migration-sas-to-databricks](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-databricks) — dbt project with CI/CD artifacts: GitHub Actions workflows (`.github/workflows/`), Databricks Workflow definitions (`workflows/`), pre-commit configuration (`.pre-commit-config.yaml`), SQL linting (sqlfluff), dbt tests, and a Makefile for local development tasks

<a id="what-participants-will-learn"></a>
## What Participants Will Learn

- How Devin analyzes batch orchestration scripts (Control-M job definitions, SAS batch runners) to understand execution dependencies
- How Devin maps legacy operational patterns to cloud-native equivalents (Control-M → Databricks Workflows, manual deploy → GitHub Actions, no tests → dbt test + sqlfluff)
- How pre-commit hooks, linting (sqlfluff), and dbt tests create automated quality gates that replace manual SAS log inspection
- How environment-specific deployment (dev/staging/prod) replaces the single-environment .spk promotion model
- The fundamental operational improvement: SAS was generally NOT version controlled — Git-native CI/CD is a governance upgrade, not just a technology swap
- How to use Devin as a team resource for CI/CD modernization — multiple engineers reviewing the same pipeline PR, shared Knowledge notes encoding deployment conventions

<a id="devin-features-exercised"></a>
## Devin Features Exercised

- Cross-repo analysis: comparing legacy orchestration artifacts with modern CI/CD configurations
- CI/CD pipeline authoring: extending GitHub Actions workflows with deployment stages
- Configuration management: environment-specific variable handling across deployment targets
- Documentation generation: structured comparison documents with actionable migration guidance
- PR creation with CI/CD infrastructure changes
- DeepWiki for rapid orientation on both repos before analyzing artifacts
- Knowledge notes for encoding CI/CD conventions across sessions

<a id="difficulty--estimated-time"></a>
## Difficulty & Estimated Time

**Difficulty:** Intermediate to Advanced
**Estimated Time:** 60 minutes

---

<a id="hands-on-labs"></a>
## Hands-On Labs

<a id="lab-1-cicd-comparison-analysis"></a>
### Lab 1: CI/CD Comparison Analysis

**Repositories:** Both [ts-sas-legacy-analytics](https://github.com/Cognition-Partner-Workshops/ts-sas-legacy-analytics) and [uc-data-migration-sas-to-databricks](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-databricks)

Compare the legacy SAS operational model (batch scheduling, manual deployment, log-based validation) with the modern dbt/Databricks CI/CD pipeline across five dimensions.

#### Step 1: Paste into Devin

```
Analyze the batch orchestration in ts-sas-legacy-analytics/BatchJobs/ and the
CI/CD artifacts in uc-data-migration-sas-to-databricks/ (specifically
.github/workflows/, workflows/, .pre-commit-config.yaml, and Makefile). Produce
a comparison document showing SAS DevOps vs dbt/Databricks DevOps across these
5 dimensions: (1) Version Control — how code is stored and promoted,
(2) Testing — how quality is validated before deployment, (3) Deployment — how
code moves from development to production, (4) Scheduling — how jobs are
orchestrated and triggered, (5) Monitoring — how failures are detected and
handled. For each dimension, document the legacy SAS approach, the modern
dbt/Databricks approach, and the specific artifacts in each repo that
demonstrate the pattern.
```

#### Step 2: Research with Ask Devin

- *"How does the Control-M job chain in run_daily_banking.sas translate to the Databricks Workflow definition in daily_banking_pipeline.json? What about error handling and retry logic?"*
- *"What dbt test strategies should we use to replace the manual log inspection that SAS operators did after each batch run?"*
- *"How should we customize sqlfluff rules for our SAS-migrated SQL — are there patterns from SAS that produce valid but non-idiomatic SQL?"*

#### Key Takeaways

- **Five-dimension comparison**: Version control, testing, deployment, scheduling, and monitoring each have a legacy SAS pattern and a modern dbt/Databricks equivalent
- **Artifact-grounded analysis**: Each comparison point references specific files in both repos, not abstract descriptions
- **Governance gap**: The most significant finding is typically that SAS code was not version controlled at all — the comparison makes this gap concrete

---

<a id="lab-2-extend-cicd-with-deployment-stage"></a>
### Lab 2: Extend CI/CD with Deployment Stage

**Repository:** [uc-data-migration-sas-to-databricks](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-databricks)

The existing GitHub Actions workflow enforces quality gates (lint, test, build) but does not deploy. Extend it with a deployment stage that uses the Databricks CLI to deploy workflow definitions with environment-specific variable handling.

#### Step 1: Paste into Devin

```
Review the GitHub Actions workflow in
uc-data-migration-sas-to-databricks/.github/workflows/dbt_ci.yml and extend it
with a deployment stage that uses the Databricks CLI to deploy the workflow
definition from workflows/daily_banking_pipeline.json. Add environment-specific
variable handling so the pipeline can target dev, staging, or prod Databricks
workspaces based on the branch or trigger event. The deployment stage should:
(1) only run after all quality gates pass (lint, test, build),
(2) use GitHub environment secrets for workspace URLs and tokens,
(3) validate the workflow JSON before deploying,
(4) include a dry-run option for staging deployments.
```

#### Step 2 (Optional): Read the DeepWiki

Open the DeepWiki page for uc-data-migration-sas-to-databricks to understand how the CI/CD pipeline, workflow definitions, and quality gates work together. DeepWiki coverage depends on repo structure — complex CI/CD configurations may require manual review of the workflow YAML.

#### Key Takeaways

- **Quality gates before deployment**: The deployment stage only runs after lint, test, and build pass — a governance upgrade over manual .spk promotion
- **Environment separation**: dev/staging/prod targeting replaces the single-environment deployment model typical of SAS shops
- **Dry-run capability**: Staging deployments can be validated without side effects, reducing production risk

---

<a id="lab-3-add-monitoring-and-alerting"></a>
### Lab 3: Add Monitoring and Alerting

**Repository:** [uc-data-migration-sas-to-databricks](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-databricks)

The CI/CD pipeline enforces quality before merge, but production monitoring is not yet configured. Add monitoring that maps from the legacy Control-M operational model to Databricks-native capabilities.

#### Step 1: Paste into Devin

```
The CI/CD pipeline in uc-data-migration-sas-to-databricks enforces quality
before merge, but production monitoring is not yet configured. Add a monitoring
section to the Databricks Workflow definition in
workflows/daily_banking_pipeline.json that includes:
(1) failure notifications (email or Slack webhook),
(2) SLA-based alerting if a job exceeds its expected duration,
(3) retry policies that mirror the Control-M restart logic from the legacy
    environment in ts-sas-legacy-analytics/BatchJobs/.
Document the mapping between legacy Control-M monitoring (log inspection,
manual alerts) and the new Databricks-native monitoring in a
MONITORING_COMPARISON.md.
```

#### Step 2: Research with Ask Devin

- *"What monitoring capabilities does the Control-M batch chain in ts-sas-legacy-analytics have, and what's the closest Databricks Workflows equivalent for each?"*
- *"How do we set up SLA-based alerting in Databricks Workflows for a job that historically ran in a 2-hour batch window under Control-M?"*

#### Key Takeaways

- **From manual to automated monitoring**: SAS operators manually inspected logs after batch runs; Databricks Workflows provide built-in failure notifications and SLA alerts
- **Retry logic translation**: Control-M restart/recovery patterns map to Databricks task-level retry policies
- **End-to-end operational parity**: With CI/CD, deployment, scheduling, and monitoring in place, the modern pipeline covers the full operational surface that Control-M + manual processes covered in the legacy environment

---

<a id="key-takeaways"></a>
## Key Takeaways

- **No CI/CD in SAS** — Legacy SAS environments had no CI/CD pipeline. Code was deployed manually via .spk packages or file copies, tested by manually inspecting logs, and scheduled through external orchestrators with no integration to source control.
- **Git-native version control is the foundational upgrade** — SAS code was generally not version controlled. Moving to Git is not just a tooling change — it enables audit trails, change review, rollback, and collaboration patterns that did not exist in the legacy environment.
- **Automated quality gates replace manual inspection** — Pre-commit hooks catch issues at development time, sqlfluff enforces SQL style, and dbt tests validate business logic — all before code reaches the main branch. This replaces the post-execution log review that SAS operators performed after batch runs.
- **Cloud-native orchestration replaces external schedulers** — Databricks Workflows provide scheduling, dependency management, retry policies, and SLA monitoring as built-in capabilities. This replaces the need for external orchestrators like Control-M.
- **Team resource, not individual tool** — Multiple engineers can review the same CI/CD pipeline PR. Knowledge notes encode deployment conventions so future sessions typically apply the same standards. Scheduled sessions can monitor pipeline health on a recurring basis.

---

<a id="going-further"></a>
## Going Further — Automation & Scale

### Webhook-Driven Pipeline Updates

Connect Devin to your CI pipeline so changes to the dbt project automatically trigger a review of the CI/CD configuration:

```
dbt model added → webhook → Devin session → Validates workflow JSON covers new model → PR with updates
```

### Scheduled CI/CD Health Checks

Schedule a recurring Devin session that reviews the CI/CD pipeline configuration against the current dbt project state — checking for models not covered by the workflow definition, tests that have been added but not wired into CI, or sqlfluff rules that need updating as the SQL codebase grows.

### Divide and Conquer

For large migrations with multiple batch schedules, use child sessions — one per Control-M job chain — each analyzing the legacy scheduling and producing the equivalent Databricks Workflow definition. The parent session consolidates the individual workflow definitions into a coordinated production schedule.

---

<a id="notes"></a>
## Notes

- The `ts-sas-legacy-analytics` repo has no SAS runtime — analysis is entirely based on static inspection of the batch orchestration scripts in `BatchJobs/`
- The CI/CD artifacts in `uc-data-migration-sas-to-databricks` (`.github/workflows/dbt_ci.yml`, `workflows/daily_banking_pipeline.json`, `.pre-commit-config.yaml`, `Makefile`) are the target-state artifacts that participants extend during the labs
- This module complements the [SAS Migration Analysis](sas-migration-analysis.md) module, which covers estate discovery and dbt target mapping. This module focuses on the CI/CD and operationalization layer
- Different participants may produce different CI/CD configurations — there is no single "right answer" for how to structure the deployment pipeline
