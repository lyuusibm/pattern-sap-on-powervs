---

copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: pattern-sap-on-powervs

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for resiliency
{: #resiliency-decisions}

+-----------+-----------+--------------+-------------------------------+
| **Arc     | **Requ    | **Decision** | **Rationale**                 |
| hitecture | irement** |              |                               |
| d         |           |              |                               |
| ecision** |           |              |                               |
| High      | Provide   | DB native    | Single zone high availability |
| Ava       | 99.95%    | replication: | deployment of SAP application |
| ilability | ava       |              | and database is supported.    |
| SAP       | ilability | HANA System  |                               |
|           | for SAP   | Replication  | Multiple/Clustered SAP        |
|           |           | (HSR)        | application servers can be    |
|           |           |              | configured.                   |
|           |           | Oracle       |                               |
|           |           | DataGuard    |                               |
|           |           |              |                               |
|           |           | DB2 HADR     |                               |
|           |           |              |                               |
|           |           | Sybase       |                               |
|           |           | Replication  |                               |
|           |           | Server       |                               |
|           |           |              |                               |
|           |           | MSSQL        |                               |
|           |           | Always-On    |                               |
|           |           |              |                               |
|           |           | Cluster:     |                               |
|           |           |              |                               |
|           |           | Pac          |                               |
|           |           | emaker-based |                               |
|           |           | cluster with |                               |
|           |           | Red Hat      |                               |
|           |           | Enterprise   |                               |
|           |           | Linux HA     |                               |
|           |           | Add-On, or   |                               |
|           |           | with SUSE    |                               |
|           |           | Linux High   |                               |
|           |           | Availability |                               |
|           |           | Extension    |                               |
| High      | Provide   | -            | -   Minimize cost,            |
| Ava       | 99.95%    |    Redundant |     implementation and        |
| ilability | ava       |     LPARs in |     maintenance complexity,   |
| Infra     | ilability |     an [SAP  |     potential latency and     |
| structure | for       |              |     maximize value with IBM   |
|           | infra     | Scale-out](h |     solutions.                |
|           | structure | ttps://cloud |                               |
|           |           | .ibm.com/doc | -   "Different Server"        |
|           |           | s/sap?topic= |     placement group should be |
|           |           | sap-refarch- |     chosen to build high      |
|           |           | hana-scaleou |     availability within the   |
|           |           | t#network-la |     same data center.         |
|           |           | yout-for-sca |                               |
|           |           | le-out-confi | -   PowerHA can also be       |
|           |           | gurations-2) |     implemented for cluster   |
|           |           |              |     management if AIX is the  |
|           |           |   deployment |     operating system.         |
|           |           |     with VPC |                               |
|           |           |     ALB      |                               |
|           |           |     solution |                               |
|           |           |     on a     |                               |
|           |           |              |                               |
|           |           |  single-zone |                               |
|           |           |     can      |                               |
|           |           |     provide  |                               |
|           |           |     an       |                               |
|           |           |              |                               |
|           |           |  application |                               |
|           |           |     SLA of   |                               |
|           |           |     99.95%   |                               |
| Disaster  | Disaster  | IBM Storage  | -   IBM Storage Protect Plus  |
| Recovery  | recovery  | Protect      |     provides near instant     |
| for SAP   | c         |              |     recovery and replication  |
| on        | apability |              |     in hybrid multi-cloud     |
| Linux/AIX | in        |              |     environments.             |
|           | secondary |              |     <https://www.ib           |
|           | region    |              | m.com/downloads/cas/L9MD4MEZ> |
|           |           |              |                               |
|           |           |              | -   Support for databases     |
|           |           |              |     Standard lead with        |
|           |           |              |     offering (MSSQL, Oracle,  |
|           |           |              |     HANA).                    |
|           |           |              |                               |
|           |           |              | -   Standard DR capability    |
|           |           |              |     for RPO \< 15 min, RTO \< |
|           |           |              |     4 hours is achievable.    |
|           |           |              |                               |
|           |           |              | -   Veeam is an alternative   |
|           |           |              |     for AIX as the operating  |
|           |           |              |     system.                   |
| Disaster  | Disaster  | Global       | <https://www.ibm.com/blog/an  |
| Recovery  | recovery  | Replication  | nouncement/introducing-global |
| for SAP   | c         | Service      | -replication-service-on-ibm-p |
| on        | apability | (GRS)        | ower-systems-virtual-server/> |
| AIX/IBM i | in        |              |                               |
|           | secondary |              | Global Replication Service    |
|           | region    |              | (GRS) is based on Global      |
|           |           |              | Mirror Replication as a       |
|           |           |              | configurable option-- only    |
|           |           |              | available in WDC and DAL as   |
|           |           |              | of now, will be available in  |
|           |           |              | all PowerVS regions soon      |
| Disaster  | HANA      | Implement    | Setup Shared processor Pool   |
| Recovery  | disaster  | Shared       | to reserve capacity in the    |
| -- Cost   | recovery  | Processor    | secondary region. Setup DR    |
| Optimized | c         | Pool         | systems on minimum sized VMs  |
|           | apability |              | to save operating cost. This  |
|           | in        |              | is a Power systems virtual    |
|           | secondary |              | servers special feature.      |
|           | region    |              |                               |
|           |           |              | Alternative: DR and           |
|           |           |              | Non-Production systems to     |
|           |           |              | share the same                |
|           |           |              | infrastructure.               |
| Backup -  | Back up   | IBM Storage  | IBM Storage Protect is an SAP |
| Linux     | of        | Protect      | certified backup tool for SAP |
|           | NetWeaver |              | HANA and works with Backint.  |
|           | and DB    |              |                               |
|           | data      |              | IBM Storage Protect Agent for |
|           |           |              | all OS including AIX if the   |
|           |           |              | client currently has SW ELA   |
|           |           |              | licensing. IBM Storage        |
|           |           |              | Protect works seamlessly with |
|           |           |              | native SAP backup tools;      |
|           |           |              | provides additional           |
|           |           |              | functionality like encryption |
|           |           |              | of backed-up data data in     |
|           |           |              | transit; built-in             |
|           |           |              | deduplication compresses      |
|           |           |              | storage without additional    |
|           |           |              | dedupe hardware               |
| Backup -  |           | IBM Storage  | Alternative: Veeam is         |
| AIX       |           | Protect      | supported on AIX. However, if |
|           |           |              | there is HANA/Linux in        |
|           |           |              | client's landscape, only one  |
|           |           |              | SAP certified backup tool     |
|           |           |              | should be used. Then IBM      |
|           |           |              | Storage Protect is            |
|           |           |              | recommended.                  |
| B         |           | Veeam V12    | SAP HANA server itself,       |
| ackup-AIX |           |              | image-level and file-level    |
|           |           |              | backup functionality of Veeam |
|           |           |              | Backup & Replication or Veeam |
|           |           |              | Agent for Linux.              |
|           |           |              |                               |
|           |           |              | Image and file level backups  |
|           |           |              | of SAP HANA servers do not    |
|           |           |              | guarantee                     |
|           |           |              | transaction-consistency of    |
|           |           |              | database backups.             |
| Backup -- |           | FalconStor   | Although SAP on IBM i/Power   |
| IBM i     |           | VTL          | Systems Virtual Servers is    |
|           |           |              | not SAP certified, client may |
|           |           |              | choose it if service provider |
|           |           |              | supports.                     |
| Backup -- | Point in  | FlashCopy    | IBM Power Systems Virtual     |
| other     | time copy |              | Servers offering              |
|           | of data   |              |                               |
|           | volume    |              |                               |
{: caption="Table 1. Architecture decsions for resiliency" caption-side="bottom"}
