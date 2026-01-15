\# Openflow to Silver Alignment



\*\*Status:\*\* Proposed  

\*\*Owner:\*\* Ingestion Engineering + Analytics Engineering



\## Goal

Define how JSON payloads (stored as VARIANT in RAW) are promoted into stable, typed SILVER tables without breaking downstream models when payloads evolve.



---



\## 1) RAW Landing Contract (Non-Negotiable)



RAW is the immutable landing zone. Store events exactly as received with sufficient metadata to replay, dedupe, and audit.



\### Standard Columns



| Column | Type | Required | Description |

|---|---|---|---|

| payload | VARIANT | Yes | Full source JSON payload |

| received\_at | TIMESTAMP\_NTZ | Yes | Ingestion timestamp |

| source\_system | STRING | Yes | Source identifier |

| source\_event\_id | STRING | Yes | Source event ID |

| ingest\_run\_id | STRING | Yes | Pipeline run identifier |

| payload\_schema\_version | STRING | Optional | Schema version |

| landing\_hash | STRING | Optional | Payload hash |



---



\## 2) SILVER Table Contract



SILVER tables provide typed, stable schemas for downstream consumption.



Required columns:

\- Business keys (domain specific)

\- source\_event\_id

\- received\_at

\- ingest\_run\_id



---



\## 3) Extraction Pattern (VARIANT → Typed)



Use explicit casts when extracting fields.



Example:



```sql

select

&nbsp; payload:"claimId"::string  as claim\_id,

&nbsp; payload:"memberId"::string as member\_id,

&nbsp; source\_event\_id,

&nbsp; received\_at

from raw.raw\_kafka\_claims\_event



