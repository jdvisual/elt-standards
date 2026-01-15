\# Airflow ↔ Snowflake Execution Contract



\*\*Status:\*\* Proposed  

\*\*Owner:\*\* Platform Engineering + Data Platform



\## Goal

Define what Airflow is responsible for versus what Snowflake/dbt is responsible for, so the platform stays maintainable, auditable, and safe to rerun.



---



\## Airflow Responsibilities (What it should do)

\- Orchestrate execution order and scheduling

\- Trigger ingestion or transformation jobs

\- Enforce retries for transient failures

\- Record run metadata (run id, execution date, status)

\- Route alerts on failure/SLA breach

\- Support backfills safely



---



\## Snowflake Responsibilities (What Snowflake should do)

\- Store and process data (RAW/SILVER/GOLD)

\- Enforce schema contracts for SILVER/GOLD

\- Provide durable audit/log tables where needed

\- Perform compute-intensive transformations close to the data



---



\## dbt Responsibilities (Preferred transformation engine)

\- Implement transformation logic (SILVER/GOLD)

\- Enforce tests and documentation

\- Manage model dependencies and incremental strategy



---



\## Rules of Engagement (Non-Negotiable)



\### 1) Airflow must not contain business logic

Business rules belong in dbt or version-controlled SQL in Snowflake.



\### 2) Airflow tasks must be idempotent

Re-running the same execution date must not create duplicates or corrupt state.



Approved patterns:

\- MERGE/UPSERT

\- Partition overwrite for the execution date

\- Temp + atomic swap promotion



\### 3) Credentials and secrets never live in DAG code

Use Airflow Connections/Secret Backend and environment-specific credentials.



\### 4) Environment isolation

DEV Airflow does not write to PROD Snowflake.

No cross-environment role inheritance.



\### 5) Observability is required

Each DAG run should emit:

\- rows read/written/quarantined

\- target table identifiers

\- execution date / data interval

\- run id / batch id



---



\## Failure Semantics



\### Transient failure

Retry (network, temporary unavailable service).



\### Deterministic failure

Fail fast and alert (schema mismatch, missing required fields, invalid data contract).



---



\## Ownership

Every production DAG must have an owner:

\- team

\- escalation contact

\- runbook link (where to look, how to rerun safely)



