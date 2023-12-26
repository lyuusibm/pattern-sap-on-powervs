---

copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: pattern-sap-on-powervs

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Migration design
{: #migration-design}

There are several migration scenarios, the most straight-forward being a
direct "lift-and-shift" (homogeneous) where the Infrastructure and SAP
versions remain the same, for example on-prem SAP NetWeaver ABAP 7.0
running on VMWare to the same environment on cloud. Migration from
on-prem to another infrastructure platform or SAP version
(heterogeneous) can become more complex, especially when combined with a
DB conversion or application server upgrade.

For a list of migration options and tools, see [Moving SAP Workloads](https://cloud.ibm.com/docs/sap?topic=sap-faq-moving-sap-workloads#faq-moving-sap-workloads-overview).

There are two types of SAP migration categories:

Homogeneous - Target has the same OS and software versions as the source

Heterogeneous - Target has a different OS, software or database type or
version than the source

The following tables summarize various approaches for a homogeneous and
heterogeneous migration; all dependent on client environment(s) and
preferences.

**Homogeneous Migration:**

+------------+-----------------+-------------------+------------------+
| *          | **Technique     | **Advantages and  | **Associated     |
| *Migration | Summary**       | Concerns**        | Tools**          |
| Method**   |                 |                   |                  |
| Virtual    | Use specialty   | -   Lift and      | GLVM             |
| Machine    | tools to back   |     shift         |                  |
| (VM)       | up entire SAP   |     operation.    |                  |
| Backup and | instance(s) as  |                   |                  |
| Restore    | virtual machine | -   Ensure        |                  |
|            | images and      |     tooling can   |                  |
|            | restore to VPC  |     support delta |                  |
|            | as Virtual      |     replication   |                  |
|            | server          |     after the     |                  |
|            | instances       |     point-in-time |                  |
|            | (VSIs).         |     move.         |                  |
|            |                 |                   |                  |
|            |                 | -   Depending     |                  |
|            |                 |     upon the      |                  |
|            |                 |     tools used,   |                  |
|            |                 |     post          |                  |
|            |                 |     migration     |                  |
|            |                 |     adjustments   |                  |
|            |                 |     may be        |                  |
|            |                 |     necessary to  |                  |
|            |                 |     mesh with the |                  |
|            |                 |     target        |                  |
|            |                 |     environment   |                  |
|            |                 |     in terms of   |                  |
|            |                 |                   |                  |
|            |                 |   network/systems |                  |
|            |                 |     addressing,   |                  |
|            |                 |     interface     |                  |
|            |                 |     enablement,   |                  |
|            |                 |     etc           |                  |
| Database   | Backup the      | -   Time to       | SAP software to  |
| Backup and | source database |     transfer      | build \"shell\"  |
| Restore    | using native    |     large         | system;          |
|            | DBMS tools.     |     database      |                  |
|            | Restore backup  |     backups can   | IBM Storage      |
|            | copy to a       |     be an issue.  | Protect (IBM     |
|            | \"shell\" copy  |                   | Cloud Catalog)   |
|            | of source       | -   Target needs  |                  |
|            | system prebuilt |                   |                  |
|            | on PowerVS,     |   same/compatible |                  |
|            | using identical |     vendor        |                  |
|            | versions of     |     software to   |                  |
|            | software.       |     restore from  |                  |
|            |                 |     backups       |                  |
|            |                 |     file(s)       |                  |
|            |                 |     received.     |                  |
| Fresh      | Build a fresh   | Good for          | SAP software to  |
| Build and  | copy of the SAP | applications and  | build \"shell\"  |
| Copy the   | system on       | middleware        | system;          |
| Config     | PowerVS and     | servers not       |                  |
|            | copy/reenter    | requiring data    |                  |
|            | configuration   | transfer. Risk of |                  |
|            | parameters as   | error             |                  |
|            | required.       | introduction if   |                  |
|            |                 | post installation |                  |
|            |                 | manual procedures |                  |
|            |                 | are applied to    |                  |
|            |                 | reproduce exact   |                  |
|            |                 | configuration.    |                  |
| Database   | Establish       | Verify that       | SAP software to  |
| Re         | replication     | replication is    | build \"shell\"  |
| plication/ | between source  | supported between | system; Vendor   |
| Continuous | replication and | the source        | specific         |
| Data       | target database | version of the    | database tools   |
| Protection | constructed on  | database and the  | to configure and |
| (CDP)      | PowerVS.        | target version    | administer       |
| Tools      |                 | across the        | database         |
|            |                 | distances         | replication      |
|            |                 | involved          | (i.e. SQL Always |
|            |                 |                   | on replication;  |
|            |                 |                   | HANA System      |
|            |                 |                   | Replication,     |
|            |                 |                   | Oracle DataGuard |
|            |                 |                   | etc.)            |
{: caption="Table 1. Homogeneous Migration" caption-side="bottom"}

**\
**

**Heterogeneous Migration:**

+---------------+----------------+-------------------+----------------+
| **Migration   | **Technique    | **Advantages and  | **Associated   |
| Method**      | Summary**      | Concerns**        | Tools**        |
| System Copy   | Move or        | Commonly used to  | Copy           |
| that uses     | Re-Platform to | change database   | Export/Import  |
| [SWPM](ht     | different CPU  | server in         | of SWPM        |
| tps://support | Architecture,  | preparation for   |                |
| .sap.com/en/t | OS, or         | more significant  |                |
| ools/software | database. Uses | move; for         |                |
| -logistics-to | System         | example, move to  |                |
| ols/software- |                | SAP HANA DB with  |                |
| provisioning- |                | a Classical       |                |
| manager.html) |                | Migration         |                |
|               |                | approach          |                |
| SAP           | Export         | Time to transfer  | SAP software   |
| Export/Import | database from  | export across a   | to build       |
|               | source system  | network is a      | \"shell\"      |
|               | to target on   | concern if        | system; Aspera |
|               | Po             | database is       | (optional) to  |
|               | werVS.Transfer | large. Using SAP  | expedite       |
|               | the export to  | import/export     | transfer of    |
|               | PowerVS and    | procedures        | SAP export     |
|               | import into a  | eliminates        |                |
|               | prebuilt       | concern about     |                |
|               | \"shell\"      | having compatible |                |
|               | version of     | backup/restore    |                |
|               | same system,   | software at the   |                |
|               | applying       | source and        |                |
|               | variations to  | target.           |                |
|               | software as    |                   |                |
|               | required.      |                   |                |
| Standard [DMO | Use SAP        | Technique         | SAP software   |
| Migration](ht | SUM/DMO to     | supports upgrade  | to build       |
| tps://support | migrate and    | of application    | \"shell\"      |
| .sap.com/en/t | upgrade        | and/or database   | system; SAP    |
| ools/software | application    | software. Time to | SUM/DMO        |
| -logistics-to | and/or         | execute SUM/DMO   | migration tool |
| ols/software- | database       | across a network  |                |
| update-manage | software and   | can be lengthy if |                |
| r/database-mi | associated     | source database   |                |
| gration-optio | data from      | is large and/or   |                |
| n-dmo.html) - | source         | network           |                |
| from          | location       | connection is     |                |
| on-premises   | across the     | slow or           |                |
| to IBM Cloud  | network to a   | unreliable.       |                |
|               | \"shell\"      |                   |                |
|               | target system  |                   |                |
|               | on IBM Cloud   |                   |                |
|               | PowerVS.       |                   |                |
| Two Step      | Perform a Lift | Two Step          | SAP software   |
| Migration:    | and Shift      | migration         | to build       |
| Lift and      | Migration of   | approach can take | \"shell\"      |
| Shift,        | source system  | a long time to    | system; Aspera |
| followed by   | to IBM Cloud   | execute. It is    | (optional) to  |
| SUM/DMO       | PowerVS Build  | easier and faster | expedite lift  |
| t             | a shell target | to run a SUM/DMO  | and shift      |
| ransformation | system in IBM  | procedure in this | transfer SAP   |
|               | Cloud that has | way because the   | SUM/DMO        |
|               | applied        | source and target | migration tool |
|               | necessary      | systems are       |                |
|               | variations in  | co-located.       |                |
|               | software.      |                   |                |
|               |                |                   |                |
|               | Perform a      |                   |                |
|               | SUM/DMO        |                   |                |
|               | procedure      |                   |                |
|               | within IBM     |                   |                |
|               | Cloud to       |                   |                |
|               | mi             |                   |                |
|               | grate/transfer |                   |                |
|               | the data to    |                   |                |
|               | the new        |                   |                |
|               | system.        |                   |                |
| Partner Tools | Build a        | Supports          | SAP            |
| (e.g. SNP     | \"shell\"      | application       | installation   |
| Cockpit)      | target system  | modernization,    | software; SNP  |
| (https://www. | in VPC that    | simplifies        | CrystalBridge  |
| snpgroup.com) | has required   | migration using   | (via IBM       |
|               | software       | custom tooling.   | Consulting);   |
|               | levels. Use    | Requires a robust | SNP Cockpit    |
|               | SNP tools to   | network           | (via IBM       |
|               | analyze source | connection to     | Consulting);   |
|               | system and     | accomplish the    |                |
|               | transfer a     | migration +       |                |
|               | subset of      | transformation to |                |
|               | configuration, | the               |                |
|               | data, and/or   | PowerVS-resident  |                |
|               | technical debt | target system     |                |
|               | from the       |                   |                |
|               | source system  |                   |                |
|               | across the     |                   |                |
|               | network to the |                   |                |
|               | target system  |                   |                |
|               | .              |                   |                |
{: caption="Table 2. Heterogeneous Migration" caption-side="bottom"}
