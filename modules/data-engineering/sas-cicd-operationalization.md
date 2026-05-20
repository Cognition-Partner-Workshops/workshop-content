# SAS CI/CD & Operationalization — From Control-M to Databricks Workflows

## Repositories

- [ts-sas-legacy-analytics](#ts-sas-legacy-analytics)
- [uc-data-migration-sas-to-databricks](#uc-data-migration-sas-to-databricks)

---

## Challenge

Migrate the CI/CD and operationalization layer from a legacy SAS environment — Control-M batch scheduling, manual .spk deployment, no version control, no automated testing — to a modern dbt/Databricks pipeline with GitHub Actions CI, Databricks Workflows, pre-commit hooks, dbt tests, and Git-native version control.

Legacy SAS estates typically have no CI/CD: code is deployed by copying files, tested manually by running programs and inspecting logs, and scheduled via external orchestrators (Control-M, LSF, Platform Suite for SAS) with no integration to source control. This challenge demonstrates how Devin analyzes both the legacy operational model and the modern target CI/CD artifacts to produce a comprehensive comparison and extend the pipeline with production deployment capabilities.

## Target Outcomes

- Comparison document covering SAS DevOps vs dbt/Databricks DevOps across 5 dimensions: version control, testing, deployment, scheduling, and monitoring
- Extended GitHub Actions workflow with a deployment stage using the Databricks CLI
- Environment-specific variable handling (dev/staging/prod) in the CI/CD pipeline
- Understanding of how Control-M batch operations translate to Databricks Workflows
- Governance upgrade: automated quality gates enforced before merge

## What Participants Will Learn

- How Devin analyzes batch orchestration scripts (Control-M job definitions, SAS batch runners) to understand execution dependencies
- How Devin maps legacy operational patterns to cloud-native equivalents (Control-M → Databricks Workflows, manual deploy → GitHub Actions, no tests → dbt test + sqlfluff)
- How pre-commit hooks, linting (sqlfluff), and dbt tests create automated quality gates that replace manual SAS log inspection
- How environment-specific deployment (dev/staging/prod) replaces the single-environment .spk promotion model
- The fundamental operational improvement: SAS was generally NOT version controlled — Git-native CI/CD is a governance upgrade, not just a technology swap

## Devin Features Exercised

- Cross-repo analysis: comparing legacy orchestration artifacts with modern CI/CD configurations
- CI/CD pipeline authoring: extending GitHub Actions workflows with deployment stages
- Configuration management: environment-specific variable handling across deployment targets
- Documentation generation: structured comparison documents with actionable migration guidance
- PR creation with CI/CD infrastructure changes

## Difficulty

Intermediate to Advanced

## Estimated Time

60 minutes

---

## <a id="ts-sas-legacy-analytics"></a>ts-sas-legacy-analytics

**Repository:** [ts-sas-legacy-analytics](https://github.com/Cognition-Partner-Workshops/ts-sas-legacy-analytics)

Legacy SAS analytics environment with batch orchestrators in `BatchJobs/` that demonstrate Control-M integration patterns: sequential job chaining, error handling with conditional restart, dependency-based execution order, and manual deployment via .spk packages. The batch scripts exercise the legacy operational model — no version control, no automated testing, manual log inspection for validation, and Control-M as the sole scheduling mechanism.

### Step 1: Paste into Devin — CI/CD Comparison Analysis

> Analyze the batch orchestration in ts-sas-legacy-analytics/BatchJobs/ and the CI/CD artifacts in uc-data-migration-sas-to-databricks/ (specifically .github/workflows/, workflows/, .pre-commit-config.yaml, and Makefile). Produce a comparison document showing SAS DevOps vs dbt/Databricks DevOps across these 5 dimensions: (1) Version Control — how code is stored and promoted, (2) Testing — how quality is validated before deployment, (3) Deployment — how code moves from development to production, (4) Scheduling — how jobs are orchestrated and triggered, (5) Monitoring — how failures are detected and handled. For each dimension, document the legacy SAS approach, the modern dbt/Databricks approach, and the specific artifacts in each repo that demonstrate the pattern. Open a PR.

### Step 2: Paste into Devin — Extend CI/CD with Deployment Stage

> Review the GitHub Actions workflow in uc-data-migration-sas-to-databricks/.github/workflows/dbt_ci.yml and extend it with a deployment stage that uses the Databricks CLI to deploy the workflow definition from workflows/daily_banking_pipeline.json. Add environment-specific variable handling so the pipeline can target dev, staging, or prod Databricks workspaces based on the branch or trigger event. The deployment stage should: (1) only run after all quality gates pass (lint, test, build), (2) use GitHub environment secrets for workspace URLs and tokens, (3) validate the workflow JSON before deploying, (4) include a dry-run option for staging deployments. Open a PR.

### Step 3: Research with Ask Devin

- *"How does the Control-M job chain in run_daily_banking.sas translate to the Databricks Workflow definition in daily_banking_pipeline.json? What about error handling and retry logic?"*
- *"What dbt test strategies should we use to replace the manual log inspection that SAS operators did after each batch run?"*
- *"How should we customize sqlfluff rules for our SAS-migrated SQL — are there patterns from SAS that produce valid but non-idiomatic SQL?"*

### Step 4 (Optional): Read the DeepWiki

Open the DeepWiki pages for both ts-sas-legacy-analytics and uc-data-migration-sas-to-databricks to understand the full operational migration from Control-M batch orchestration to Databricks Workflows with CI/CD governance.

### Key Takeaways

- SAS environments had no CI/CD — code was deployed manually and tested manually
- dbt brings declarative testing, Git-native version control, and automated quality gates
- Databricks Workflows replace Control-M with cloud-native orchestration
- The GitHub Actions pipeline enforces quality before merge — a governance upgrade
- SAS was generally NOT version controlled — this is a fundamental operational improvement

---

## <a id="uc-data-migration-sas-to-databricks"></a>uc-data-migration-sas-to-databricks

**Repository:** [uc-data-migration-sas-to-databricks](https://github.com/Cognition-Partner-Workshops/uc-data-migration-sas-to-databricks)

SAS-to-Databricks migration target with CI/CD artifacts that represent the modern operational model: GitHub Actions workflows (`.github/workflows/`), Databricks Workflow definitions (`workflows/`), pre-commit configuration (`.pre-commit-config.yaml`), SQL linting (sqlfluff), dbt tests, and a Makefile for local development tasks. These artifacts collectively replace the legacy Control-M + manual deployment model.

### Step 1: Paste into Devin — Validate CI/CD Artifacts

> Review the CI/CD artifacts in uc-data-migration-sas-to-databricks: the GitHub Actions workflow in .github/workflows/dbt_ci.yml, the pre-commit configuration in .pre-commit-config.yaml, and the Databricks Workflow definition in workflows/daily_banking_pipeline.json. For each artifact, document: (1) what quality gates it enforces, (2) how it integrates with the overall pipeline, (3) what legacy SAS operational gap it fills. Verify that the workflow definition correctly represents the execution dependencies from the original Control-M batch chain. Open a PR with your analysis as a CICD_REVIEW.md document.

### Step 2: Paste into Devin — Add Monitoring and Alerting

> The CI/CD pipeline in uc-data-migration-sas-to-databricks enforces quality before merge, but production monitoring is not yet configured. Add a monitoring section to the Databricks Workflow definition that includes: (1) failure notifications (email or Slack webhook), (2) SLA-based alerting if a job exceeds its expected duration, (3) retry policies that mirror the Control-M restart logic from the legacy environment. Document the mapping between legacy Control-M monitoring (log inspection, manual alerts) and the new Databricks-native monitoring. Open a PR.

### Step 3 (Optional): Read the DeepWiki

Open the DeepWiki page for uc-data-migration-sas-to-databricks to explore how the CI/CD pipeline, workflow definitions, and quality gates work together to form the operational backbone of the migrated environment.

### Key Takeaways

- GitHub Actions CI enforces linting (sqlfluff), testing (dbt test), and validation before code reaches production — replacing the manual SAS log review process
- Pre-commit hooks catch issues at development time, before code is even pushed — a shift-left improvement over discovering problems during batch execution
- Databricks Workflows provide cloud-native scheduling with built-in retry, dependency management, and SLA monitoring — replacing Control-M's external orchestration model
- The Makefile provides a local development inner loop (lint, test, run) that mirrors the CI pipeline — developers get fast feedback without waiting for batch execution
