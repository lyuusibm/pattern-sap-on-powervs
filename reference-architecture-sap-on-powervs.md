---

copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: pattern-sap-on-powervs

keywords:
# The release that the reference architecture describes

authors:
  - name: Doug Eppard

version: 1.0

deployment-url:

docs: https://cloud.ibm.com/docs/pattern-sap-on-powervs

content-type: reference-architecture

---

{{site.data.keyword.attribute-definition-list}}

# Oracle RAC on Power Virtual Server
{: #sap-on-vpc}
{: toc-content-type="reference-architecture"}
{: toc-version="1.0"}

## Architecture diagram
{: #architecture-diagram}

Figure 1 provides a high-level summary of the Use Case, a SAP
Single-zone, multi-region deployment on IBM Cloud PowerVS. The Primary
Region supports Production workloads on PowerVS. The Secondary Region
supports non-Production and Disaster Recovery workloads should the
customer have DR requirements. The components deployed to the Edge VPC
provide security functions and resource isolation to the IBM Cloud
workloads.

.

![A diagram of a computer network Description automatically
generated](./image1.png)

Figure 1

1.  Client network connectivity is accomplished through Direct Link with
    VPN access for MSPs.

2.  An Edge VPC is deployed which contains routing and security
    functions.

3.  Transit Gateway to Power Virtual Server hosting the SAP application
    and database(s)

4.  Public connectivity also routes through Cloud Internet Services
    (CIS) which can provide load balancing, failover, and DDoS services,
    then routes to the edge VPC

5.  Global Transit Gateway connecting the PowerVS environment across
    regions to facilitate replication for DR purposes.

Figure 2 illustrates a detailed network and component architecture for a
single-zone, multi-region deployment to facilitate disaster recovery.

![A diagram of a network Description automatically
generated](./image2.png)

Figure 2

Architecture Description

1.  Two separate IBM Cloud regions, one containing production, the other
    containing both non-production and DR.

2.  Client network connectivity is accomplished through Direct Links to
    each region with VPN access for MSPs.

3.  An Edge VPC is deployed which contains routing and security
    functions. For security purposes, all ingress and egress traffic
    will route through the Edge VPC. It contains an sFTP server, Bastion
    host (jump), Firewalls providing advanced security functions and the
    SAP router and Web Dispatcher.

4.  The Edge VPC is connected to PowerVS through a local Transit Gateway
    and hosts the SAP application and database(s).

5.  Public connectivity routes through Cloud Internet services which can
    provide load balancing, failover, and DDoS services, then routes to
    the edge VPC

6.  PowerVS contains SAP Application components hosted on redundant SAP
    certified LPARS in an [SAP
    Scale-out](https://cloud.ibm.com/docs/sap?topic=sap-refarch-hana-scaleout#network-layout-for-scale-out-configurations-2)
    environment.

7.  SAP HANA is hosted on separate SAP certified LPARs in the same zone,
    using local Tier 1 storage.

8.  Virtual Private endpoints are used to provide connectivity to cloud
    native services

9.  Global Transit Gateway connecting PowerVS across regions for data
    replication purposes between the two regions.

10. Multiple LPARs are used to provide 99.95% availability within a zone

11. Bare Metals in classic to provide backups using IBM Storage Protect


## Design scope
{: #design-scope}

Design decisions that need to be considered for an end-2-end SAP on
PowerVS deployment and which are covered in this accelerator include:

-   **Compute:** Bare Metal and Virtual Servers

-   **Storage:** Primary, Backup, and Archive

-   **Networking:** Enterprise Connectivity, Edge Gateways, Segmentation
    and Isolation, Cloud Native Connectivity and Load Balancing

-   **Security:** Data, Identity and Access Management, Infrastructure
    and Endpoint, Threat Detection and Response

-   **Resiliency:** Backup and Restore, Disaster Recovery, High
    Availability

-   **Service Management:** Monitoring, Logging, Alerting,
    Management/Orchestration

The Architecture Framework, described in [Introduction to the
Architecture
Framework](https://cloud.ibm.com/docs/architecture-framework?topic=architecture-framework-intro),
provides a consistent approach to designing cloud solutions by
addressing requirements across a pre-defined set of \"aspects\" and
\"domains\", which are technology-agnostic architectural areas that need
to be considered for any enterprise solution. It can be used as a guide
to make the necessary design and component choices to ensure the
applicable requirements for each \"aspect\" and \"domain\" have been
considered. Figure 3 below shows the domains that are covered in this
solution.

![A screenshot of a computer Description automatically
generated](./image3.png)

Figure 3


## Requirements
{: #requirements}

The following  represents a [baseline]{.underline} set of requirements
which we believe are applicable to most clients and critical to a
successful SAP deployment.

| **Aspect**         | **Requirement**                                                                                                                                                                                                                                                                                                                       |
| Network            | Enterprise connectivity to customer data center(s) to provide access to applications from on-prem.                                                                                                                                                                                                                                    |
|                    | Map and convert existing customer SAP Network functionality into IBM Cloud and PowerVS networking services                                                                                                                                                                                                                            |
|                    | Migrate/Redeploy customer IP addressing scheme within the IBM Cloud environment                                                                                                                                                                                                                                                       |
|                    | Provide network isolation with the ability to segregate applications based on attributes such as data classification, public vs internal apps and function                                                                                                                                                                            |
| Security           | Provide data encryption in transit and at rest                                                                                                                                                                                                                                                                                        |
|                    | Migrate customer IDS/IAM Services to target IBM Cloud environment                                                                                                                                                                                                                                                                     |
|                    | Retain the same firewall rulesets across existing DCs                                                                                                                                                                                                                                                                                 |
|                    | Firewalls must be restrictively configured to provide advanced security features and prevent all traffic, both inbound and outbound, except that which is specifically required, documented, and approved, and include IPS/IDS services                                                                                               |
| Resiliency         | Multi-site capability to support a disaster recovery strategy and solution leveraging IBM Cloud infrastructure DR capabilities                                                                                                                                                                                                        |
|                    | Provide backups for data retention                                                                                                                                                                                                                                                                                                    |
|                    | RTO/RPO = 4 hours/15 minutes; Rollback to original environments should occur no later than specified RTOs                                                                                                                                                                                                                             |
|                    | 99.95 Availability                                                                                                                                                                                                                                                                                                                    |
|                    | Backups                                                                                                                                                                                                                                                                                                                               |
|                    |                                                                                                                                                                                                                                                                                                                                       |
|                    | -   Prod: Daily Full, logs per SAP product standard, 30 days retention time                                                                                                                                                                                                                                                           |
|                    |                                                                                                                                                                                                                                                                                                                                       |
|                    | -   Non-Prod: Weekly full, logs per SAP product standard, 14 days retention time                                                                                                                                                                                                                                                      |
| Service Management | Provide Health and System Monitoring with ability to monitor and correlate performance metrics and events and provide alerting across applications and infrastructure                                                                                                                                                                 |
|                    | Ability to diagnose issues and exceptions and identify error sources                                                                                                                                                                                                                                                                  |
|                    | Automate management processes to keep applications and infrastructure secure, up to date, and available                                                                                                                                                                                                                               |
| Other              | Migrate SAP workloads from existing data center to IBM PowerVS                                                                                                                                                                                                                                                                        |
|                    | Customer's SAP systems and applications run on NetWeaver (application) & HANA (DB), AnyDB or S/4 HANA                                                                                                                                                                                                                                 |
|                    | Provide an Image Replication migration solution that will minimize disruption during cut-over.                                                                                                                                                                                                                                        |
|                    | Cloud infrastructure for the proposed IAAS solution must be SAP Certified                                                                                                                                                                                                                                                             |
|                    | IBM Cloud IaaS will be deployed to support SAP and surrounding non-SAP workloads                                                                                                                                                                                                                                                      |
|                    | Customer does not want to adopt [RISE](https://www.ibm.com/consulting/rise-with-sap?utm_content=SRCWW&p1=Search&p4=43700077624079785&p5=e&gclid=EAIaIQobChMIr9bRlt7LgQMVJdHCBB0cewwcEAAYASAAEgIVgfD_BwE&gclsrc=aw.ds) at this time but wants to consider Cloud deployment solution that would facilitate a future RISE transformation |
{: caption="Table 1. Requirements" caption-side="bottom"}


## Components
{: #components}


| **Category**| **Solution Components** | **How it is used in solution**|
| ----------- |-------------------------|-------------------------------|
| Compute  | [[VPCVSIs]{.underline}](https://cloud.ibm.com/vpc-ext/provision/vs)| Edge VPC, IBM Storage Protect(BM) and NetWeaver and HANA DB|
|          |                         |                                 |
|          | Bare Metal ([IBM Storage Protect](https://www.ibm.com/support/pages/node/6498661))|
|          |                         |                                 |
|          | [Power VirtualServer](https://cloud.ibm.com/docs/sap?topic=sap-fast-path-site-map-power-vs)|
| Storage  | Flash storage from IBM  | NetWeaver and HANA DB servers   |
|          | FS9000 series devices   | primary storage production on   |
|          |                         | Tier 1. Non-Production on Tier  |
|          |                         | 3.                              |
|          | [[Cloud Object          | Backup and archive, application |
|          | Storage]{               | logs, operational logs and      |
|          | .underline}](https://cl | audit logs                      |
|          | oud.ibm.com/docs/cloud- |                                 |
|          | object-storage?topic=cl |                                 |
|          | oud-object-storage-abou |                                 |
|          | t-cloud-object-storage) |                                 |
|          |                         |                                 |
|          | [Block                  |                                 |
|          | storage](https          |                                 |
|          | ://cloud.ibm.com/docs/v |                                 |
|          | pc?topic=vpc-block-stor |                                 |
|          | age-about&interface=ui) |                                 |
| Ne       | [VPC Virtual Private    | Remote access to manage         |
| tworking | Network                 | resources in private network    |
|          | (VPN)                   |                                 |
|          | ](https://cloud.ibm.com |                                 |
|          | /docs/iaas-vpn?topic=ia |                                 |
|          | as-vpn-getting-started) |                                 |
|          | [[Virtual Private       | For private network access to   |
|          | Gateway & Virtual       | Cloud Services, e.g. Key        |
|          | Private Endpoint        | Protect, COS, etc.              |
|          | (V                      |                                 |
|          | PE)]{.underline}](https |                                 |
|          | ://cloud.ibm.com/docs/v |                                 |
|          | pc?topic=vpc-about-vpe) |                                 |
|          | [Cloud Internet         | Public Load balancing and DDoS  |
|          | Services                | of web servers traffic across   |
|          | (CIS)](https://clo      | zones in the region             |
|          | ud.ibm.com/docs/cis?top |                                 |
|          | ic=cis-getting-started) |                                 |
|          | [[DNS                   | Domain Naming System services   |
|          | S                       |                                 |
|          | ervices]{.underline}](h |                                 |
|          | ttps://cloud.ibm.com/do |                                 |
|          | cs/dns-svcs?topic=dns-s |                                 |
|          | vcs-about-dns-services) |                                 |
|          | [VPCs and               | Network Segmentation/Isolation  |
|          | subnets](htt            |                                 |
|          | ps://cloud.ibm.com/docs |                                 |
|          | /vpc?topic=vpc-about-su |                                 |
|          | bnets-vpc&interface=ui) |                                 |
|          | [Transit                | Connects across VPC, PowerVS    |
|          | Gateway](ht             | and Classic                     |
|          | tps://cloud.ibm.com/doc |                                 |
|          | s/transit-gateway?topic |                                 |
|          | =transit-gateway-about) |                                 |
|          | [IBM Cloud Application  | Load balancing workloads across |
|          | Load                    | multiple workload instances     |
|          | Bal                     | over the private network        |
|          | ancer](https://cloud.ib |                                 |
|          | m.com/docs/vpc?topic=vp |                                 |
|          | c-load-balancers-about) |                                 |
|          | (ALB)                   |                                 |
|          |                         |                                 |
|          | SAP Web Dispatcher      |                                 |
| Security | [[Block Storage         | Block Storage Encryption at     |
|          | encryption]{.underl     | rest                            |
|          | ine}](https://cloud.ibm |                                 |
|          | .com/docs/vpc?topic=vpc |                                 |
|          | -mng-data&interface=ui) |                                 |
|          | with provider keys      |                                 |
|          | Cloud Object Storage    | Cloud Object Storage Encryption |
|          | Encryption              | at rest                         |
|          | PowerVS Tier1 or Tier3  | Power VS uses IBM FlashSystem   |
|          | storage                 | Storage with AES-256 (Advanced  |
|          |                         | Encryption Standard)            |
|          |                         | hardware-based encryption       |
|          | HANA Data Volume        | HANA Database Encryption at     |
|          | Encryption (DVE)        | rest                            |
|          | [[IAM]{.unde            | IBM Cloud Identity & Access     |
|          | rline}](https://cloud.i | Management                      |
|          | bm.com/docs/account?top |                                 |
|          | ic=account-cloudaccess) |                                 |
|          | Privileged Identity and | BYO Bastion host (or Privileged |
|          | Access Management       | Access Gateway) with PAM SW     |
|          |                         | deployed in Edge VPC            |
|          | [[BYO Bastion Host on   | Remote access with Privileged   |
|          | VPC VSI with PAM        | Access Management               |
|          | SW]                     |                                 |
|          | {.underline}](https://c |                                 |
|          | loud.ibm.com/docs/frame |                                 |
|          | work-financial-services |                                 |
|          | ?topic=framework-financ |                                 |
|          | ial-services-vpc-archit |                                 |
|          | ecture-connectivity-bas |                                 |
|          | tion-tutorial-teleport) |                                 |
|          | [[Virtual Private       | Core Network Protection and     |
|          | Clouds (VPCs), Subnets, | isolation                       |
|          | Security Groups,        |                                 |
|          | ACLs]{.                 |                                 |
|          | underline}](https://clo |                                 |
|          | ud.ibm.com/docs/vpc?top |                                 |
|          | ic=vpc-getting-started) |                                 |
|          |                         |                                 |
|          | Isolated PowerVS LPARs  |                                 |
|          | [Cloud Internet         | DDoS protection and Web App     |
|          | Services                | Firewall                        |
|          | (CIS)](https://clo      |                                 |
|          | ud.ibm.com/docs/cis?top |                                 |
|          | ic=cis-getting-started) |                                 |
|          | -   [[Forti             | -   IPS/IDS protection at all   |
|          | gate]{.underline}](http |     ingress/egress              |
|          | s://cloud.ibm.com/catal |                                 |
|          | og/content/ibm-fortigat | -   Unified Threat Management   |
|          | e-AP-HA-terraform-deplo |     (UTM) Firewall              |
|          | y-5dd3e4ba-c94b-43ab-b4 |                                 |
|          | 16-c1c313479cec-global) |                                 |
|          |                         |                                 |
|          | -   [[Juniper           |                                 |
|          |     vSRX]{.underline}]  |                                 |
|          | (https://cloud.ibm.com/ |                                 |
|          | catalog/content/juniper |                                 |
|          | -vsrx-catalog-deploy-1. |                                 |
|          | 4-dc1e707c-33dd-4321-b2 |                                 |
|          | a5-c22dbf0dd0ee-global) |                                 |
|          |                         |                                 |
|          | -   [[Checkpoint Cloud  |                                 |
|          |                         |                                 |
|          |    Guard]{.underline}]( |                                 |
|          | https://cloud.ibm.com/c |                                 |
|          | atalog/content/checkpoi |                                 |
|          | nt-iaas-gw-ibm-vpc-1.0. |                                 |
|          | 7-9ed8dbde-2931-45f5-a7 |                                 |
|          | a7-0c90ce0d2686-global) |                                 |
|          |                         |                                 |
|          | -   [[Palo              |                                 |
|          |     Alto]{.u            |                                 |
|          | nderline}](https://clou |                                 |
|          | d.ibm.com/catalog/conte |                                 |
|          | nt/ibmcloud-vmseries-1. |                                 |
|          | 9-6470816d-562d-4627-86 |                                 |
|          | a5-fe3ad4e94b30-global) |                                 |
| Re       | HANA System Replication | Provide 99.95% availability for |
| siliency | (HSR)                   | HANA DB                         |
|          | [IBM                    | Backups and restores for images |
|          | Stor                    | and file systems.               |
|          | age](https://cloud.ibm. |                                 |
|          | com/media/docs/download |                                 |
|          | s/power-iaas/PowerVS_AI |                                 |
|          | X_Backup_Performance_Be |                                 |
|          | st_Practices_and_Guidel |                                 |
|          | ines_v1_0_03012022.pdf) |                                 |
|          | Protect                 |                                 |
|          |                         |                                 |
|          | [GRS](https:            |                                 |
|          | //cloud.ibm.com/docs/po |                                 |
|          | wer-iaas?topic=power-ia |                                 |
|          | as-getting-started-GRS) |                                 |
|          | DBACOCKPIT,             | SAP HANA backups                |
|          | HANACOCKPIT, backint    |                                 |
|          | Native database backup  | AnyDB backups                   |
|          | capabilities            |                                 |
| Service  | [IBM Cloud              | Apps and operational monitoring |
| Ma       | Monitoring](            |                                 |
| nagement | https://cloud.ibm.com/d |                                 |
| (Observ  | ocs/monitoring?topic=mo |                                 |
| ability) | nitoring-about-monitor) |                                 |
|          | [IBM Log                | Application and operational     |
|          | Analysis](https:        | logs                            |
|          | //cloud.ibm.com/docs/lo |                                 |
|          | g-analysis?topic=log-an |                                 |
|          | alysis-getting-started) |                                 |
{: caption="Table 2. Components" caption-side="bottom"}

As mentioned earlier, the [Architecture
Framework](https://cloud.ibm.com/docs/architecture-framework?topic=architecture-framework-intro)
is used to guide and determine the applicable aspects and domains for
which architecture decisions need to be made based on customer
requirements. The following sections contain the considerations, and
architecture decisions for the aspects and domains that are in scope for
this solution pattern.
