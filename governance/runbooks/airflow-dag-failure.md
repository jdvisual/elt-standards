\# Runbook: Airflow DAG Failure



\*\*Status:\*\* Proposed  

\*\*Owner:\*\* Platform Engineering  

\*\*Last Updated:\*\* 2026-01-15



\## Symptoms

\- DAG run shows \*\*failed\*\*

\- One or more tasks are \*\*up for retry\*\* repeatedly

\- Alerts triggered for task failure or SLA miss



\## Impact

\- Downstream datasets may be stale (SILVER/GOLD not updated)

\- Reporting/ML products may be incorrect or delayed



\## Immediate Checks (2–5 minutes)

1\. Identify failing DAG and task:

&nbsp;  - dag\_id

&nbsp;  - run\_id

&nbsp;  - task\_id

2\. Open task logs for the most recent failed attempt

3\. Check if failure looks \*\*transient\*\* (network, timeout) vs \*\*deterministic\*\* (schema, missing data)



\## Diagnosis Steps

\### A) Transient failure indicators

\- Connection reset / DNS / temporary 5xx

\- Warehouse unavailable temporarily

\- API rate limiting



Actions:

\- allow retries to complete

\- if retries exhausted, re-run after service recovery



\### B) Deterministic failure indicators

\- SQL compilation error

\- Missing table/column

\- Data contract validation failure

\- Permissions/role error



Actions:

\- do not keep re-running blindly

\- identify what changed (recent PR, schema drift, secret rotation)

\- fix root issue then re-run safely



\## Common Root Causes \& Fixes

\### 1) Snowflake permission error

\- Confirm Airflow connection role

\- Confirm role has USAGE on DB/SCHEMA and correct DML rights



\### 2) Schema drift

\- Confirm RAW payload changed

\- Confirm SILVER extraction expects missing/renamed field

\- Implement additive handling or versioned extraction



\### 3) Warehouse / cost guardrails

\- Warehouse autosuspend disabled or size too large

\- Reduce warehouse size / set limits / reschedule backfills



\### 4) Secrets / credentials expired

\- Rotate secret in Airflow connection / secret backend

\- Validate connectivity from Airflow worker



\## Mitigation / Recovery

Goal: restore a successful run for the correct execution date.



Approved recovery actions:

\- Clear failed task(s) after the root issue is fixed

\- Re-run the DAG for the specific execution date

\- Backfill a limited range (throttled) if multiple days missed



\## Safe Re-run Guidance (Idempotency)

Before re-run, confirm the pipeline is safe:

\- MERGE/upsert or partition overwrite is used

\- No blind append to “latest-state” tables

\- Dedupe key exists (source\_event\_id or business key)



If not safe:

\- stop and escalate

\- implement idempotent logic before rerun



\## Escalation / Ownership

\- Platform Engineering: Airflow infra, connections, scheduling

\- Data Platform: Snowflake RBAC/warehouse issues

\- Analytics Engineering: dbt model logic/test failures



\## Follow-up / Prevention

After incident:

\- Add/adjust alerting (task failure, SLA, freshness)

\- Add tests or preflight checks

\- Update this runbook with new failure mode

\- Add a changelog entry if behavior changed



