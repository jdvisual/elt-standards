# ELT Standards Handbook (Index)

This handbook defines the canonical standards for building, operating, and governing the ELT platform.
It is intended for onboarding, architecture review, and ongoing platform evolution.

---

## Governance & Process
- [Documentation Lifecycle](governance/doc-lifecycle.md)
- [Contributing Guidelines](CONTRIBUTING.md)
- [Jira Mapping Guidance](governance/jira-mapping.md)
- [Gold Data Product Standards](governance/gold-data-products.md)
- [Disaster Recovery & Backup Strategy](governance/disaster-recovery.md)
- [Edge-Case Handling Playbook](governance/edge-case-handling.md)

---

## Incident Runbooks
- [Runbooks Overview](governance/runbooks/README.md)
- [Airflow DAG Failure](governance/runbooks/airflow-dag-failure.md)

---

## Snowflake
- [Architecture Overview](snowflake/architecture.md)
- [Naming Standards](snowflake/naming-standards.md)
- [RBAC Overview](snowflake/rbac.md)
- [RBAC Detailed Standard](snowflake/rbac-standard.md)
- [Warehouse Standards](snowflake/warehouse-standards.md)
- [Performance & Cost Controls](snowflake/performance-and-cost.md)

---

## Openflow (Ingestion)
- [Overview](openflow/overview.md)
- [Naming Standards](openflow/naming-standards.md)
- [User & Role Setup](openflow/user-role-setup.md)
- [Security & Least Privilege](openflow/security-least-privilege.md)
- [Payload Handling](openflow/payload-handling.md)
- [Openflow → Silver Alignment](openflow/silver-alignment.md)

---

## Airflow (Orchestration)
- [DAG Standards](airflow/dag-standards.md)
- [Connections & Secrets](airflow/connections-secrets.md)
- [Operational Standards](airflow/operations-standards.md)
- [Airflow ↔ Snowflake Execution Contract](airflow/contract-with-snowflake.md)

---

## dbt (Transformation)
- [Project Structure](dbt/project-structure.md)
- [Modeling Standards](dbt/modeling-standards.md)
- [Incremental & Testing Strategy](dbt/incremental-and-testing.md)
- [Performance Guidelines](dbt/performance-guidelines.md)

---

## Repository Metadata
- [README](README.md)
- [CHANGELOG](CHANGELOG.md)
- [LICENSE](LICENSE)
