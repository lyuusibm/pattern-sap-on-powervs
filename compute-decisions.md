---

copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: pattern-sap-on-powervs

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for compute
{: #compute-decisions}

+----------+-------------+---------------+---------------------------+
| **Arch   | **Re        | **Decision**  | **Rationale**             |
| itecture | quirement** |               |                           |
| de       |             |               |                           |
| cision** |             |               |                           |
| Compute  | Target      | Power Virtual | -   Hardware flexibility  |
| (Ne      | environment | Server        |     limited to            |
| tWeaver) | to match    | LPAR(s)       |     non-production        |
|          | specific    |               |     environments          |
|          | workload    |               |                           |
|          | r           |               | -   For production use    |
|          | equirements |               |     the SAP-certified     |
|          | and be SAP  |               |     profiles              |
|          | certified   |               |                           |
| Compute  | Target      | Power Virtual | -   Hardware flexibility  |
|          | environment | Server        |     limited to            |
| (HANA)   | to match    | LPAR(s)       |     non-production        |
|          | workload    |               |     environments          |
|          | r           |               |                           |
|          | equirements |               | -   For production use    |
|          | and be SAP  |               |     the SAP-certified     |
|          | certified   |               |     profiles              |
| Backup   | Backup      | Bare Metal on | -   Bare Metal on Classic |
| server   | server      | Classic       |     can accommodate large |
| (        | sized for   |               |     storage requirements  |
| Spectrum | enough      |               |     for backups           |
| Storage  | available   |               |                           |
| Protect) | storage     |               |                           |
| Virtual  | Virtual     | VPC Virtual   |                           |
| Servers  | servers for | Servers       |                           |
| (Edge    | workloads   |               |                           |
| VPC)     | in the Edge |               |                           |
|          | VPC         |               |                           |
{: caption="Table 1. Architecture decisions for compute" caption-side="bottom"}
