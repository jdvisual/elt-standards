# Openflow to Silver Alignment

**Status:** Proposed  
**Owner:** Ingestion Engineering + Analytics Engineering

## Goal
Define how JSON payloads (stored as VARIANT in RAW) are promoted into stable, typed SILVER tables without breaking downstream models when payloads evolve.

## RAW Landing Standard
RAW tables must:
- Store the full payload in `payload` (VARIANT)
- Capture ingestion metadata:
  - `received_at` (TIMESTAMP)
  - `source_system` (STRING)
  - `source_event_id` (STRING)
  - `ingest_run_id` (STRING or UUID)
  - `payload_schema_version` (STRING, optional)

## SILVER Table Principles
SILVER tables:
- Have explicit, version-controlled schemas
- Are resilient to payload drift (additive changes should not break)
- Use deterministic keys where possible
- Track lineage back to RAW (via `source_event_id` / `ingest_run_id`)

## Recommended Pattern
1. RAW: land payload + metadata
2. SILVER: extract typed fields via dbt models (or controlled SQL views)
3. Reject/quarantine rows that fail schema expectations

## Example Mapping (Conceptual)
RAW:
- `raw_kafka_claims_event(payload VARIANT, received_at, source_event_id, ...)`

SILVER:
- `silver_claims_event(claim_id, member_id, service_date, amount, source_event_id, received_at, ...)`

## Handling Schema Drift
- Additive fields: safe; may be added to SILVER in a controlled release
- Breaking changes: require `payload_schema_version` and parallel models if needed
- Unknown fields: remain in RAW payload; SILVER extracts only approved fields

## Quality Gates (Minimum)
- `source_event_id` must be present
- Duplicate `source_event_id` handling must be defined (dedupe or upsert)
- Record-level failure reasons must be captured if quarantining is used