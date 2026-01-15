\# Snowflake Performance \& Cost Controls



\*\*Status:\*\* Proposed  

\*\*Owner:\*\* Data Platform  

\*\*Last Updated:\*\* 2026-01-15



\## Goal

Ensure Snowflake usage remains performant, predictable, and cost-controlled as data volume and user count scale.



---



\## Core Principles

\- Storage is cheap; compute is not

\- Scale compute only when needed

\- Prefer fewer, well-sized warehouses over many always-on ones

\- Make cost visible and attributable



---



\## Warehouse Standards



\### Sizing Guidance

| Workload | Typical Size |

|---|---|

| Ingestion | XS–S |

| dbt transformations | S–M |

| BI / analytics | M–L |

| Ad-hoc exploration | XS (separate) |



\### Mandatory Settings

\- Auto-suspend: ≤ 60 seconds

\- Auto-resume: enabled

\- Multi-cluster: only when concurrency requires it



---



\## Query Performance Guidelines



\### Prefer

\- Filter early (WHERE before JOIN where possible)

\- Select only required columns

\- Use result cache where applicable



\### Avoid

\- SELECT \*

\- Cross joins without explicit intent

\- Large unbounded scans in BI queries

\- Repeated transformations of the same RAW data outside dbt



---



\## Table Design

\- RAW: immutable, append-only where possible

\- SILVER: typed, deduped, incremental-friendly

\- GOLD: curated, stable schema, consumer-ready



---



\## Cost Controls \& Monitoring

\- Separate warehouses per workload type

\- Monitor:

&nbsp; - credits by warehouse

&nbsp; - long-running queries

&nbsp; - frequent full table scans

\- Set budgets and alerts where available



---



\## Backfills \& Large Runs

\- Throttle backfills (batch by date range)

\- Avoid running large backfills during peak BI hours



---



\## Ownership

\- Data Platform owns warehouse configuration

\- Engineering teams own query efficiency

\- Cost anomalies must be investigated and documented



