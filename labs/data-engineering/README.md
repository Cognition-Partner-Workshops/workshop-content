# Data Engineering

Challenges focused on data warehouse migration, ETL pipeline modernization, data quality validation, and analytics platform transitions.

> **Job titles:** Data Engineer, Analytics Engineer, Data Architect, ETL Developer

## Modules

| Module | Difficulty | Time |
|--------|-----------|------|
| [DW Migration: Teradata to Snowflake](dw-migration-teradata-to-snowflake.md) | Intermediate–Advanced | 60 min |
| [DW Migration: Teradata to BigQuery](dw-migration-teradata-to-bigquery.md) | Intermediate–Advanced | 60 min |
| [Data Source Migration](data-source-migration.md) | Intermediate | 60 min |
| [ETL Pipeline Modernization](etl-pipeline-modernization.md) | Intermediate–Advanced | 60 min |
| [Data Quality & Validation](data-quality-validation.md) | Intermediate | 45 min |
| [SAS to Python/Snowflake](sas-to-python-snowflake.md) | Intermediate–Advanced | 60 min |
| [Informatica PowerCenter Analysis](informatica-powercenter-analysis.md) | Intermediate | 45 min |
| [Informatica PowerCenter to Snowflake Migration](informatica-to-snowflake-migration.md) | Advanced | 75 min |
| [COBOL Copybook to PySpark/JSON](cobol-copybook-to-pyspark-json.md) | Intermediate | 45 min |
| [SAS Migration Analysis](sas-migration-analysis.md) | Intermediate–Advanced | 75 min |
| [SAS CI/CD & Operationalization](sas-cicd-operationalization.md) | Intermediate–Advanced | 60 min |

## Repositories

| Repository | Compatible Modules |
|------------|--------------------|
| uc-dw-migration-teradata-to-snowflake | [DW Migration: Teradata to Snowflake](dw-migration-teradata-to-snowflake.md), [ETL Pipeline Modernization](etl-pipeline-modernization.md), [Data Quality & Validation](data-quality-validation.md) |
| uc-dw-migration-teradata-to-bigquery | [DW Migration: Teradata to BigQuery](dw-migration-teradata-to-bigquery.md) |
| uc-data-source-migration-jdbc-normalization | [Data Source Migration](data-source-migration.md) |
| ts-informatica-powercenter | [Informatica PowerCenter Analysis](informatica-powercenter-analysis.md), [Informatica PowerCenter to Snowflake Migration](informatica-to-snowflake-migration.md) |
| ts-cobol-carddemo | [COBOL Copybook to PySpark/JSON](cobol-copybook-to-pyspark-json.md) |
| ts-sas-legacy-analytics | [SAS to Python/Snowflake](sas-to-python-snowflake.md), [SAS Migration Analysis](sas-migration-analysis.md), [SAS CI/CD & Operationalization](sas-cicd-operationalization.md) |
| uc-data-migration-sas-to-snowflake | [SAS to Python/Snowflake](sas-to-python-snowflake.md) |
| uc-data-migration-sas-to-databricks | [SAS Migration Analysis](sas-migration-analysis.md), [SAS CI/CD & Operationalization](sas-cicd-operationalization.md) |

## When to Use This Category

- Data-focused audiences (data engineering, analytics, BI teams)
- Workshops showing Devin's ability to understand and transform data schemas, queries, and pipelines
- DW Migration and SAS to Python/Snowflake are particularly relevant for enterprises migrating off legacy analytics platforms
- Data Quality & Validation pairs well with any data migration module as a validation step
- The `uc-dw-migration-teradata-to-snowflake` repo was specifically curated for these challenges
- Informatica PowerCenter modules are ideal for enterprises migrating from on-prem Informatica ETL to cloud-native Snowflake architectures
- The [SAS Migration Analysis](sas-migration-analysis.md) module includes a **Programmatic Context Loop** section — prescriptive guidance on configuring DeepWiki, Knowledge notes, MCP servers, and environment blueprints so Devin can iteratively build understanding during migration analysis. Use this as a reference for any migration module where programmatic resource access is part of the workflow
