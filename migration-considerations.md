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

There are xeveral migration scenarios, the most straight-forward being a
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

| **Migration Method**                                        | **Technique Summary**                                                                                                                                                   | **Advantages and Concerns**                                                                                                                                                               | **Associated Tools**                                                                                                                                                                                     |
|-|-|-|-|
| Virtual Machine (VM) Backup and Restore                     | Use specialty tools to back up entire SAP instance(s) as virtual machine images and restore to VPC as Virtual server instances (VSIs).                                  |Lift and shift operation.                                                                                                                                                             | GLVM
|                                                             |                                                                                                                                                                         |Ensure tooling can support delta replication after the point-in-time move.                                                                                                            |
|                                                             |                                                                                                                                                                         |Depending upon the tools used, post migration adjustments may be necessary to mesh with the target environment in terms of network/systems addressing, interface enablement, etc      |                                                                                                                                                                                                          |
| Database Backup and Restore                                 | Backup the source database using native DBMS tools. Restore backup copy to a \"shell\" copy of source system prebuilt on PowerVS, using identical versions of software. |Time to transfer large database backups can be an issue.                                                                                                                              | SAP software to build \"shell\" system;
|                                                             |                                                                                                                                                                         |Target needs same/compatible vendor software to restore from backups file(s) received.                                                                                                | IBM Storage Protect (IBM Cloud Catalog)                                                                                                                                                                  |
| Fresh Build and Copy the Config                             | Build a fresh copy of the SAP system on PowerVS and copy/reenter configuration parameters as required.                                                                  | Good for applications and middleware servers not requiring data transfer. Risk of error introduction if post installation manual procedures are applied to reproduce exact configuration. | SAP software to build \"shell\" system;                                                                                                                                                                  |
| Database Replication/Continuous Data Protection (CDP) Tools | Establish replication between source replication and target database constructed on PowerVS.                                                                            | Verify that replication is supported between the source version of the database and the target version across the distances involved                                                      | SAP software to build \"shell\" system; Vendor specific database tools to configure and administer database replication (i.e. SQL Always on replication; HANA System Replication, Oracle DataGuard etc.) |
{: caption="Table 1. Homogeneous Migration" caption-side="bottom"}

**Heterogeneous Migration:**

| **Migration Method**                                                                                                                                                           | **Technique Summary**                                                                                                                                                                                                                                 | **Advantages and Concerns**                                                                                                                                                                                 | **Associated Tools**                                                                                                     |
|-|-|-|-|
| System Copy that uses [SWPM](https://support.sap.com/en/tools/software-logistics-tools/software-provisioning-manager.html)                                                     | Move or Re-Platform to different CPU Architecture, OS, or database. Uses System                                                                                                                                                                       | Commonly used to change database server in preparation for more significant move; for example, move to SAP HANA DB with a Classical Migration approach                                                      | Copy Export/Import of SWPM                                                                                               |
| SAP Export/Import                                                                                                                                                              | Export database from source system to target on PowerVS.  Transfer the export to PowerVS and import into a prebuilt \"shell\" version of same system, applying variations to software as required.                                                      | Time to transfer export across a network is a concern if database is large. Using SAP import/export procedures eliminates concern about having compatible backup/restore software at the source and target. | SAP software to build \"shell\" system; Aspera (optional) to expedite transfer of SAP export                             |
| Standard [DMO Migration](https://support.sap.com/en/tools/software-logistics-tools/software-update-manager/database-migration-option-dmo.html) - from on-premises to IBM Cloud | Use SAP SUM/DMO to migrate and upgrade application and/or database software and associated data from source location across the network to a \"shell\" target system on IBM Cloud PowerVS.                                                            | Technique supports upgrade of application and/or database software. Time to execute SUM/DMO across a network can be lengthy if source database is large and/or network connection is slow or unreliable.    | SAP software to build \"shell\" system; SAP SUM/DMO migration tool                                                       |
| Two Step Migration: Lift and Shift, followed by SUM/DMO transformation                                                                                                         | Perform a Lift and Shift Migration of source system to IBM Cloud PowerVS.  Build a shell target system in IBM Cloud that has applied necessary variations in software. Perform a SUM/DMO procedure within IBM Cloud to migrate/transfer the data to the new system.                                                                                  | Two Step migration approach can take a long time to execute. It is easier and faster to run a SUM/DMO procedure in this way because the source and target systems are co-located.                           | SAP software to build \"shell\" system; Aspera (optional) to expedite lift and shift transfer SAP SUM/DMO migration tool |
| Partner Tools (e.g. SNP Cockpit) (https://www.snpgroup.com)                                                                                                                    | Build a \"shell\" target system in VPC that has required software levels. Use SNP tools to analyze source system and transfer a subset of configuration, data, and/or technical debt from the source system across the network to the target system . | Supports application modernization, simplifies migration using custom tooling. Requires a robust network connection to accomplish the migration + transformation to the PowerVS-resident target system      | SAP installation software; SNP CrystalBridge (via IBM Consulting); SNP Cockpit (via IBM Consulting)                     |
{: caption="Table 2. Heterogeneous Migration" caption-side="bottom"}
