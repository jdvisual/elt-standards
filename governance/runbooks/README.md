\# Incident Runbooks



\*\*Status:\*\* Proposed  

\*\*Owner:\*\* Platform Engineering



\## Purpose

Runbooks provide fast, repeatable steps to diagnose and recover from common platform incidents.



\## Principles

\- Keep steps actionable and ordered

\- Include links to logs/queries and exact commands where possible

\- Prefer “restore service safely” over “perfect root cause”

\- Always capture learnings and update the runbook after incidents



\## Minimum Runbook Sections (Template)

Every runbook should include:



1\. \*\*Symptoms\*\*

2\. \*\*Impact\*\*

3\. \*\*Immediate Checks\*\*

4\. \*\*Diagnosis Steps\*\*

5\. \*\*Mitigation / Recovery\*\*

6\. \*\*Safe Re-run Guidance\*\*

7\. \*\*Escalation / Ownership\*\*

8\. \*\*Follow-up / Prevention\*\*



\## Standard Metadata

At the top of each runbook include:

\- Status (Proposed/Approved/Deprecated)

\- Owner

\- Last Updated

\- Severity guidance (SEV2/SEV1 etc. if your org uses it)



\## Suggested Runbooks

\- Airflow DAG failure

\- Openflow ingestion failure

\- dbt job failure / test failure gate

\- Snowflake warehouse runaway cost

\- Data freshness / late source incident



