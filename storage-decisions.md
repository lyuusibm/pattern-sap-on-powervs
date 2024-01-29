---

copyright:
  years: 2024
lastupdated: "2024-01-12"

subcollection: pattern-sap-on-powervs

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for storage
{: #storage-decisions}

| Architecture decision       | Requirement                                       | Decision                                                                                                                                           | Rationale      |
|----|----|----|----|
| Primary Storage - Production      | Tier 1 - 10 IOPS                                       | Flash Storage from IBM FS9000 series devices                                                                                                             | * Tier 1 HANA DB on Production \n * Recommended for large non-HANA DB \n * Maximize performance and optimize costs for peak IOPS workload \n * Using of a mix of Tiers is not supported|
| Primary Storage - nonproduction | Tier 3 - 3 IOPS                                        | Flash Storage from IBM FS9000 series devices                                                                                                             | Recommended for all non-DB tiers (App. Servers) including noncritical DB LPARs |                    |
| Backup Storage                    | Backup storage for servers                             | Bare Metal with local disk                                                                                                                               | Bare Metal on classic offers profiles to accommodate larger storage sizes.
|                                   |                                                        | Block storage                                                                                                                                            |Cloud Object Storage for offsite 2^nd^ copy for lower costs                 |
|                                   |                                                        | Cloud Object Storage                                                                                                                                     | Combine Block and Cloud Object Storage for long-term needs                                   |                    |
| Data or Storage Migration \n For more migration information, see [Moving SAP Workloads](/docs/sap?topic=sap-faq-moving-sap-workloads#faq-moving-sap-workloads-overview)| Migration tools from existing environment to PowerVS | Migration tools from existing environment to PowerVS                                                                                               |Image import obviates the need for testing and is useful for nonproduction or POC systems
|                                   |                                                        |DB DR Replication                                                                                                                                    |Using Native DB Replication minimizes the need for testing efforts
|                                   |                                                        |SAP Standard Migrations Tools                                                                                                                        |SAP Standard Migration options like SWPM, DMO
|                                   |                                                        |Backup and Restore tools                                                                                                                                |Aspera for high-speed data transfer ideal for Migrations
|                                   |                                                        |Aspera data transfer                                                                                                                                 | Minimize
|                                   |                                                        | GLVM                                                                                                                                                 | Business disruption due to migration
|                                   |                                                        |     | Cost and efforts that are incurred in testing migrated databases                     |                    |
{: caption="Table 1. Architecture decisions for storage" caption-side="bottom"}
