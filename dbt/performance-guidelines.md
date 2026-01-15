\# dbt Performance Guidelines



\*\*Status:\*\* Proposed  

\*\*Owner:\*\* Analytics Engineering  

\*\*Last Updated:\*\* 2026-01-15



\## Goal

Ensure dbt models scale efficiently as data volume grows while keeping build times predictable.



---



\## Incremental First Mindset

\- Default to incremental models for large fact tables

\- Use full-refresh only when required and approved

\- Always define a `unique\_key` for incremental models



Preferred strategies:

\- MERGE / UPSERT

\- Partition overwrite by date



---



\## Model Design

\- Keep models focused (single responsibility)

\- Break complex logic into intermediate models

\- Avoid deeply nested CTEs in final marts



---



\## Testing Strategy

\- Apply tests where they add value

\- Avoid expensive tests on massive tables without filters

\- Use freshness tests selectively on critical sources



---



\## Dependency Management

\- Use `ref()` consistently

\- Avoid circular dependencies

\- Group related models into folders for clarity



---



\## Build Optimization

\- Use `dbt run --select` for targeted builds

\- Avoid full project runs unnecessarily

\- Use tags to control execution scope



---



\## CI Considerations

\- Run lightweight tests in CI

\- Avoid full data scans in PR validation

\- Fail fast on schema/test errors



---



\## Anti-patterns

\- Full-refresh on large tables by default

\- Monolithic “do everything” models

\- Blind incremental logic without dedupe guarantees



