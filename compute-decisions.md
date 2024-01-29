---

copyright:
  years: 2024
lastupdated: "2024-01-12"

subcollection: pattern-sap-on-powervs

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for compute
{: #compute-decisions}

| Architecture decision              | Requirement                             | Decision                | Rationale                                     |
|----|----|----|----|
| Compute (NetWeaver)                      | Target an environment to match specific workload requirements and be SAP certified |Power Virtual Server LPARs |Hardware flexibility limited to nonproduction environments \n For production use the SAP-certified profiles
| Compute (HANA)                                  | Target an environment to match workload requirements and be SAP certified          | Power Virtual Server LPARs |Hardware flexibility limited to nonproduction environments \n For production use the SAP-certified profiles
| Backup server (Spectrum Storage Protect) | Backup server sized for enough available storage                                | Bare Metal on Classic        |Bare Metal on Classic can accommodate large storage requirements for backups |
| Virtual Servers (Edge VPC)               | Virtual servers for workloads in the Edge VPC                                   | VPC Virtual Servers          |                                                                                  |
{: caption="Table 1. Architecture decisions for compute" caption-side="bottom"}
