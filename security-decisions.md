---

copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: pattern-sap-on-powervs

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for security
{: #security-decisions}

|**Architecture decision**|**Requirement**|**Decision** |**Rationale**
|-|-|-|-|
|Primary Storage                         | Ability to encrypt data at rest                                                                                 |PowerVS uses IBM FlashSystem Storage with AES-256 (Advanced Encryption Standard) hardware-based encryption                                                           | Industry-Standard AES-256 encryption as provided by FlashSystem Storage for PowerVS|
|Backup Storage                       | Ability to encrypt backups at rest                                                                              |Cloud Object Storage Encryption                                                                                                                                       | By default, all objects that are stored in IBM Cloud Object Storage are encrypted by using randomly generated keys and an all-or-nothing-transform (AONT)|
|Archive Storage                         |Encryption at rest| Provider managed encryption provided by block storage.                                                                                                                | The file systems associated within any Classic infrastructure is IBM Cloud Block Storage Volume or file storage. Block and File Storage allows for Provider managed encryption (IBM Cloud managed keys) which will be configured by default when storage is setup|
|HANA Data Encryption                    | Ability to encrypt SAP HANA data at rest                                                                        |HANA Data Volume Encryption (DVE)                                                                                                                                     | DVE encrypts HANA data at the persistence layer, protecting data stored on disk from unauthorized access at operating system level|
|Encryption in transit            |IaaS platform must support Data Encryption: \n - Client to server \n - App LPAR to DB LPAR                                                                     |FTPs and HTTPs protocols (client to server) \n Inflight encryption for HANA DB supported using TLS/SSL encryption for App to DB                                                                                                                       | Client to server encryption can be accomplished over HTTPs (SSL) \n File transfer encryption via FTPs \n SAP supports encryption between application and database using SSL
|Identity Access and Role Management (IDM)| Securely authenticate users for platform services and control access to resources consistently across IBM Cloud |IBM Cloud IAM                                                                                                                                                         | Use IAM access policies to assign users, service IDs, and trusted profiles access to resources within the IBM Cloud account
|Privileged Identity & Access Management | Privileged access management services for administrative purposes                                               |BYO Bastion host (or Privileged Access Gateway) with PAM SW deployed in Edge VPC                                                                                  | Securely access remote resources over the private network for management purposes; bastion accessed via SSH. Session recording, tracking all activities, successful or not, to note any potential threats
|                               |          |2FA Authentication though IBM Security Verify                                                                                                                     |
|Core Network Protection|- Strict separation of duties \n - Isolated security zones between environments \n -Isolated, private cloud environment|- Separate VPCs, Subnets, ACLs and Security Groups for non-SAP workloads that may exist in VPC \n - In addition to VPC capabilities, use of virtual firewalls deployed to the Edge/Transit VPC to provide advance FW and routing capabilities between VPC and PowerVS \n - Separation of application and DB in separate PowerVS LPARs well as separate production and non-Production environments|A design combination using: \n Separate VPCs (transit, management, workload) connected through transit gateway and the use of edge firewall capabilities. Subnets, Security Groups and ACLs to create an Edge/Transit VPC design along with isolated LPARs on PowerVS|
|Threat detection and response|- Boundary protection: highest level of isolation from external network threats \n - IPS/IDS protection at all ingress/egress \n - Unified Threat Management (UTM) Firewall|BYO Virtual Firewall (on VSI) in Edge VPC (deployed across availability zones) client choices: \n [Fortigate](https://cloud.ibm.com/catalog/content/ibm-fortigate-AP-HA-terraform-deploy-5dd3e4ba-c94b-43ab-b416-c1c313479cec-global)  \n  [Juniper vSRX](https://cloud.ibm.com/catalog/content/juniper-vsrx-catalog-deploy-1.4-dc1e707c-33dd-4321-b2a5-c22dbf0dd0ee-global) \n [Checkpoint Cloud Guard](https://cloud.ibm.com/catalog/content/checkpoint-iaas-gw-ibm-vpc-1.0.7-9ed8dbde-2931-45f5-a7a7-0c90ce0d2686-global) \n [Palo Alto](https://cloud.ibm.com/catalog/content/ibmcloud-vmseries-1.9-6470816d-562d-4627-86a5-fe3ad4e94b30-global)|Can be provided by Enterprise Network DMZ \n In addition, if client requires: \n - Virtual FW on VSI in the Transit/Edge VPC \n - Client preference however recommendation is Fortigate or Juniper \n - Fortigate supports native HA configuration \n - Fortigate and Juniper both support both IPS & IDS
{: caption="Table 1. Architecture decisions for security" caption-side="bottom"}
