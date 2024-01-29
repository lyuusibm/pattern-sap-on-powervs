---

copyright:
  years: 2024
lastupdated: "2024-01-12"

subcollection: pattern-sap-on-powervs

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Migration design
{: #migration-design}

There are several migration scenarios, but the most straight-forward is a
direct "lift-and-shift" (homogeneous) where the Infrastructure and SAP
versions remain the same. For example, on-premises SAP NetWeaver ABAP 7.0
running on VMWare to the same environment on cloud. Migration from
on-premises to another infrastructure platform or SAP version
(heterogeneous) can become more complex, especially when combined with a
DB conversion or application server upgrade.

For a list of migration options and tools, see [Moving SAP Workloads](/docs/sap?topic=sap-faq-moving-sap-workloads#faq-moving-sap-workloads-overview).

There are two types of SAP migration categories:

Homogeneous
:    Target has the same OS and software versions as the source

Heterogeneous
:    Target has a different OS, software, or database type or
version than the source

The following tables summarize various approaches for a homogeneous and
heterogeneous migration; all dependent on client environments and
preferences.

## Homogeneous Migration
{: #homogeneous-migration}

| Migration method                                       | Technique summary                                                  | Advantages and concerns                                                 | Associated tools**                                                  |
|----|----|----|----|
| Virtual Machine (VM) Backup and Restore                     | Use specialty tools to back up entire SAP instances as virtual machine images and restore to VPC as Virtual server instances (VSIs).                                  | Lift and shift operation.                                                                                                                                                             | GLVM
|                                                             |                                                                                                                                                                         | Make sure that tools can support delta replication after the point-in-time move.                                                                                                            |
|                                                             |                                                                                                                                                                         | Depending upon the tools used, post-migration adjustments might be necessary to mesh with the target environment in terms of network/systems addressing, interface enablement, and so on      |                                                                                                                                                                                                          |
| Database Backup and Restore                                 | Backup the source database by using native DBMS tools. Restore a backup copy to a shell copy of the source system prebuilt on PowerVS by using identical versions of the software. |Time to transfer large database backups can be an issue.                                                                                                                              | SAP software to build a shell system;
|                                                             |                                                                                                                                                                         |Target needs the same or compatible vendor software to restore from backups files received.                                                                                                | IBM Storage Protect (IBM Cloud catalog)                                                                                                                                                                  |
| Fresh Build and Copy the Config                             | Build a fresh copy of the SAP system on PowerVS and copy or reenter configuration parameters as required.                                                                  | Good for applications and middleware servers not requiring data transfer. Risk of error introduction if postinstallation manual procedures are applied to reproduce exact configuration. | SAP software to build a shell system;                                                                                                                                                                  |
| Database Replication/Continuous Data Protection (CDP) Tools | Establish replication between source replication and a target database that is constructed on PowerVS.                                                                            | Verify that replication is supported between the source version of the database and the target version across the distances involved                                                      | SAP software to build shell system; Vendor-specific database tools to configure and administer database replication (that is, SQL Always on replication; HANA System Replication, Oracle DataGuard and so on) |
{: caption="Table 1. Homogeneous migration" caption-side="bottom"}

## Heterogeneous migration
{: #heterogeneous-migration}

| Migration method                                                                    | Technique summary                                                                                                                                   | Advantages and concerns                                                                                           | Associated tools                |
|----|----|----|----|
| System Copy that uses [SWPM](https://support.sap.com/en/tools/software-logistics-tools/software-provisioning-manager.html){: external}                                                     | Move or Re-Platform to different CPU Architecture, OS, or database. Uses System                                                                                                                                                                       | Commonly used to change database server in preparation for more significant move; for example, move to SAP HANA DB with a Classical Migration approach                                                      | Copy Export/Import of SWPM                                                                                               |
| SAP Export/Import                                                                                                                                                              | Export database from source system to target on PowerVS. Transfer the export to PowerVS and import into a prebuilt shell version of the same system, applying variations to software as required.                                                      | Time to transfer export across a network is a concern if database is large. Using SAP import/export procedures eliminates concern about having compatible backup/restore software at the source and target. | SAP software to build shell system; Aspera (optional) to expedite transfer of SAP export                             |
| Standard [DMO Migration](https://support.sap.com/en/tools/software-logistics-tools/software-update-manager/database-migration-option-dmo.html){: external} - from on-premises to IBM Cloud | Use SAP SUM/DMO to migrate and upgrade application or database software and associated data from source location across the network to a shell target system on IBM Cloud PowerVS.                                                            | The technique supports upgrade of application or database software. The time to run SUM/DMO across a network can be lengthy if source database is large or network connection is slow or unreliable.    | SAP software to build shell system; SAP SUM/DMO migration tool                                                       |
| Two-Step Migration: Lift and Shift, followed by SUM/DMO transformation                                                                                                         | Perform a Lift and Shift Migration of the source system to IBM Cloud PowerVS. Build a shell target system in IBM Cloud that applies the necessary variations in software. Perform a SUM/DMO procedure within IBM Cloud to migrate or transfer the data to the new system.                                                                                  | Two-Step migration approach can take a long time to run. It is easier and faster to run a SUM/DMO procedure in this way because the source and target systems are colocated.                           | SAP software to build shell system; Aspera (optional) to expedite lift and shift transfer SAP SUM/DMO migration tool |
| Partner Tools (for example SNP Cockpit) (https://www.snpgroup.com){: external}                             | Build a shell target system in VPC that has the required software levels. Use SNP tools to analyze source system and transfer a subset of configuration, data, or technical debt from the source system across the network to the target system. | Supports application modernization, simplifies migration by using custom tools. Requires a robust network connection to accomplish the migration + transformation to the PowerVS-resident target system      | SAP installation software; SNP CrystalBridge (through IBM Consulting); SNP Cockpit (through IBM Consulting)                     |
{: caption="Table 2. Heterogeneous migration" caption-side="bottom"}
