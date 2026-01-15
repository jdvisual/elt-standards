\# Edge-Case Handling Playbook (ELT)



\*\*Status:\*\* Proposed  

\*\*Owner:\*\* Data Platform + Platform Engineering + Analytics Engineering  

\*\*Last Updated:\*\* 2026-01-15



\## Goal

Define standard handling for common pipeline edge cases so data quality issues do not silently corrupt downstream datasets.



---



\## 1) Late-Arriving Data



\### Problem

Records arrive after the normal processing window (e.g., yesterday’s claims arrive today).



\### Standard Approach

\- SILVER/GOLD builds must support a configurable “lookback window” (e.g., last 3–7 days)

\- Incremental models should reprocess a bounded window rather than only “today”



\### Approved Patterns

\- \*\*Incremental with lookback filter\*\* (date-based)

\- \*\*Partition overwrite\*\* for the window

\- \*\*MERGE\*\* where keys represent latest-state



\### Anti-pattern

\- Only processing new rows by ingestion time with no lookback (misses late data)



---



\## 2) Duplicate Events / Replays



\### Problem

The same event is delivered more than once (source retries, Kafka replay, Openflow re-delivery).



\### Standard Approach

\- RAW stores duplicates (do not drop silently)

\- SILVER must dedupe deterministically using a stable key



\### Preferred Dedupe Keys (in order)

1\. `source\_event\_id` (best)

2\. business key + event timestamp

3\. payload hash + received\_at (last resort)



\### Rule

SILVER promotion must be idempotent:

\- reruns do not create duplicate business records



---



\## 3) Out-of-Order Events



\### Problem

Events arrive not in chronological order (common with distributed systems).



\### Standard Approach

\- Use event time (from payload) where available

\- Maintain “latest-state” tables via MERGE on keys + ordering rules



\### Rule

Define the “winner” deterministically:

\- latest event\_time wins, or

\- highest sequence number wins



---



\## 4) Schema Drift in JSON Payloads



\### Problem

Payload adds/removes/renames fields over time.



\### Standard Approach

\- RAW preserves full payload as VARIANT

\- SILVER schemas are explicit and version-controlled



\### Safe Drift (Additive)

\- new fields added → non-breaking

\- extraction logic may ignore unknown fields



\### Breaking Drift

\- renamed fields

\- type changes (string → object)

\- removed required attributes



\### Required Response to Breaking Drift

\- create/record a schema version (`payload\_schema\_version` if available)

\- update extraction to support both old and new (if feasible)

\- otherwise version the SILVER model/table (v1/v2) with deprecation plan



---



\## 5) Bad Records / Validation Failures (Quarantine)



\### Problem

Malformed JSON, missing keys, invalid types, impossible values.



\### Standard Approach

Bad records must not disappear. Choose one:



\### Option A — Quarantine Table

\- Store rejected rows with:

&nbsp; - reason\_code

&nbsp; - reason\_detail

&nbsp; - received\_at

&nbsp; - source\_event\_id

&nbsp; - payload



\### Option B — Status Columns

\- Keep record in table but include:

&nbsp; - record\_status (VALID/INVALID)

&nbsp; - record\_error



\### Rule

Quarantine count must be observable (alerts/metrics).



---



\## 6) Partial Loads / Mid-Run Failure



\### Problem

A run fails halfway through, leaving partial data written.



\### Standard Approach

Prefer atomic patterns:

\- load into temp/staging table

\- validate counts

\- swap/merge into target



\### Rule

If atomic swap is not possible, ensure rerun logic is idempotent.



---



\## 7) Backfills (Large Historical Reprocessing)



\### Problem

Need to reload weeks/months of history (late fixes, new logic).



\### Standard Approach

\- throttle backfills (batch by date range)

\- run outside peak BI windows

\- monitor Snowflake credits and query duration



\### Rule

Backfills must not overload production workloads.



---



\## 8) Data Freshness Issues



\### Problem

Source is late or missing entirely.



\### Standard Approach

\- detect via source freshness checks (dbt freshness where applicable)

\- alert on late sources

\- optionally “hold” downstream GOLD publish until inputs are present



\### Rule

Freshness issues must be detectable and visible.



---



\## Ownership \& Escalation

\- Platform Engineering: Airflow scheduling/infra, retries, connections

\- Data Platform: Snowflake patterns, RBAC, warehouse behavior

\- Analytics Engineering: dbt models, tests, contract enforcement



---



\## Done Criteria (Edge Case Ready)

A pipeline is “edge-case ready” when:

\- it can rerun safely (idempotent)

\- it handles late data (lookback strategy)

\- it dedupes deterministically

\- it quarantines bad records with visibility

\- it detects freshness issues



