\# dbt Incremental + Testing Strategy



\*\*Status:\*\* Proposed  

\*\*Owner:\*\* Analytics Engineering



\## Goal

Define standards for dbt incremental modeling and minimum testing requirements so datasets are reliable, fast, and safe to change.



---



\## 1) Model Layering (Expected)

\- \*\*staging\*\*: source-aligned cleanup and normalization

\- \*\*intermediate\*\*: conforming joins and shared transforms

\- \*\*marts\*\*: business-ready datasets and curated products



\*\*Rule:\*\* business logic belongs in marts (or intermediate), not raw staging.



---



\## 2) When to Use Incremental Models



Use incremental when:

\- source volumes are large

\- daily/hourly deltas are significantly smaller than full history

\- SLA or cost requires partial processing



Avoid incremental when:

\- dataset is small (full refresh is simpler)

\- source is non-deterministic (no stable keys / no reliable timestamps)

\- dedupe logic cannot be defined clearly



---



\## 3) Incremental Requirements (Non-Negotiable)



Any incremental model must define:

\- `materialized: incremental`

\- a stable `unique\_key`

\- a clear incremental filter (timestamp or change capture field)

\- expected merge strategy



Minimum example:



```yaml

models:

&nbsp; my\_project:

&nbsp;   silver:

&nbsp;     +materialized: incremental

&nbsp;     +unique\_key: claim\_id

&nbsp;     +incremental\_strategy: merge



