# Evaluation: A1 — ETL Pipeline Modernization

## Inputs
- **User:** `{{user_email}}`
- **Repo:** `{{git_repo}}`
- **Branch/PR:** `{{branch_or_pr}}`

## Instructions

Check out the specified branch/PR in `{{git_repo}}`. Evaluate whether the ETL modernization lab has been completed by checking each criterion below against the code on the branch.

Compare the branch against `main` to identify what the participant changed.

## Criteria (all must pass)

1. **Airflow DAGs exist:** At least one Airflow DAG file exists in `etl/airflow/dags/`. Each legacy script (`analytics_daily.py`, `audit_archive_weekly.py`, `search_reindex_weekly.py`, `storage_cleanup_daily.py`, `user_activity_daily.py`) should have a corresponding DAG.

2. **Docker Compose for Airflow:** `etl/airflow/docker-compose.yml` exists and defines an Airflow webserver, scheduler, and at least one worker or local executor.

3. **Requirements file:** `etl/airflow/requirements.txt` exists and includes Airflow provider packages (e.g. `apache-airflow-providers-amazon`, `apache-airflow-providers-postgres`).

4. **No plaintext credentials:** `etl/config.ini` is NOT imported or referenced by any DAG. DAGs use Airflow Connections/Variables (look for `S3Hook`, `PostgresHook`, `SqsHook`, `DynamoDBHook`, or `Variable.get()`) instead of raw `boto3` clients with hardcoded keys.

5. **Task separation:** Each DAG has multiple tasks (not one giant `PythonOperator`). Check that extract, transform, load, and report phases are separated into distinct tasks or task groups.

6. **Retry policies:** At least one DAG configures `retries` and `retry_delay` in `default_args` or on individual tasks.

7. **Structured logging:** DAGs use Python `logging` module (not `print()`). Check for `import logging` or `from airflow.utils.log` usage.

8. **Tests exist:** `etl/airflow/tests/` directory exists with at least one pytest test file testing transformation logic.

9. **Business logic preserved:** Spot-check at least one DAG (e.g. the analytics DAG) to verify it implements the same aggregation logic, S3 key formats, and SQL queries as the corresponding legacy script in `etl/scripts/`.

## Output Format

```
RESULT: PASS | FAIL

CRITERIA:
1. Airflow DAGs exist: PASS/FAIL — [details]
2. Docker Compose for Airflow: PASS/FAIL — [details]
3. Requirements file: PASS/FAIL — [details]
4. No plaintext credentials: PASS/FAIL — [details]
5. Task separation: PASS/FAIL — [details]
6. Retry policies: PASS/FAIL — [details]
7. Structured logging: PASS/FAIL — [details]
8. Tests exist: PASS/FAIL — [details]
9. Business logic preserved: PASS/FAIL — [details]

SUMMARY: [one sentence overall assessment]
```

Overall RESULT is PASS only if criteria 1-5 and 9 all pass (6-8 are recommended but not blocking).
