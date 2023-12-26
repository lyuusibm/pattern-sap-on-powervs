---

copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: pattern-sap-on-powervs

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for service management
{: #service management}

+---------+---+------------------+---+-------------+--------------------------+
| **Archi | * |                  | * |             | **Rationale**            |
| tecture | * |                  | * |             |                          |
| dec     | R |                  | D |             |                          |
| ision** | e |                  | e |             |                          |
|         | q |                  | c |             |                          |
|         | u |                  | i |             |                          |
|         | i |                  | s |             |                          |
|         | r |                  | i |             |                          |
|         | e |                  | o |             |                          |
|         | m |                  | n |             |                          |
|         | e |                  | * |             |                          |
|         | n |                  | * |             |                          |
|         | t |                  |   |             |                          |
|         | * |                  |   |             |                          |
|         | * |                  |   |             |                          |
| Mon     |   | Provide          |   | SAP App:    | -   Provides templates   |
| itoring |   | Integration,     |   | SAP FRUN    |     for monitoring of    |
| (Appli  |   | Exception,       |   | (part of    |     technical systems    |
| cation) |   | Health and       |   | SAP Digital |     including their      |
|         |   | System           |   | Ops Suite)  |     instances, databases |
|         |   | Monitoring       |   |             |     and hosts.           |
|         |   |                  |   |             |                          |
|         |   |                  |   |             | ```{=html}               |
|         |   |                  |   |             | <!-- -->                 |
|         |   |                  |   |             | ```                      |
|         |   |                  |   |             | -   Displays the status  |
|         |   |                  |   |             |     of managed objects   |
|         |   |                  |   |             |     and detailed drill   |
|         |   |                  |   |             |     down to each single  |
|         |   |                  |   |             |     metric or event.     |
|         |   |                  |   |             |     Shows the history of |
|         |   |                  |   |             |     each metric in the   |
|         |   |                  |   |             |     Metric Monitor.      |
|         |   |                  |   |             |                          |
|         |   |                  |   |             | -   Automatic alert      |
|         |   |                  |   |             |     generation when      |
|         |   |                  |   |             |     thresholds are       |
|         |   |                  |   |             |     violated.            |
|         |   |                  |   | Instana     | -   Instana provides     |
|         |   |                  |   |             |     additional           |
|         |   |                  |   |             |     application          |
|         |   |                  |   |             |     performance metrics  |
|         |   |                  |   |             |     and automate         |
|         |   |                  |   |             |     application          |
|         |   |                  |   |             |     performance          |
|         |   |                  |   |             |     management for the   |
|         |   |                  |   |             |     Web, App, and        |
|         |   |                  |   |             |     Database tiers.      |
|         |   |                  |   |             |     Instana provides     |
|         |   |                  |   |             |     data and actionable  |
|         |   |                  |   |             |     insights to monitor  |
|         |   |                  |   |             |     the applications and |
|         |   |                  |   |             |     automate root-cause  |
|         |   |                  |   |             |     analysis.            |
| Mon     |   | Monitor and      |   | Infr        | -   A fully-automated    |
| itoring |   | correlate        |   | astructure: |     application          |
| Cloud   |   | performance      |   | Instana,    |     performance          |
| (Pl     |   | metrics and      |   | LogDNA      |     management (APM)     |
| atform) |   | events           |   |             |     solution             |
|         |   |                  |   |             |                          |
|         |   |                  |   |             | -   Automates root-cause |
|         |   |                  |   |             |     analysis by using    |
|         |   |                  |   |             |     event correlation,   |
|         |   |                  |   |             |     performance          |
|         |   |                  |   |             |     thresholds, errors,  |
|         |   |                  |   |             |     changes, and         |
|         |   |                  |   |             |     analysis of service  |
|         |   |                  |   |             |     level agreement      |
|         |   |                  |   |             |     (SLA) violations.    |
|         |   |                  |   |             |                          |
|         |   |                  |   |             | -   Provides full        |
|         |   |                  |   |             |     context across the   |
|         |   |                  |   |             |     application          |
|         |   |                  |   |             |     infrastructure       |
|         |   |                  |   |             |     supporting all       |
|         |   |                  |   |             |     physical, virtual,   |
|         |   |                  |   |             |     and serverless       |
|         |   |                  |   |             |     services and         |
|         |   |                  |   |             |     functions            |
|         |   |                  |   |             |                          |
|         |   |                  |   |             | -   Provides data and    |
|         |   |                  |   |             |     actionable insights  |
|         |   |                  |   |             |     to monitor the       |
|         |   |                  |   |             |     applications,        |
|         |   |                  |   |             |     understand and       |
|         |   |                  |   |             |     respond to           |
|         |   |                  |   |             |     system-wide          |
|         |   |                  |   |             |     performance changes, |
|         |   |                  |   |             |     optimize resource    |
|         |   |                  |   |             |     utilization, and get |
|         |   |                  |   |             |     a unified view of    |
|         |   |                  |   |             |     operational health.  |
| Logging |   | Diagnose issues, |   | [IBM Log    | Recommended tool for     |
|         |   | analyze stack    |   | Analysis    | infra Logging for any    |
|         |   | traces and       |   | ](https://c | non-VMWare workloads.    |
|         |   | exceptions,      |   | loud.ibm.co | Ingestion and            |
|         |   | identify the     |   | m/docs/log- | integration with other   |
|         |   | source of        |   | analysis?to | tools for diagnosis and  |
|         |   | errors, and      |   | pic=log-ana | alerts                   |
|         |   | monitor          |   | lysis-getti |                          |
|         |   | different log    |   | ng-started) |                          |
|         |   | sources through  |   |             |                          |
|         |   | a single view    |   |             |                          |
| A       |   | Provide tracking |   | -   [IBM    | IBM Cloud Activity       |
| lerting |   | and alerting     |   |     Cloud   | Tracker provides         |
|         |   | functions across |   |             | interfaces to capture,   |
|         |   | application and  |   |    Activity | store, view, search, and |
|         |   | infrastructure.  |   |     Tracker | monitor API activity and |
|         |   |                  |   |     with    | supports the             |
|         |   |                  |   |     LogDNA] | configuration of alerts  |
|         |   |                  |   | (https://cl | to send notifications on |
|         |   |                  |   | oud.ibm.com | one or more target       |
|         |   |                  |   | /docs/power | channels.                |
|         |   |                  |   | -iaas?topic |                          |
|         |   |                  |   | =power-iaas |                          |
|         |   |                  |   | -at-events) |                          |
|         |   |                  |   |             |                          |
|         |   |                  |   | -   Pager   |                          |
|         |   |                  |   |     Duty +  |                          |
|         |   |                  |   |             |                          |
|         |   |                  |   |  ServiceNow |                          |
|         |   |                  |   |             |                          |
|         |   |                  |   |    (SNOW) + |                          |
|         |   |                  |   |             |                          |
|         |   |                  |   |    Customer |                          |
|         |   |                  |   |     SIEM    |                          |
|         |   |                  |   |             |                          |
|         |   |                  |   | -           |                          |
|         |   |                  |   |   Instana - |                          |
|         |   |                  |   |     Full    |                          |
|         |   |                  |   |     stack   |                          |
|         |   |                  |   |     ob      |                          |
|         |   |                  |   | servability |                          |
|         |   |                  |   |     for     |                          |
|         |   |                  |   |             |                          |
|         |   |                  |   | application |                          |
|         |   |                  |   |     and     |                          |
|         |   |                  |   |     inf     |                          |
|         |   |                  |   | rastructure |                          |
| Mana    |   | Automate         |   | Ansible +   |                          |
| gement/ |   | management       |   | Terraform   |                          |
|         |   | processes to     |   |             |                          |
| Orches  |   | keep             |   |             |                          |
| tration |   | applications and |   |             |                          |
|         |   | infrastructure   |   |             |                          |
|         |   | secure, up to    |   |             |                          |
|         |   | date, and        |   |             |                          |
|         |   | available        |   |             |                          |
{: caption="Table 1. Architecture decisions for service management" caption-side="bottom"}
