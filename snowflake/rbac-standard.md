# Snowflake RBAC Standard

**Status:** Proposed  
**Owner:** Security + Data Platform

## Purpose
Define a consistent Role-Based Access Control (RBAC) model for Snowflake that supports:
- least privilege
- environment isolation
- clear separation of duties (ingest vs transform vs read)
- scalable grants using role-to-role inheritance

## Core Concepts
**RBAC**: permissions are granted to **roles**; users/service accounts are assigned **roles**.

**Design rule**: avoid granting privileges directly to users. Prefer:
- object privileges → access roles
- access roles → functional roles
- functional roles → users/service accounts

## Role Types
### 1) Functional Roles (what a persona does)
Examples:
- `FUNC_INGEST`
- `FUNC_TRANSFORM`
- `FUNC_READONLY`
- `FUNC_ADMIN` (restricted)

Functional roles should NOT contain direct object grants when possible.

### 2) Access Roles (what objects can be accessed)
Access roles are where object-level grants live.
Examples:
- `AR_RAW_RW`
- `AR_SILVER_RW`
- `AR_GOLD_RO`
- `AR_MONITORING_RO`

### 3) Environment Roles (where access applies)
Prefer separate environments and separate roles per environment:
- DEV: `*_DEV`
- TEST: `*_TEST`
- PROD: `*_PROD`

## Recommended Naming
Role names should be consistent and searchable:

- Functional: `FUNC_{persona}_{env}`
  - Example: `FUNC_INGEST_DEV`
- Access: `AR_{domain}_{layer}_{perm}_{env}`
  - Example: `AR_CLAIMS_RAW_RW_DEV`
- Service accounts: `SVC_{tool}_{env}`
  - Example: `SVC_OPENFLOW_DEV`, `SVC_DBT_PROD`

## Baseline Role Hierarchy
Example inheritance pattern (DEV shown):

`SVC_OPENFLOW_DEV`
→ `FUNC_INGEST_DEV`
→ `AR_<domain>_RAW_RW_DEV`

`SVC_DBT_DEV`
→ `FUNC_TRANSFORM_DEV`
→ `AR_<domain>_SILVER_RW_DEV`
→ `AR_<domain>_GOLD_RW_DEV`

BI/Analyst user
→ `FUNC_READONLY_DEV`
→ `AR_<domain>_GOLD_RO_DEV`

## Minimal Privilege Guidance
### Ingestion (Openflow)
- USAGE on database + schema
- INSERT on RAW landing tables
- CREATE TABLE only if explicitly required by design (prefer pre-created landing tables)

### Transform (dbt)
- USAGE on db/schema
- SELECT on RAW
- CREATE/WRITE on SILVER/GOLD target schemas
- Optional: CREATE VIEW (if dbt uses views)

### Read-Only (BI)
- USAGE on db/schema
- SELECT on GOLD only (avoid RAW/SILVER unless required)

## Environment Isolation Rules
- No DEV role is granted to PROD users
- No cross-env role inheritance
- Separate warehouses per env/purpose when possible (compute separation is part of governance)

## Auditability & Operations
- Every service account has a single primary role
- No shared human accounts
- Use role grants that are easy to explain during audit:
  - “This service account can only insert into RAW landing tables”

## Anti-Patterns (avoid)
- Granting privileges directly to users
- Reusing the same role name across environments without suffixing
- Using one “all access” role broadly
- Allowing ingestion accounts to read GOLD by default

## Implementation Notes
This doc defines the **standard**. The exact role list will be instantiated per domain (e.g., claims, eligibility, providers).