\# Airflow Operational Standards



\*\*Status:\*\* Proposed  

\*\*Owner:\*\* Platform Engineering



\## Goal

Define the operational rules for Airflow so pipelines are reliable, debuggable, and safe to run repeatedly.



---



\## 1) Scheduling vs SLA



\### Schedule

The schedule defines \*\*when\*\* the DAG is triggered (cron, daily, hourly, etc.).



\### SLA

SLA defines \*\*how long\*\* the run is allowed to take before it is considered late.



\*\*Rule:\*\* An SLA breach is an operational event and must alert.



---



\## 2) Retries \& Backoff (Default Policy)



Default settings unless a DAG explicitly requires different:



\- `retries`: 3  

\- `retry\_delay`: 5 minutes  

\- `exponential\_backoff`: true  

\- `max\_retry\_delay`: 60 minutes  

\- `depends\_on\_past`: false (default off unless justified)



\*\*Rule:\*\* Retries are for transient failures (network, temporary unavailability).  

\*\*Rule:\*\* Deterministic failures (schema mismatch, missing file) must fail fast and alert.



---



\## 3) Alerts \& Notifications



Minimum alerting:

\- Alert on final task failure

\- Alert on SLA miss (if defined)

\- Alert on DAG not scheduled / not running (heartbeat monitoring)



Alert destination:

\- Team channel + ticketing (or equivalent)

\- Include run id, task id, error summary, and links to logs



\*\*Rule:\*\* A silent failure is treated as a defect.



---



\## 4) Idempotency (Non-Negotiable)



DAGs must be safe to re-run for a given execution date.



Approved patterns:

\- Upsert/MERGE into target tables

\- Partition overwrite for the specific execution date

\- Write to temp + swap (atomic promotion)



Not approved:

\- Blind append to a “current state” table without dedupe keys



---



\## 5) Backfills



Backfills are expected and must be supported.



Rules:

\- Use `catchup` intentionally (explicit true/false with justification)

\- Ensure tasks handle late-arriving data

\- For large backfills, throttle concurrency and warehouse usage



\*\*Rule:\*\* Backfill plans must be documented for any DAG that drives business-critical reporting.



---



\## 6) Concurrency \& Resource Controls



Minimum standards:

\- Set DAG-level `max\_active\_runs`

\- Use task pools for constrained resources (APIs, warehouses)

\- Avoid unbounded parallelism



\*\*Rule:\*\* Concurrency limits must protect shared infrastructure from accidental overload.



---



\## 7) Logging \& Observability



Each DAG should emit structured run context:

\- dag\_id

\- run\_id

\- execution\_date / data interval

\- source identifier

\- record counts (read/written/quarantined)



\*\*Rule:\*\* Row counts and key metrics must be logged for each major stage (RAW, SILVER, GOLD).



---



\## 8) Failure Handling \& Runbooks



Every production DAG must have a runbook entry that covers:

\- What the DAG does

\- Inputs/outputs

\- Common failure modes

\- How to re-run safely

\- Who owns it



Preferred location:

\- `governance/runbooks/` (or link to an external runbook system)



---



\## 9) Change Management



\- Changes land via PR

\- Production-impacting changes require clear release notes

\- Any change to scheduling, retries, SLAs, or target tables must be called out in the PR



---



\## Done Criteria for a Production DAG

\- Defined schedule + (optional) SLA

\- Default retry policy applied or justified exception

\- Alerts enabled and tested

\- Idempotency verified

\- Backfill approach documented

\- Runbook exists or linked



