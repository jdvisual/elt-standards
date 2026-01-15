\# Disaster Recovery \& Backup Strategy



\*\*Status:\*\* Proposed  

\*\*Owner:\*\* Platform Engineering + Data Platform  

\*\*Last Updated:\*\* 2026-01-15



\## Goal

Define recovery expectations, responsibilities, and mechanisms for the ELT platform in the event of system failure, data corruption, or regional outage.



---



\## Scope

This document covers:

\- Snowflake (data + compute)

\- Airflow (orchestration)

\- dbt (transformation artifacts)

\- Source ingestion (Openflow)



---



\## Recovery Objectives



| Component | RPO (Data Loss) | RTO (Restore Time) |

|---|---:|---:|

| Snowflake RAW | Near-zero (event replay) | < 4 hours |

| Snowflake SILVER/GOLD | ≤ 24 hours | < 4 hours |

| Airflow Scheduler / Metadata | ≤ 24 hours | < 2 hours |

| dbt Artifacts | 0 (Git-backed) | < 1 hour |



\*\*RPO:\*\* Recovery Point Objective  

\*\*RTO:\*\* Recovery Time Objective



---



\## Snowflake Recovery Strategy



\### Built-in Protections

\- Time Travel enabled per environment

\- Fail-safe for catastrophic table loss

\- Immutable RAW landing tables where possible



\### Recovery Scenarios



\#### Accidental DELETE / UPDATE

\- Use Time Travel to restore table or schema

\- Re-run downstream dbt models if needed



\#### Corrupted Transformation Logic

\- Revert dbt code via Git

\- Re-run affected models incrementally or full-refresh as required



\#### Regional Snowflake Outage

\- Follow Snowflake incident guidance

\- Pause upstream ingestion if necessary

\- Resume pipelines once service is restored



---



\## Airflow Recovery Strategy



\### Metadata \& Configuration

\- DAG code is Git-backed and redeployable

\- Connections and variables must be backed by:

&nbsp; - secret backend OR

&nbsp; - infrastructure-as-code where available



\### Scheduler / Worker Failure

\- Restart scheduler/workers

\- Validate metadata DB integrity

\- Resume paused DAGs carefully



\### Lost Runs

\- Identify missed execution dates

\- Backfill in controlled windows

\- Monitor warehouse and downstream load



---



\## dbt Recovery Strategy



\- dbt code and configuration live in Git

\- Target database objects are rebuildable from source

\- Use:

&nbsp; - incremental re-runs where safe

&nbsp; - full refresh only when necessary and approved



Artifacts to regenerate if lost:

\- manifest.json

\- run\_results.json

\- docs site (optional)



---



\## Openflow / Ingestion Recovery



\### Event Replay

\- RAW payloads stored durably in Snowflake

\- Source systems should retain events long enough for replay where possible



\### Duplicate Protection

\- source\_event\_id + ingest\_run\_id used for dedupe

\- SILVER promotion must be idempotent



---



\## Backup Responsibilities



| Area | Owner |

|---|---|

| Snowflake retention settings | Data Platform |

| Airflow infra \& metadata | Platform Engineering |

| Secrets / credentials | Security / Platform |

| Git repositories | Engineering teams |



---



\## DR Testing \& Validation



\- Perform at least annual tabletop DR review

\- Periodically test:

&nbsp; - Time Travel restore

&nbsp; - dbt rebuild from scratch

&nbsp; - controlled Airflow backfill



Document outcomes and update this plan accordingly.



---



\## Out of Scope

\- Source system DR (owned by upstream teams)

\- BI tool DR (handled separately)



---



\## Escalation

In a declared DR event:

1\. Platform Engineering coordinates response

2\. Data Platform assesses data integrity

3\. Stakeholders are notified of expected recovery timelines



