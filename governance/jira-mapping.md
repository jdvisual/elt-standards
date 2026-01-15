# Jira Mapping Guidance

**Status:** Proposed  
**Owner:** Platform Engineering

## Principle
Git is the source of truth for standards. Jira tracks implementation.

## Epic Structure
Create one Epic per domain folder:
- Snowflake Standards
- Openflow Standards
- Airflow Standards
- dbt Standards
- Governance

## Story Pattern
Stories should reference the canonical doc:
- Link to the file in GitHub
- Define “Done” as merged PR + any implementation completed

## Example Story Template
Title: Implement Snowflake RBAC Baseline Roles
Description:
- Reference: `snowflake/rbac.md`
Acceptance Criteria:
- Roles created in DEV
- Grants validated
- Document updated if changes were required