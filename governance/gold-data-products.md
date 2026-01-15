\# Gold Data Product Standards



\*\*Status:\*\* Proposed  

\*\*Owner:\*\* Data Platform + Analytics Engineering



\## Goal

Define what qualifies as a GOLD data product and the minimum standards for ownership, stability, and change control.



---



\## Definition

A GOLD data product is a curated dataset intended for broad consumption (BI, analytics, ML, downstream apps).



---



\## Minimum Requirements



\### 1) Ownership

Each product must declare:

\- owning team

\- business steward (if applicable)

\- technical contact



\### 2) Contract

\- stable schema (no breaking changes without versioning)

\- documented semantics for key fields

\- defined grain (row meaning)



\### 3) SLA / Freshness

\- expected update frequency (daily/hourly/etc.)

\- expected availability time window



\### 4) Quality

Minimum:

\- key uniqueness and not-null enforced (tests)

\- relationship integrity where applicable

\- metric sanity checks where feasible



\### 5) Lineage

\- source systems listed

\- RAW → SILVER → GOLD path documented



---



\## Backward Compatibility Rules

Breaking changes require one of:

\- versioned dataset (v1/v2)

\- deprecation period with notice

\- release note in changelog



---



\## Deprecation

A deprecated product must include:

\- replacement dataset (if any)

\- last supported date

\- migration guidance



---



\## Done Criteria

A GOLD product is “production ready” when:

\- it has an owner

\- it has documented schema/grain

\- it has minimum tests

\- it has a declared SLA/freshness expectation

\- changes are governed via PR



