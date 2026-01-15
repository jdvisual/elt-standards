\# Openflow Security \& Least Privilege



\*\*Status:\*\* Proposed  

\*\*Owner:\*\* Security + Ingestion Engineering



\## Goal

Define baseline security rules for Openflow ingestion into Snowflake with least privilege and strong auditability.



---



\## Service Accounts

\- Service accounts only (no human interactive logins)

\- One service account per environment

\- One service account per tool where possible



Examples:

\- `SVC\_OPENFLOW\_DEV`

\- `SVC\_OPENFLOW\_PROD`



---



\## Role Model

Openflow service accounts should only have access required to land RAW events.



Minimum grants:

\- USAGE on database

\- USAGE on RAW schema

\- INSERT on RAW landing tables



Avoid by default:

\- SELECT on GOLD

\- WRITE on SILVER/GOLD

\- OWNERSHIP grants



---



\## Environment Isolation

\- DEV Openflow roles cannot write to TEST/PROD

\- Separate roles per environment

\- Separate warehouses per env/purpose if applicable



---



\## Auditability

All RAW rows must include:

\- `received\_at`

\- `source\_system`

\- `source\_event\_id`

\- `ingest\_run\_id`



\*\*Rule:\*\* ingestion must be traceable from source event → RAW row → SILVER record.



---



\## Key Rotation \& Credential Hygiene

\- Rotate credentials on an agreed schedule

\- Store secrets only in approved secret manager / Airflow Connections

\- No credentials in repos, wiki pages, or tickets



---



\## Anti-patterns (Avoid)

\- Shared service accounts across environments

\- Granting privileges directly to users

\- Broad roles like “INGEST\_ALL”

\- Allowing Openflow role to query unrelated domains



