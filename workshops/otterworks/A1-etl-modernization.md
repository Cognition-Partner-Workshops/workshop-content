# Lab: ETL Pipeline Modernization

## Objective

Migrate legacy cron-based Python ETL scripts to Apache Airflow DAGs with proper orchestration, credential management, and retry logic.

## What's Wrong

The ETL pipelines in `etl/scripts/` are standalone Python scripts run by cron. They have hardcoded credentials in `etl/config.ini`, no retry logic, no monitoring, and will fail silently. They need to be migrated to Apache Airflow.

Specific problems:
- **Plaintext credentials** in `etl/config.ini` (AWS keys, database passwords)
- **No orchestration** — cron has no concept of task dependencies, retries, or SLA tracking
- **Monolithic scripts** — each script is 300-500 lines in a single `main()` function with no separation of concerns
- **Silent failures** — bare `except: pass` blocks swallow errors
- **No tests** — zero test coverage on transformation logic
- **`print()` logging** — no structured logging, no way to search or alert on log output
- **`pandas` for everything** — will not scale past ~1M rows for the analytics pipeline
- **No connection reuse** — new `boto3` clients and `psycopg2` connections created inline every time

## Where to Look

- `etl/scripts/analytics_daily.py` — Main analytics pipeline, ~400 lines, monolithic. Extracts from SQS + DynamoDB, aggregates by user/document/file/hour, loads to S3 and PostgreSQL.
- `etl/scripts/audit_archive_weekly.py` — Archives old audit events from DynamoDB to S3 Glacier.
- `etl/scripts/search_reindex_weekly.py` — Full reindex of MeiliSearch from document-service and file-service APIs.
- `etl/scripts/storage_cleanup_daily.py` — Finds orphaned S3 objects and quarantines them.
- `etl/scripts/user_activity_daily.py` — Generates per-user activity reports from PostgreSQL and S3.
- `etl/config.ini` — Plaintext AWS credentials and database passwords.
- `etl/crontab` — Scheduling with no dependency management.
- `etl/requirements.txt` — Minimal, pinned to old versions.
- `etl/ETL_UPGRADE_GUIDE.md` — **Read this first.** It documents the full target state and all 9 migration axes.

## What Done Looks Like

- [ ] Airflow DAGs created in `etl/airflow/dags/` (one per legacy script)
- [ ] `etl/airflow/docker-compose.yml` for local Airflow development environment
- [ ] `etl/airflow/requirements.txt` with modern Airflow provider packages
- [ ] No credentials in config files — Airflow Connections and Variables used instead
- [ ] Each monolithic script split into separate Airflow tasks (extract, transform, load, report)
- [ ] Airflow provider hooks used (`S3Hook`, `SqsHook`, `DynamoDBHook`, `PostgresHook`) instead of raw `boto3`/`psycopg2`
- [ ] Retry policies configured on tasks (at least 2 retries with exponential backoff)
- [ ] Structured logging via Python `logging` module (not `print()`)
- [ ] `etl/airflow/tests/` with pytest tests for pure transformation functions
- [ ] Original business logic preserved (same S3 key formats, same SQL queries, same aggregation output)

## Hints (for facilitators — reveal progressively if participants are stuck)

### Hint 1 — Getting Started

Read `etl/ETL_UPGRADE_GUIDE.md` for the full migration plan. It lists all 9 migration axes and has a script-to-DAG mapping table. Start with the simplest script.

### Hint 2 — Specific Direction

Start with `storage_cleanup_daily.py` — it has the clearest extract/compare/act/report pattern that maps directly to four Airflow tasks. Then tackle `analytics_daily.py` which is the most complex.

### Hint 3 — Approach

Ask Devin to read `etl/ETL_UPGRADE_GUIDE.md` and `etl/scripts/storage_cleanup_daily.py`, then create an Airflow DAG that implements the same logic with proper `S3Hook`, task separation, retry policies, and structured logging. Use the same S3 key formats and DynamoDB table names.

## Time Budget

- ~60-90 minutes for one script migration (recommended: `storage_cleanup_daily.py` or `search_reindex_weekly.py`)
- Advanced participants can tackle multiple scripts or add PySpark for the analytics aggregation pipeline
- Full migration of all 5 scripts: 3-4 hours
