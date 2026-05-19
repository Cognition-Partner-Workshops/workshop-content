# Model Evaluation & Testing

## Repositories

- [timesheet-app](#timesheet-app)
- [uc-data-source-migration-jdbc-normalization](#uc-data-source-migration-jdbc-normalization)

---

## Challenge

Build evaluation harnesses for ML models with metrics, validation splits, and basic fairness checks. Ensure models are testable and their performance is measurable before deployment.

## Target Outcomes

- Model evaluation framework with standard metrics (accuracy, precision, recall, F1 or RMSE, MAE for regression)
- Cross-validation and holdout test set evaluation
- Basic fairness and bias checks across data segments
- Baseline metrics report with visualizations or summary tables
- PR with evaluation harness and baseline metrics report

## What Participants Will Learn

- How Devin structures model evaluation code with proper train/validation/test splits
- How Devin selects and implements appropriate metrics for different model types
- How Devin approaches fairness and bias evaluation across data segments
- The value of specifying evaluation criteria and data segments in prompts

## Devin Features Exercised

- ML evaluation methodology and statistical analysis
- Test framework design for ML models
- Data analysis and reporting
- PR creation with evaluation artifacts

## Difficulty

Advanced

## Estimated Time

60 minutes

## Notes

- This module pairs well with ML Pipeline Setup — run them sequentially for a complete ML workflow
- If no trained model exists yet, evaluation harness should include a simple baseline model for comparison
- Encourage participants to specify which data segments matter for fairness checks (e.g., loan type, borrower demographics)

---

## <a id="timesheet-app"></a>timesheet-app

**Repository:** [timesheet-app](https://github.com/Cognition-Partner-Workshops/timesheet-app)

React + Node.js/Express timesheet application — build an evaluation harness for a task duration prediction model using historical work entry data.

### Step 1: Paste into Devin

> Create a model evaluation framework for timesheet-app under `ml/evaluation/`. Build an evaluation harness that: (1) loads historical work entry data and splits it into train/validation/test sets with configurable ratios, (2) trains a baseline regression model (linear regression) and evaluates it, (3) computes RMSE, MAE, and R-squared metrics on each split, (4) generates a metrics report comparing performance across client segments and project types, and (5) checks for prediction bias (does the model systematically over- or under-predict for certain categories?). Output the results as a JSON report and a markdown summary. Include a README and requirements.txt. Open a PR.

### Step 2: Research with Ask Devin

- *"What data segments exist in the timesheet-app work entries? Are there different clients, project types, or time periods that should be evaluated separately?"*
- *"What is a reasonable baseline for task duration prediction? What RMSE would indicate a useful model versus random guessing?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the data model and identify which segments of work entries should be evaluated independently for fairness.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the evaluation harness properly isolate train/test data to prevent leakage?
- **Leave a comment** asking Devin to add a confusion matrix visualization for bucketed duration predictions (short/medium/long tasks)
- **Watch Devin respond** and push a follow-up commit

---

## <a id="uc-data-source-migration-jdbc-normalization"></a>uc-data-source-migration-jdbc-normalization

**Repository:** [uc-data-source-migration-jdbc-normalization](https://github.com/Cognition-Partner-Workshops/uc-data-source-migration-jdbc-normalization)

Spring Boot loan service — build an evaluation harness for a data quality scoring model that validates migration accuracy.

### Step 1: Paste into Devin

> Create a model evaluation framework for the data quality scoring pipeline in uc-data-source-migration-jdbc-normalization under `ml/evaluation/`. Build an evaluation harness that: (1) generates labeled test data by introducing known data quality issues (corrupted dates, invalid amounts, mismatched status codes) into clean records, (2) evaluates the anomaly detection model's precision and recall at identifying corrupted records, (3) runs cross-validation with 5 folds, (4) checks fairness across loan product types and borrower segments — does the model flag certain loan types disproportionately?, and (5) outputs a metrics report in both JSON and markdown format. Include a README explaining the evaluation methodology. Open a PR.

### Step 2: Research with Ask Devin

- *"What types of data quality issues are defined in the column_mappings.md? How can we synthetically introduce each type of corruption for testing?"*
- *"What loan product types and borrower segments exist in the seed data? Are there enough records per segment for meaningful evaluation?"*

### Step 3 (Optional): Read the DeepWiki

Open the repo's DeepWiki page to understand the field transformation rules in `data/mappings/column_mappings.md`. Each transformation type (date parsing, amount parsing, code expansion) is a potential corruption category for evaluation.

### Step 4 (Optional): Review & Give Feedback

- **Review the diff** — does the synthetic corruption realistically model actual migration failures?
- **Leave a comment** asking Devin to add a precision-recall curve visualization and identify the optimal detection threshold
- **Watch Devin respond** and push a follow-up commit
