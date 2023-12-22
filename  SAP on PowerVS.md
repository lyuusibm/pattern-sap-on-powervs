# Overview

> The objective of this document is to provide an IBM Solution Design
> for the deployment of SAP on IBM Power Virtual Server (PowerVS) to:

-   Accelerate and simplify solution design by providing a standard IBM
    Cloud deployment architecture reference solution for SAP on IBM
    PowerVS enterprise-class deployments following the [IBM Architecture
    Framework](https://cloud.ibm.com/docs/architecture-framework?topic=architecture-framework-intro).

-   Provide a prescriptive, end-2-end enterprise-class solution design,
    with diagrams, component architecture decisions along with rationale
    for cloud component selection for a secure, resilient SAP on PowerVS
    deployment.

-   Ensure requirements can be met from performance, system availability
    and security prespectives .

> This is a [baseline]{.underline} solution pattern containing the
> design and architecture decisions for an SAP deployment on IBM Cloud
> Power Virtual Server to meet common requirements. Actual client
> solutions will depend on specific client needs. This document does not
> cover SAP configuration and SAP component deployment scenarios, it is
> limited to IBM cloud infrastructure options to support SAP workloads.
>
> It describes the deployment of SAP Business Applications Running on
> SAP NetWeaver, SAP HANA or SAP AnyDB and other SAP products using
> other technologies (SAP Content Server, or newer applications such as
> SAP Data Intelligence).

IBM Cloud SAP-Certified Infrastructure provides the flexibility to run
SAP workloads in the IBM Cloud and the ability quickly address issues
such as:

-   Moving SAP workloads to the cloud

-   Rapidly expanding or contracting capacity

-   Supplementing an existing private cloud architecture

Enterprise-class, mission critical workloads need to be resilient and
provide disaster recovery (DR) capabilities and high availability (HA).
This pattern illustrates a Single-Zone, Multi-Region design deployed to
IBM Power Virtual Server, which can provide both DR and HA to provide
99.95 availability for an SAP NetWeaver/HANA or SAP NetWeaver/AnyDB
solution to meet typical customer requirements.

The following documentation provides design considerations and
architecture decisions for deploying the IBM Cloud resources to support
SAP existing (migration) or new workloads, including:

-   SAP Business Applications such as SAP S/4HANA or SAP BW/4HANA

-   SAP Technical Applications such SAP HANA database server and SAP
    NetWeaver application server

# Reference architecture

## Architecture diagrams

Figure 1 provides a high-level summary of the Use Case, a SAP
Single-zone, multi-region deployment on IBM Cloud PowerVS. The Primary
Region supports Production workloads on PowerVS. The Secondary Region
supports non-Production and Disaster Recovery workloads should the
customer have DR requirements. The components deployed to the Edge VPC
provide security functions and resource isolation to the IBM Cloud
workloads.

.

![A diagram of a computer network Description automatically
generated](./media/image1.png){width="6.5in"
height="4.871527777777778in"}

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
generated](./media/image2.png){width="6.5in"
height="6.321527777777778in"}

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

## Design Scope

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
generated](./media/image3.png){width="6.5in"
height="3.182638888888889in"}

Figure

## Requirements

The following represents a [baseline]{.underline} set of requirements
which we believe are applicable to most clients and critical to a
successful SAP deployment.

  -----------------------------------------------------------------------
  **Networking Requirements**
  -----------------------------------------------------------------------
  Enterprise connectivity to customer data center(s) to provide access to
  applications from on-prem.

  Map and convert existing customer SAP Network functionality into IBM
  Cloud and PowerVS networking services

  Migrate/Redeploy customer IP addressing scheme within the IBM Cloud
  environment

  Provide network isolation with the ability to segregate applications
  based on attributes such as data classification, public vs internal
  apps and function 
  -----------------------------------------------------------------------

  -----------------------------------------------------------------------
  **Security Requirements**
  -----------------------------------------------------------------------
  Provide data encryption in transit and at rest

  Migrate customer IDS/IAM Services to target IBM Cloud environment

  Retain the same firewall rulesets across existing DCs

  Firewalls must be restrictively configured to provide advanced security
  features and prevent all traffic, both inbound and outbound, except
  that which is specifically required, documented, and approved, and
  include IPS/IDS services
  -----------------------------------------------------------------------

+-----------------------------------------------------------------------+
| **Resiliency Requirements**                                           |
+=======================================================================+
| Multi-site capability to support a disaster recovery strategy and     |
| solution leveraging IBM Cloud infrastructure DR capabilities          |
+-----------------------------------------------------------------------+
| Provide backups for data retention                                    |
+-----------------------------------------------------------------------+
| RTO/RPO = 4 hours/15 minutes; Rollback to original environments       |
| should occur no later than specified RTOs                             |
+-----------------------------------------------------------------------+
| 99.95 Availability                                                    |
+-----------------------------------------------------------------------+
| Backups                                                               |
|                                                                       |
| -   Prod: Daily Full, logs per SAP product standard, 30 days          |
|     retention time                                                    |
|                                                                       |
| -   Non-Prod: Weekly full, logs per SAP product standard, 14 days     |
|     retention time                                                    |
+-----------------------------------------------------------------------+

  -----------------------------------------------------------------------
  **Service Management Requirements**
  -----------------------------------------------------------------------
  Provide Health and System Monitoring with ability to monitor and
  correlate performance metrics and events and provide alerting across
  applications and infrastructure

  Ability to diagnose issues and exceptions and identify error sources

  Automate management processes to keep applications and infrastructure
  secure, up to date, and available
  -----------------------------------------------------------------------

  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  **Other Requirements**
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  Migrate SAP workloads from existing data center to IBM PowerVS

  Customer's SAP systems and applications run on NetWeaver (application) & HANA (DB), AnyDB or S/4 HANA

  Provide an Image Replication migration solution that will minimize disruption during cut-over.

  Cloud infrastructure for the proposed IAAS solution must be SAP Certified

  IBM Cloud IaaS will be deployed to support SAP and surrounding non-SAP workloads

  Customer does not want to adopt
  [RISE](https://www.ibm.com/consulting/rise-with-sap?utm_content=SRCWW&p1=Search&p4=43700077624079785&p5=e&gclid=EAIaIQobChMIr9bRlt7LgQMVJdHCBB0cewwcEAAYASAAEgIVgfD_BwE&gclsrc=aw.ds)
  at this time but wants to consider Cloud deployment solution that would facilitate a future RISE transformation
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Components

+----------+-------------------------+---------------------------------+
| **Ca     | **Solution Components** | **How it is used in solution**  |
| tegory** |                         |                                 |
+==========+=========================+=================================+
| Compute  | [[VPC                   | Edge VPC, IBM Storage           |
|          | VSIs]{.underline        | Protect(BM) and NetWeaver and   |
|          | }](https://cloud.ibm.co | HANA DB                         |
|          | m/vpc-ext/provision/vs) |                                 |
|          |                         |                                 |
|          | Bare Metal ([IBM        |                                 |
|          | Storage                 |                                 |
|          | Protect](ht             |                                 |
|          | tps://www.ibm.com/suppo |                                 |
|          | rt/pages/node/6498661)) |                                 |
|          |                         |                                 |
|          | [Power Virtual          |                                 |
|          | Server](                |                                 |
|          | https://cloud.ibm.com/d |                                 |
|          | ocs/sap?topic=sap-fast- |                                 |
|          | path-site-map-power-vs) |                                 |
+----------+-------------------------+---------------------------------+
| Storage  | Flash storage from IBM  | NetWeaver and HANA DB servers   |
|          | FS9000 series devices   | primary storage production on   |
|          |                         | Tier 1. Non-Production on Tier  |
|          |                         | 3.                              |
+----------+-------------------------+---------------------------------+
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
+----------+-------------------------+---------------------------------+
| Ne       | [VPC Virtual Private    | Remote access to manage         |
| tworking | Network                 | resources in private network    |
|          | (VPN)                   |                                 |
|          | ](https://cloud.ibm.com |                                 |
|          | /docs/iaas-vpn?topic=ia |                                 |
|          | as-vpn-getting-started) |                                 |
+----------+-------------------------+---------------------------------+
|          | [[Virtual Private       | For private network access to   |
|          | Gateway & Virtual       | Cloud Services, e.g. Key        |
|          | Private Endpoint        | Protect, COS, etc.              |
|          | (V                      |                                 |
|          | PE)]{.underline}](https |                                 |
|          | ://cloud.ibm.com/docs/v |                                 |
|          | pc?topic=vpc-about-vpe) |                                 |
+----------+-------------------------+---------------------------------+
|          | [Cloud Internet         | Public Load balancing and DDoS  |
|          | Services                | of web servers traffic across   |
|          | (CIS)](https://clo      | zones in the region             |
|          | ud.ibm.com/docs/cis?top |                                 |
|          | ic=cis-getting-started) |                                 |
+----------+-------------------------+---------------------------------+
|          | [[DNS                   | Domain Naming System services   |
|          | S                       |                                 |
|          | ervices]{.underline}](h |                                 |
|          | ttps://cloud.ibm.com/do |                                 |
|          | cs/dns-svcs?topic=dns-s |                                 |
|          | vcs-about-dns-services) |                                 |
+----------+-------------------------+---------------------------------+
|          | [VPCs and               | Network Segmentation/Isolation  |
|          | subnets](htt            |                                 |
|          | ps://cloud.ibm.com/docs |                                 |
|          | /vpc?topic=vpc-about-su |                                 |
|          | bnets-vpc&interface=ui) |                                 |
+----------+-------------------------+---------------------------------+
|          | [Transit                | Connects across VPC, PowerVS    |
|          | Gateway](ht             | and Classic                     |
|          | tps://cloud.ibm.com/doc |                                 |
|          | s/transit-gateway?topic |                                 |
|          | =transit-gateway-about) |                                 |
+----------+-------------------------+---------------------------------+
|          | [IBM Cloud Application  | Load balancing workloads across |
|          | Load                    | multiple workload instances     |
|          | Bal                     | over the private network        |
|          | ancer](https://cloud.ib |                                 |
|          | m.com/docs/vpc?topic=vp |                                 |
|          | c-load-balancers-about) |                                 |
|          | (ALB)                   |                                 |
|          |                         |                                 |
|          | SAP Web Dispatcher      |                                 |
+----------+-------------------------+---------------------------------+
| Security | [[Block Storage         | Block Storage Encryption at     |
|          | encryption]{.underl     | rest                            |
|          | ine}](https://cloud.ibm |                                 |
|          | .com/docs/vpc?topic=vpc |                                 |
|          | -mng-data&interface=ui) |                                 |
|          | with provider keys      |                                 |
+----------+-------------------------+---------------------------------+
|          | Cloud Object Storage    | Cloud Object Storage Encryption |
|          | Encryption              | at rest                         |
+----------+-------------------------+---------------------------------+
|          | PowerVS Tier1 or Tier3  | Power VS uses IBM FlashSystem   |
|          | storage                 | Storage with AES-256 (Advanced  |
|          |                         | Encryption Standard)            |
|          |                         | hardware-based encryption       |
+----------+-------------------------+---------------------------------+
|          | HANA Data Volume        | HANA Database Encryption at     |
|          | Encryption (DVE)        | rest                            |
+----------+-------------------------+---------------------------------+
|          | [[IAM]{.unde            | IBM Cloud Identity & Access     |
|          | rline}](https://cloud.i | Management                      |
|          | bm.com/docs/account?top |                                 |
|          | ic=account-cloudaccess) |                                 |
+----------+-------------------------+---------------------------------+
|          | Privileged Identity and | BYO Bastion host (or Privileged |
|          | Access Management       | Access Gateway) with PAM SW     |
|          |                         | deployed in Edge VPC            |
+----------+-------------------------+---------------------------------+
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
+----------+-------------------------+---------------------------------+
|          | [[Virtual Private       | Core Network Protection and     |
|          | Clouds (VPCs), Subnets, | isolation                       |
|          | Security Groups,        |                                 |
|          | ACLs]{.                 |                                 |
|          | underline}](https://clo |                                 |
|          | ud.ibm.com/docs/vpc?top |                                 |
|          | ic=vpc-getting-started) |                                 |
|          |                         |                                 |
|          | Isolated PowerVS LPARs  |                                 |
+----------+-------------------------+---------------------------------+
|          | [Cloud Internet         | DDoS protection and Web App     |
|          | Services                | Firewall                        |
|          | (CIS)](https://clo      |                                 |
|          | ud.ibm.com/docs/cis?top |                                 |
|          | ic=cis-getting-started) |                                 |
+----------+-------------------------+---------------------------------+
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
+----------+-------------------------+---------------------------------+
| Re       | HANA System Replication | Provide 99.95% availability for |
| siliency | (HSR)                   | HANA DB                         |
+----------+-------------------------+---------------------------------+
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
+----------+-------------------------+---------------------------------+
|          | DBACOCKPIT,             | SAP HANA backups                |
|          | HANACOCKPIT, backint    |                                 |
+----------+-------------------------+---------------------------------+
|          | Native database backup  | AnyDB backups                   |
|          | capabilities            |                                 |
+----------+-------------------------+---------------------------------+
| Service  | [IBM Cloud              | Apps and operational monitoring |
| Ma       | Monitoring](            |                                 |
| nagement | https://cloud.ibm.com/d |                                 |
| (Observ  | ocs/monitoring?topic=mo |                                 |
| ability) | nitoring-about-monitor) |                                 |
+----------+-------------------------+---------------------------------+
|          | [IBM Log                | Application and operational     |
|          | Analysis](https:        | logs                            |
|          | //cloud.ibm.com/docs/lo |                                 |
|          | g-analysis?topic=log-an |                                 |
|          | alysis-getting-started) |                                 |
+----------+-------------------------+---------------------------------+

As mentioned earlier, the [Architecture
Framework](https://cloud.ibm.com/docs/architecture-framework?topic=architecture-framework-intro)
is used to guide and determine the applicable aspects and domains for
which architecture decisions need to be made based on customer
requirements. The following sections contain the considerations, and
architecture decisions for the aspects and domains that are in scope for
this solution pattern.

## 

## Compute design

Power Virtual Servers are available with flexible hardware
configurations on both Power S922 and E980. It is permitted to define a
custom size of the IBM Power Virtual Server to use for SAP NetWeaver, in
accordance with existing SAP NetWeaver or SAP AnyDB for IBM Power
Systems best practices and guidance from SAP.

The Flexibility of IBM Power Systems Virtual Servers hardware capability
includes:

-   Cores (CPU)

-   Memory (RAM)

-   Data volume size

-   Data volume type / performance tier

-   Network interfaces

-   PowerVM Host Pinning Policy (soft or hard)

-   PowerVM Host CPU Binding (dedicated or shared)

PowerVS custom profiles are for nonproduct development for testing or
development use only. Custom, non-SAP Certified profiles aren\'t
intended for production deployments and aren\'t supported or certified
for SAP production. Custom profiles should not be used to go from a
nonproduction environment to a production environment.

There are multiple SAP Certified Power Virtual Sever profiles certified
by SAP:

> NetWeaver PowerVS profiles:
>
> [IBM Power Virtual Server certified profiles for SAP
> NetWeaver](https://cloud.ibm.com/docs/sap?topic=sap-nw-iaas-offerings-profiles-power-vs)
>
> HANA PowerVS profiles:
>
> [IBM Power Virtual Server certified profiles for SAP
> HANA](https://cloud.ibm.com/docs/sap?topic=sap-hana-iaas-offerings-profiles-power-vs)

For sizing information see [Sizing process for SAP
Systems](https://cloud.ibm.com/docs/sap?topic=sap-sizing&interface=ui)

For a distributed SAP installation, it is best to have all nodes in the
same location (Availability Zone or Datacenter). Deviation from this
setup can cause latency and timeouts, which render your SAP system
unresponsive. Due to this, SAP does not support the application layer
and database to be deployed across different zones or COLOs, therefore
the application and database layers must exist in the same availability
zone or COLO.

Databases typically experience growth over time so database size and
expected data growth rates should be taken into consideration when
planning database deployments.

SAP AnyDB

AnyDB refers to any non-HANA, SAP supported database. SAP supports: IBM
DB2, Oracle, Microsoft SQL Server, SAP Max DB, SAP ASE and SAP IQ.

Deploying SAP AnyDB on IBM PowerVS is similar to deployments with
infrastructure with on-premises data centers. You can use the
information that is provided from SAP and the RDBMS providers. The
following links contain design considerations for each database:

[IBM DB2](https://cloud.ibm.com/docs/sap?topic=sap-anydb-ibm-db2)

[SAP Max DB](https://cloud.ibm.com/docs/sap?topic=sap-anydb-sap-maxdb)

[SAP ASE](https://cloud.ibm.com/docs/sap?topic=sap-anydb-sap-ase)

[SAP IQ](https://cloud.ibm.com/docs/sap?topic=sap-anydb-sap-iq)

Oracle RAC for high availability and Oracle Dataguard for disaster
recovery can be deployed to PowerVS as an AnyDB option. Best practices
and guidance for both can be found at the following links:

[Oracle RAC on
PowerVS](https://cloud.ibm.com/docs/pattern-oracle-rac-on-powervs?topic=pattern-oracle-rac-on-powervs-overview)

[Oracle Disaster Recovery using Data
Guard](https://cloud.ibm.com/docs/pattern-oracle-disaster-recovery-on-powervs?topic=pattern-oracle-disaster-recovery-on-powervs-Oracle-dr-ibm-pvs#oracle-data-guard)

## Storage design

SAP solutions will want to ensure there is enough available storage to
accommodate the existing environment and allow for additional data
growth for primary, backup and archive storage. You will need to choose
the appropriate SAP Certified profile to meet primary storage and growth
requirements for workloads.

The storage tiers in Power Systems Virtual Server are based on I/O
operations per second (IOPS). It means that the performance of your
storage volumes is limited to the maximum number of IOPS based on volume
size and storage tier.

For each Power Systems Virtual Server instance, you must select a Flash
storage from IBM FS9000 series devices [storage
tiers](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-about-virtual-server#storage-tiers):
Tier 1 is currently set to 10 IOPS/GB or Tier 3 is currently set to 3
IOPS/GB.

When planning an SAP deployment, IOPS and its effect on latency needs to
also be considered. 100 GB PowerVS Tier 3 storage volume can receive up
to 300 IOPs, and a 100 GB Tier 1 storage volume can receive up to 1000
IOPS. After the IOPS limit is reached for the storage volume, the I/O
latency increases.

Storage tiers differ and Storage tiers cannot be mixed.

For SAP HANA, only Tier 1 storage is supported and is required.

For SAP NetWeaver, both Tier 1 and Tier 3 are supported.

For SAP NetWeaver and SAP AnyDB databases (such as IBM Db2 or Oracle
DB), Tier 1 (NVMe) is recommended but Tier 3 (SSD) can be used.
Typically, Tier 3 storage tier is not suitable for production workloads.
When choosing a storage tier, ensure that you consider not just the
average I/O load, but more importantly the peak IOPS of your storage
workload.

Client is responsible for backing up data on for PowerVS FlashSystem
FS9000 storage

[Other Storage
Considerations](https://cloud.ibm.com/docs/sap?topic=sap-storage-design-considerations)

## Network design

Networking is a key aspect of any cloud deployment that should not be
overlooked. The network design should consider enterprise connectivity,
latency, throughput, resiliency, and security requirements.

For security purposes, it is recommended to isolate production from
non-production workloads as a best practice. In addition, it's also a
best practice to isolate by application tiers (e.g. application vs.
database). PowerVS enables the creation of logical isolation across
separate LPARs.

To provide isolation and centralize advanced security functions, the
network design follows "Hub and spoke" VPC model. The Edge VPC serves as
the hub for which all ingress and egress traffic flows. The Edge is a
virtual network (VPC) that acts as a central point of connectivity to
on-premises network and all other VPC and PowerVS environments. PowerVS
is connected to the Edge ("Hub") via a Transit Gateway which allows
traffic routing between each of the PowerVS, VPC and classic
environments in the IBM Cloud account.

Enterprise connectivity is needed to connect the SAP environment to the
client data center(s). IBM Cloud Direct Link is typically used for this
connectivity and if redundancy is needed, two direct links can be used
in a resilient design.

A separate Management VPC (not illustrated) can be deployed into
multiple regions if needed, to host management infrastructure and
services also connected to the Edge and PowerVS through Transit Gateway.

# For a list of all Networking Considerations, see [networking design considerations](https://cloud.ibm.com/docs/sap?topic=sap-networking-design-considerations).

#  

## Security design

Security should be applied at all layers of the solution for defense in
depth depending on requirements, for example at the network edge, VPCs,
and at every compute instance as well as at the application and database
layers. In addition, data should be protected both in transit and at
rest according to data classification and controls should be in place to
eliminate the need for direct access to the environment(s), and when
direct access is needed, all access should be logged and monitored.

It is a best practice to isolate, secure, manage and monitor all ingress
and egress traffic to the environment and to centralize these functions
in a hub and spoke model through an Edge VPC. As a result, it's
recommended that security and isolation is established using a
combination of Security Groups (SG), Network Access Control Lists (NACL)
and routing/FW rules defined in the Edge VPC. This is in addition to the
cloud account level protection provided in IBM Cloud Identity Services.

Implementation of firewall(s) in the Transit/Edge can secure and route
traffic to the IBM Cloud environment as well as from the Customer
network.

Enable logging to facilitate the firewall activity analysis if needed to
meet client security IPS/IDS requirements.

All traffic, both public and private, should route through the
Edge/Transit VPC for routing, isolation, and logging.

To restrict, log and monitor administrative access to the environment, a
bastion (jump) host is recommended in the Edge VPC for all
administrative access. In addition, all Bastion access and activity
should be logged and monitored through Privileged Access Management
(PAM) services.

Security is a shared responsibility between cloud provider and customer
at both the infrastructure and application layers.

## Resiliency design

Resiliency is the ability of a workload to meet a specific target
Service Level Objective (SLO), Service Level Availability (SLA) or
recover from a service disruption and still meet the required SLA.
Resiliency needs to be considered at both the infrastructure and
application layers across the entire solution.

A resilient design may include Disaster Recovery (DR) and High
Availability (HA) depending on SLA requirements and they are two
different things.

Disaster Recovery is the infrastructure, application layers and
accompanying set of policies and procedures to enable the recovery or
continuation of vital technology infrastructure and applications
following a natural or human-induced disaster.

High Availability is the resource availability in a solution throughout
the stack, built in by design, to withstand individual component
failures in the system.

Both DR and HA can be enabled using several technologies and approaches
which depends on the Recovery Point Objective (RPO), Recovery Time
Objective (RTO) and SLA requirements.

The table below shows a comparison of the different deployment options.

+-----------------+-------+---------------------+---------------------+
| Deployment      | Avail | Features            | Recommended Usage   |
|                 | abili |                     |                     |
|                 | ty^1^ |                     |                     |
+=================+=======+=====================+=====================+
| Single Zone     | 99.9% | \- Single instance  | \- Low to medium    |
|                 |       | (single point of    | priority            |
|                 |       | failure) or         | applications        |
|                 |       | multiple instances  |                     |
|                 |       | (protects from      | \- Non-production   |
|                 |       | infrastructure      | environment         |
|                 |       | failures)           |                     |
|                 |       |                     |                     |
|                 |       | \- Low/Medium cost  |                     |
+-----------------+-------+---------------------+---------------------+
| Single Zone;    | 9     | \- Multi-instance   | \- Core business    |
| Multi Instance  | 9.95% | (protects from      | applications        |
|                 |       | infrastructure      |                     |
|                 |       | failures)           | \- Production level |
|                 |       |                     | environments with   |
|                 |       |                     | resiliency          |
|                 |       |                     | requirements not    |
|                 |       |                     | exceeding 99.95%    |
+-----------------+-------+---------------------+---------------------+
| Multi-zone,     | 99.9  | \- Redundant        | \- Core business    |
| Single Region   | 9%^2^ | resources           | applications        |
|                 |       |                     |                     |
|                 |       | \- Protection from  | \- Production level |
|                 |       | zone outages        | environments with   |
|                 |       |                     | stringent           |
|                 |       | \- Medium/high cost | resiliency          |
|                 |       |                     | requirements        |
+-----------------+-------+---------------------+---------------------+
| Multi-zone,     | 9     | \- Protection from  | \- Disaster         |
| Multi-Region    | 9.99% | region outages      | Recovery            |
|                 |       |                     |                     |
|                 |       | \- High cost        | \- Business         |
|                 |       |                     | continuity policies |
|                 |       |                     | with cross geo or   |
|                 |       |                     | cross-country       |
|                 |       |                     | requirements        |
+-----------------+-------+---------------------+---------------------+

1.  Based on Cloud infrastructure
    [SLA.](https://www.ibm.com/support/customer/csol/terms/?id=i126-9268&lc=en#detail-document)
    Does not represent application availability

2.  3 or more instances in separate Availability Zones

As mentioned in the Compute section, SAP does not support the
application layer and database to be deployed across different zones;
the application and database layers must exist in the same availability
zone or COLO. Therefore, the highest availability that can be supported
is 99.95%

# For more information on Resiliency on IBM Cloud, see [High Availability and Resiliency on IBM Cloud](https://cloud.ibm.com/docs/ha-infrastructure?topic=ha-infrastructure-landing-about-ha-dr-backup).

Depending on requirements and design, an SAP deployment on IBM cloud can
support 99.95% availability. Unfortunately, SAP does not certify
solutions that span multiple zones, so resiliency is deployed to a
single zone with multiple server instances in the same zone.

The IBM Cloud application load balancer along with SAP Web Dispatcher
spreads client requests or loads across 2 or more server instances for
improved resiliency and improved performance. In addition, IBM Cloud
load balancers do health checks to ensure that only \"healthy\" virtual
server instances receive client requests.

IBM Power Systems Virtual Servers are designed by hardened configuration
within single data center to provide 99.95% SLA. For more details, refer
to [Service Level Agreements for IBM
Cloud](https://www.ibm.com/support/customer/csol/terms/?id=i126-9268&lc=en#detail-document).

To increase availability for SAP systems on IBM Power Systems Virtual
servers, High Availability instances can be deployed within the same
data center with "Different Server" [placement
group](https://cloud.ibm.com/docs/power-iaas?topic=power-iaas-placement-groups),
and Disaster Recovery systems can be deployed in a different region.

To maximize investment on DR infrastructure so DR infrastructure is not
idle, it's recommended to have cross-region passive DR deployed on
reduced-size VMs. To reserve the capacity for DR systems, [Shared
Processor
Pool](https://www.ibm.com/docs/en/power9?topic=systems-managing-shared-processor-pools)
can be setup with a small fee.

Alternative cost optimized DR systems may be configured so that DR
systems share the same infrastructure with Non-Production systems, if
segmentation/compliance/licensing/Non-Production system availability and
other client requirements allow.

The IBM Cloud environment does not support any preconfigured
high-availability (HA) scenarios for SAP. However, HA scenarios can be
configured based on the HA extension for the operating system. HA
extensions are created by adding the required hardware and the required
software components to SAP landscapes.

[Here](https://cloud.ibm.com/docs/sap?topic=sap-refarch-hana-scaleout#network-layout-for-scale-out-configurations-2)
is an overview of a single-zone resilient SAP Scale-out deployment.

A combination of SAP Native Database Backup tools is used to deliver the
resiliency services such as:

-   DBACOCKPIT

-   HANACOCKPIT

-   Backint for SAP Database backups

To support database recovery for points in time going back at least one
month, a daily database backup is required, along with redo log backups
every 15 minutes.

-   For long-term recovery of data, an additional monthly backup is
    suggested with a one-year data retention or as determined by the
    customer.

-   The daily log backup frequency for production DB should be less than
    or equal to the customer desired RPO parameter for their Business
    Continuity Plan

-   Production System Monthly full retained for 60 days. Daily Full
    retained for 1 week. Daily incremental retained for 60 days. Weekly
    Full retained for 30 days.

-   Non-Productions System Monthly full retained for 60 days. Weekly
    Full retained for 14 days week. Daily incremental retained for 60
    days.

Consider the largest DB size for full backups with room for addition
growth. Recommend complementing the backup solution with Daily snapshots
for production.

## Migration design

There are several migration scenarios, the most straight-forward being a
direct "lift-and-shift" (homogeneous) where the Infrastructure and SAP
versions remain the same, for example on-prem SAP NetWeaver ABAP 7.0
running on VMWare to the same environment on cloud. Migration from
on-prem to another infrastructure platform or SAP version
(heterogeneous) can become more complex, especially when combined with a
DB conversion or application server upgrade.

# For a list of migration options and tools, see [Moving SAP Workloads](https://cloud.ibm.com/docs/sap?topic=sap-faq-moving-sap-workloads#faq-moving-sap-workloads-overview).

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
+============+=================+===================+==================+
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
+------------+-----------------+-------------------+------------------+
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
+------------+-----------------+-------------------+------------------+
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
+------------+-----------------+-------------------+------------------+
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
+------------+-----------------+-------------------+------------------+

**\
**

**Heterogeneous Migration:**

+---------------+----------------+-------------------+----------------+
| **Migration   | **Technique    | **Advantages and  | **Associated   |
| Method**      | Summary**      | Concerns**        | Tools**        |
+===============+================+===================+================+
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
+---------------+----------------+-------------------+----------------+
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
+---------------+----------------+-------------------+----------------+
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
+---------------+----------------+-------------------+----------------+
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
+---------------+----------------+-------------------+----------------+
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
+---------------+----------------+-------------------+----------------+

# 

# Architecture decisions

## Architecture decisions for compute

+----------+-------------+---------------+---------------------------+
| **Arch   | **Re        | **Decision**  | **Rationale**             |
| itecture | quirement** |               |                           |
| de       |             |               |                           |
| cision** |             |               |                           |
+==========+=============+===============+===========================+
| Compute  | Target      | Power Virtual | -   Hardware flexibility  |
| (Ne      | environment | Server        |     limited to            |
| tWeaver) | to match    | LPAR(s)       |     non-production        |
|          | specific    |               |     environments          |
|          | workload    |               |                           |
|          | r           |               | -   For production use    |
|          | equirements |               |     the SAP-certified     |
|          | and be SAP  |               |     profiles              |
|          | certified   |               |                           |
+----------+-------------+---------------+---------------------------+
| Compute  | Target      | Power Virtual | -   Hardware flexibility  |
|          | environment | Server        |     limited to            |
| (HANA)   | to match    | LPAR(s)       |     non-production        |
|          | workload    |               |     environments          |
|          | r           |               |                           |
|          | equirements |               | -   For production use    |
|          | and be SAP  |               |     the SAP-certified     |
|          | certified   |               |     profiles              |
+----------+-------------+---------------+---------------------------+
| Backup   | Backup      | Bare Metal on | -   Bare Metal on Classic |
| server   | server      | Classic       |     can accommodate large |
| (        | sized for   |               |     storage requirements  |
| Spectrum | enough      |               |     for backups           |
| Storage  | available   |               |                           |
| Protect) | storage     |               |                           |
+----------+-------------+---------------+---------------------------+
| Virtual  | Virtual     | VPC Virtual   |                           |
| Servers  | servers for | Servers       |                           |
| (Edge    | workloads   |               |                           |
| VPC)     | in the Edge |               |                           |
|          | VPC         |               |                           |
+----------+-------------+---------------+---------------------------+

## Architecture decisions for storage

+---------+-------------+-----------------------+---+--------------------+
| **Archi | **Re        | **Decision**          |   | **Rationale**      |
| tecture | quirement** |                       |   |                    |
| dec     |             |                       |   |                    |
| ision** |             |                       |   |                    |
+=========+=============+=======================+===+====================+
| Primary | Tier 1 - 10 | Flash storage from    | T |                    |
| St      | IOPS        | IBM FS9000 series     | i |                    |
| orage - |             | devices               | e |                    |
| Pro     |             |                       | r |                    |
| duction |             |                       | 1 |                    |
|         |             |                       | H |                    |
|         |             |                       | A |                    |
|         |             |                       | N |                    |
|         |             |                       | A |                    |
|         |             |                       | D |                    |
|         |             |                       | B |                    |
|         |             |                       | o |                    |
|         |             |                       | n |                    |
|         |             |                       | P |                    |
|         |             |                       | r |                    |
|         |             |                       | o |                    |
|         |             |                       | d |                    |
|         |             |                       | u |                    |
|         |             |                       | c |                    |
|         |             |                       | t |                    |
|         |             |                       | i |                    |
|         |             |                       | o |                    |
|         |             |                       | n |                    |
|         |             |                       |   |                    |
|         |             |                       | R |                    |
|         |             |                       | e |                    |
|         |             |                       | c |                    |
|         |             |                       | o |                    |
|         |             |                       | m |                    |
|         |             |                       | m |                    |
|         |             |                       | e |                    |
|         |             |                       | n |                    |
|         |             |                       | d |                    |
|         |             |                       | e |                    |
|         |             |                       | d |                    |
|         |             |                       | f |                    |
|         |             |                       | o |                    |
|         |             |                       | r |                    |
|         |             |                       | l |                    |
|         |             |                       | a |                    |
|         |             |                       | r |                    |
|         |             |                       | g |                    |
|         |             |                       | e |                    |
|         |             |                       | n |                    |
|         |             |                       | o |                    |
|         |             |                       | n |                    |
|         |             |                       | - |                    |
|         |             |                       | H |                    |
|         |             |                       | A |                    |
|         |             |                       | N |                    |
|         |             |                       | A |                    |
|         |             |                       | D |                    |
|         |             |                       | B |                    |
|         |             |                       |   |                    |
|         |             |                       | M |                    |
|         |             |                       | a |                    |
|         |             |                       | x |                    |
|         |             |                       | i |                    |
|         |             |                       | m |                    |
|         |             |                       | i |                    |
|         |             |                       | z |                    |
|         |             |                       | e |                    |
|         |             |                       | p |                    |
|         |             |                       | e |                    |
|         |             |                       | r |                    |
|         |             |                       | f |                    |
|         |             |                       | o |                    |
|         |             |                       | r |                    |
|         |             |                       | m |                    |
|         |             |                       | a |                    |
|         |             |                       | n |                    |
|         |             |                       | c |                    |
|         |             |                       | e |                    |
|         |             |                       | a |                    |
|         |             |                       | n |                    |
|         |             |                       | d |                    |
|         |             |                       | o |                    |
|         |             |                       | p |                    |
|         |             |                       | t |                    |
|         |             |                       | i |                    |
|         |             |                       | m |                    |
|         |             |                       | i |                    |
|         |             |                       | z |                    |
|         |             |                       | e |                    |
|         |             |                       | c |                    |
|         |             |                       | o |                    |
|         |             |                       | s |                    |
|         |             |                       | t |                    |
|         |             |                       | s |                    |
|         |             |                       | f |                    |
|         |             |                       | o |                    |
|         |             |                       | r |                    |
|         |             |                       | p |                    |
|         |             |                       | e |                    |
|         |             |                       | a |                    |
|         |             |                       | k |                    |
|         |             |                       | I |                    |
|         |             |                       | O |                    |
|         |             |                       | P |                    |
|         |             |                       | S |                    |
|         |             |                       | w |                    |
|         |             |                       | o |                    |
|         |             |                       | r |                    |
|         |             |                       | k |                    |
|         |             |                       | l |                    |
|         |             |                       | o |                    |
|         |             |                       | a |                    |
|         |             |                       | d |                    |
|         |             |                       | . |                    |
|         |             |                       |   |                    |
|         |             |                       | U |                    |
|         |             |                       | s |                    |
|         |             |                       | i |                    |
|         |             |                       | n |                    |
|         |             |                       | g |                    |
|         |             |                       | o |                    |
|         |             |                       | f |                    |
|         |             |                       | a |                    |
|         |             |                       | m |                    |
|         |             |                       | i |                    |
|         |             |                       | x |                    |
|         |             |                       | o |                    |
|         |             |                       | f |                    |
|         |             |                       | T |                    |
|         |             |                       | i |                    |
|         |             |                       | e |                    |
|         |             |                       | r |                    |
|         |             |                       | s |                    |
|         |             |                       | i |                    |
|         |             |                       | s |                    |
|         |             |                       | n |                    |
|         |             |                       | o |                    |
|         |             |                       | t |                    |
|         |             |                       | s |                    |
|         |             |                       | u |                    |
|         |             |                       | p |                    |
|         |             |                       | p |                    |
|         |             |                       | o |                    |
|         |             |                       | r |                    |
|         |             |                       | t |                    |
|         |             |                       | e |                    |
|         |             |                       | d |                    |
|         |             |                       | . |                    |
+---------+-------------+-----------------------+---+--------------------+
| Primary | Tier 3 - 3  | Flash storage from    | R |                    |
| Storage | IOPS        | IBM FS9000 series     | e |                    |
| --      |             | devices               | c |                    |
| Non-Pro |             |                       | o |                    |
| duction |             |                       | m |                    |
|         |             |                       | m |                    |
|         |             |                       | e |                    |
|         |             |                       | n |                    |
|         |             |                       | d |                    |
|         |             |                       | e |                    |
|         |             |                       | d |                    |
|         |             |                       | f |                    |
|         |             |                       | o |                    |
|         |             |                       | r |                    |
|         |             |                       | a |                    |
|         |             |                       | l |                    |
|         |             |                       | l |                    |
|         |             |                       | n |                    |
|         |             |                       | o |                    |
|         |             |                       | n |                    |
|         |             |                       | - |                    |
|         |             |                       | D |                    |
|         |             |                       | B |                    |
|         |             |                       | t |                    |
|         |             |                       | i |                    |
|         |             |                       | e |                    |
|         |             |                       | r |                    |
|         |             |                       | s |                    |
|         |             |                       | ( |                    |
|         |             |                       | A |                    |
|         |             |                       | p |                    |
|         |             |                       | p |                    |
|         |             |                       | . |                    |
|         |             |                       | S |                    |
|         |             |                       | e |                    |
|         |             |                       | r |                    |
|         |             |                       | v |                    |
|         |             |                       | e |                    |
|         |             |                       | r |                    |
|         |             |                       | s |                    |
|         |             |                       | ) |                    |
|         |             |                       | i |                    |
|         |             |                       | n |                    |
|         |             |                       | c |                    |
|         |             |                       | l |                    |
|         |             |                       | u |                    |
|         |             |                       | d |                    |
|         |             |                       | i |                    |
|         |             |                       | n |                    |
|         |             |                       | g |                    |
|         |             |                       | n |                    |
|         |             |                       | o |                    |
|         |             |                       | n |                    |
|         |             |                       | - |                    |
|         |             |                       | c |                    |
|         |             |                       | r |                    |
|         |             |                       | i |                    |
|         |             |                       | t |                    |
|         |             |                       | i |                    |
|         |             |                       | c |                    |
|         |             |                       | a |                    |
|         |             |                       | l |                    |
|         |             |                       | D |                    |
|         |             |                       | B |                    |
|         |             |                       | L |                    |
|         |             |                       | P |                    |
|         |             |                       | A |                    |
|         |             |                       | R |                    |
|         |             |                       | s |                    |
+---------+-------------+-----------------------+---+--------------------+
| Backup  | Backup      | Bare Metal with local | - |                    |
| Storage | storage for | disk                  |   |                    |
|         | servers     |                       |   |                    |
|         |             | Block storage         |   |                    |
|         |             |                       | B |                    |
|         |             | Cloud Object Storage  | a |                    |
|         |             |                       | r |                    |
|         |             |                       | e |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | M |                    |
|         |             |                       | e |                    |
|         |             |                       | t |                    |
|         |             |                       | a |                    |
|         |             |                       | l |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | o |                    |
|         |             |                       | n |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | c |                    |
|         |             |                       | l |                    |
|         |             |                       | a |                    |
|         |             |                       | s |                    |
|         |             |                       | s |                    |
|         |             |                       | i |                    |
|         |             |                       | c |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | o |                    |
|         |             |                       | f |                    |
|         |             |                       | f |                    |
|         |             |                       | e |                    |
|         |             |                       | r |                    |
|         |             |                       | s |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | p |                    |
|         |             |                       | r |                    |
|         |             |                       | o |                    |
|         |             |                       | f |                    |
|         |             |                       | i |                    |
|         |             |                       | l |                    |
|         |             |                       | e |                    |
|         |             |                       | s |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | t |                    |
|         |             |                       | o |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | a |                    |
|         |             |                       | c |                    |
|         |             |                       | c |                    |
|         |             |                       | o |                    |
|         |             |                       | m |                    |
|         |             |                       | m |                    |
|         |             |                       | o |                    |
|         |             |                       | d |                    |
|         |             |                       | a |                    |
|         |             |                       | t |                    |
|         |             |                       | e |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | l |                    |
|         |             |                       | a |                    |
|         |             |                       | r |                    |
|         |             |                       | g |                    |
|         |             |                       | e |                    |
|         |             |                       | r |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | s |                    |
|         |             |                       | t |                    |
|         |             |                       | o |                    |
|         |             |                       | r |                    |
|         |             |                       | a |                    |
|         |             |                       | g |                    |
|         |             |                       | e |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | s |                    |
|         |             |                       | i |                    |
|         |             |                       | z |                    |
|         |             |                       | e |                    |
|         |             |                       | s |                    |
|         |             |                       | . |                    |
|         |             |                       |   |                    |
|         |             |                       | - |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | C |                    |
|         |             |                       | l |                    |
|         |             |                       | o |                    |
|         |             |                       | u |                    |
|         |             |                       | d |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | O |                    |
|         |             |                       | b |                    |
|         |             |                       | j |                    |
|         |             |                       | e |                    |
|         |             |                       | c |                    |
|         |             |                       | t |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | S |                    |
|         |             |                       | t |                    |
|         |             |                       | o |                    |
|         |             |                       | r |                    |
|         |             |                       | a |                    |
|         |             |                       | g |                    |
|         |             |                       | e |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | f |                    |
|         |             |                       | o |                    |
|         |             |                       | r |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | o |                    |
|         |             |                       | f |                    |
|         |             |                       | f |                    |
|         |             |                       | s |                    |
|         |             |                       | i |                    |
|         |             |                       | t |                    |
|         |             |                       | e |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | 2 |                    |
|         |             |                       | ^ |                    |
|         |             |                       | n |                    |
|         |             |                       | d |                    |
|         |             |                       | ^ |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | c |                    |
|         |             |                       | o |                    |
|         |             |                       | p |                    |
|         |             |                       | y |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | f |                    |
|         |             |                       | o |                    |
|         |             |                       | r |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | l |                    |
|         |             |                       | o |                    |
|         |             |                       | w |                    |
|         |             |                       | e |                    |
|         |             |                       | r |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | c |                    |
|         |             |                       | o |                    |
|         |             |                       | s |                    |
|         |             |                       | t |                    |
|         |             |                       | s |                    |
|         |             |                       |   |                    |
|         |             |                       | - |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | C |                    |
|         |             |                       | o |                    |
|         |             |                       | m |                    |
|         |             |                       | b |                    |
|         |             |                       | i |                    |
|         |             |                       | n |                    |
|         |             |                       | e |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | B |                    |
|         |             |                       | l |                    |
|         |             |                       | o |                    |
|         |             |                       | c |                    |
|         |             |                       | k |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | a |                    |
|         |             |                       | n |                    |
|         |             |                       | d |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | C |                    |
|         |             |                       | O |                    |
|         |             |                       | S |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | f |                    |
|         |             |                       | o |                    |
|         |             |                       | r |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | l |                    |
|         |             |                       | o |                    |
|         |             |                       | n |                    |
|         |             |                       | g |                    |
|         |             |                       | - |                    |
|         |             |                       | t |                    |
|         |             |                       | e |                    |
|         |             |                       | r |                    |
|         |             |                       | m |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | n |                    |
|         |             |                       | e |                    |
|         |             |                       | e |                    |
|         |             |                       | d |                    |
|         |             |                       | s |                    |
+---------+-------------+-----------------------+---+--------------------+
| Data/   | Migration   | -   Migration tooling | - |                    |
| Storage | tooling     |     from existing     |   |                    |
| Mi      | from        |     environment to    |   |                    |
| gration | existing    |     PowerVS           |   |                    |
|         | environment |                       | I |                    |
|         | to PowerVS  | -   DB DR Replication | m |                    |
|         |             |                       | a |                    |
|         |             | -   SAP Standard      | g |                    |
|         |             |     Migrations Tools  | e |                    |
|         |             |                       |   |                    |
|         |             | -   Backup/ Restore   |   |                    |
|         |             |     tools             |   |                    |
|         |             |                       |   |                    |
|         |             | -   Aspera data       | i |                    |
|         |             |     transfer          | m |                    |
|         |             |                       | p |                    |
|         |             | -   GLVM              | o |                    |
|         |             |                       | r |                    |
|         |             | -   For more          | t |                    |
|         |             |     information see   |   |                    |
|         |             |     [Moving SAP       |   |                    |
|         |             |     Worklo            |   |                    |
|         |             | ads](https://cloud.ib |   |                    |
|         |             | m.com/docs/sap?topic= | o |                    |
|         |             | sap-faq-moving-sap-wo | b |                    |
|         |             | rkloads#faq-moving-sa | v |                    |
|         |             | p-workloads-overview) | i |                    |
|         |             |                       | a |                    |
|         |             |                       | t |                    |
|         |             |                       | e |                    |
|         |             |                       | s |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | n |                    |
|         |             |                       | e |                    |
|         |             |                       | e |                    |
|         |             |                       | d |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | f |                    |
|         |             |                       | o |                    |
|         |             |                       | r |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | t |                    |
|         |             |                       | e |                    |
|         |             |                       | s |                    |
|         |             |                       | t |                    |
|         |             |                       | i |                    |
|         |             |                       | n |                    |
|         |             |                       | g |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | a |                    |
|         |             |                       | n |                    |
|         |             |                       | d |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | u |                    |
|         |             |                       | s |                    |
|         |             |                       | e |                    |
|         |             |                       | f |                    |
|         |             |                       | u |                    |
|         |             |                       | l |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | f |                    |
|         |             |                       | o |                    |
|         |             |                       | r |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | n |                    |
|         |             |                       | o |                    |
|         |             |                       | n |                    |
|         |             |                       | - |                    |
|         |             |                       | p |                    |
|         |             |                       | r |                    |
|         |             |                       | o |                    |
|         |             |                       | d |                    |
|         |             |                       | / |                    |
|         |             |                       | P |                    |
|         |             |                       | o |                    |
|         |             |                       | C |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | s |                    |
|         |             |                       | y |                    |
|         |             |                       | s |                    |
|         |             |                       | t |                    |
|         |             |                       | e |                    |
|         |             |                       | m |                    |
|         |             |                       | s |                    |
|         |             |                       |   |                    |
|         |             |                       | - |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | U |                    |
|         |             |                       | s |                    |
|         |             |                       | i |                    |
|         |             |                       | n |                    |
|         |             |                       | g |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | N |                    |
|         |             |                       | a |                    |
|         |             |                       | t |                    |
|         |             |                       | i |                    |
|         |             |                       | v |                    |
|         |             |                       | e |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | D |                    |
|         |             |                       | B |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | R |                    |
|         |             |                       | e |                    |
|         |             |                       | p |                    |
|         |             |                       | l |                    |
|         |             |                       | i |                    |
|         |             |                       | c |                    |
|         |             |                       | a |                    |
|         |             |                       | t |                    |
|         |             |                       | i |                    |
|         |             |                       | o |                    |
|         |             |                       | n |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | m |                    |
|         |             |                       | i |                    |
|         |             |                       | n |                    |
|         |             |                       | i |                    |
|         |             |                       | m |                    |
|         |             |                       | i |                    |
|         |             |                       | z |                    |
|         |             |                       | e |                    |
|         |             |                       | s |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | n |                    |
|         |             |                       | e |                    |
|         |             |                       | e |                    |
|         |             |                       | d |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | f |                    |
|         |             |                       | o |                    |
|         |             |                       | r |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | t |                    |
|         |             |                       | e |                    |
|         |             |                       | s |                    |
|         |             |                       | t |                    |
|         |             |                       | i |                    |
|         |             |                       | n |                    |
|         |             |                       | g |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | e |                    |
|         |             |                       | f |                    |
|         |             |                       | f |                    |
|         |             |                       | o |                    |
|         |             |                       | r |                    |
|         |             |                       | t |                    |
|         |             |                       | s |                    |
|         |             |                       |   |                    |
|         |             |                       | - |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | S |                    |
|         |             |                       | A |                    |
|         |             |                       | P |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | S |                    |
|         |             |                       | t |                    |
|         |             |                       | a |                    |
|         |             |                       | n |                    |
|         |             |                       | d |                    |
|         |             |                       | a |                    |
|         |             |                       | r |                    |
|         |             |                       | d |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | M |                    |
|         |             |                       | i |                    |
|         |             |                       | g |                    |
|         |             |                       | r |                    |
|         |             |                       | a |                    |
|         |             |                       | t |                    |
|         |             |                       | i |                    |
|         |             |                       | o |                    |
|         |             |                       | n |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | o |                    |
|         |             |                       | p |                    |
|         |             |                       | t |                    |
|         |             |                       | i |                    |
|         |             |                       | o |                    |
|         |             |                       | n |                    |
|         |             |                       | s |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | l |                    |
|         |             |                       | i |                    |
|         |             |                       | k |                    |
|         |             |                       | e |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | S |                    |
|         |             |                       | W |                    |
|         |             |                       | P |                    |
|         |             |                       | M |                    |
|         |             |                       | , |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | D |                    |
|         |             |                       | M |                    |
|         |             |                       | O |                    |
|         |             |                       |   |                    |
|         |             |                       | - |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | A |                    |
|         |             |                       | S |                    |
|         |             |                       | P |                    |
|         |             |                       | E |                    |
|         |             |                       | R |                    |
|         |             |                       | A |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | f |                    |
|         |             |                       | o |                    |
|         |             |                       | r |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | h |                    |
|         |             |                       | i |                    |
|         |             |                       | g |                    |
|         |             |                       | h |                    |
|         |             |                       | - |                    |
|         |             |                       | s |                    |
|         |             |                       | p |                    |
|         |             |                       | e |                    |
|         |             |                       | e |                    |
|         |             |                       | d |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | d |                    |
|         |             |                       | a |                    |
|         |             |                       | t |                    |
|         |             |                       | a |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | t |                    |
|         |             |                       | r |                    |
|         |             |                       | a |                    |
|         |             |                       | n |                    |
|         |             |                       | s |                    |
|         |             |                       | f |                    |
|         |             |                       | e |                    |
|         |             |                       | r |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | i |                    |
|         |             |                       | d |                    |
|         |             |                       | e |                    |
|         |             |                       | a |                    |
|         |             |                       | l |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | f |                    |
|         |             |                       | o |                    |
|         |             |                       | r |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | M |                    |
|         |             |                       | i |                    |
|         |             |                       | g |                    |
|         |             |                       | r |                    |
|         |             |                       | a |                    |
|         |             |                       | t |                    |
|         |             |                       | i |                    |
|         |             |                       | o |                    |
|         |             |                       | n |                    |
|         |             |                       | s |                    |
|         |             |                       |   |                    |
|         |             |                       | M |                    |
|         |             |                       | i |                    |
|         |             |                       | n |                    |
|         |             |                       | i |                    |
|         |             |                       | m |                    |
|         |             |                       | i |                    |
|         |             |                       | z |                    |
|         |             |                       | e |                    |
|         |             |                       |   |                    |
|         |             |                       | - |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | B |                    |
|         |             |                       | u |                    |
|         |             |                       | s |                    |
|         |             |                       | i |                    |
|         |             |                       | n |                    |
|         |             |                       | e |                    |
|         |             |                       | s |                    |
|         |             |                       | s |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | d |                    |
|         |             |                       | i |                    |
|         |             |                       | s |                    |
|         |             |                       | r |                    |
|         |             |                       | u |                    |
|         |             |                       | p |                    |
|         |             |                       | t |                    |
|         |             |                       | i |                    |
|         |             |                       | o |                    |
|         |             |                       | n |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | d |                    |
|         |             |                       | u |                    |
|         |             |                       | e |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | t |                    |
|         |             |                       | o |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | m |                    |
|         |             |                       | i |                    |
|         |             |                       | g |                    |
|         |             |                       | r |                    |
|         |             |                       | a |                    |
|         |             |                       | t |                    |
|         |             |                       | i |                    |
|         |             |                       | o |                    |
|         |             |                       | n |                    |
|         |             |                       |   |                    |
|         |             |                       | - |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | C |                    |
|         |             |                       | o |                    |
|         |             |                       | s |                    |
|         |             |                       | t |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | a |                    |
|         |             |                       | n |                    |
|         |             |                       | d |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | e |                    |
|         |             |                       | f |                    |
|         |             |                       | f |                    |
|         |             |                       | o |                    |
|         |             |                       | r |                    |
|         |             |                       | t |                    |
|         |             |                       | s |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | i |                    |
|         |             |                       | n |                    |
|         |             |                       | c |                    |
|         |             |                       | u |                    |
|         |             |                       | r |                    |
|         |             |                       | r |                    |
|         |             |                       | e |                    |
|         |             |                       | d |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | i |                    |
|         |             |                       | n |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | t |                    |
|         |             |                       | e |                    |
|         |             |                       | s |                    |
|         |             |                       | t |                    |
|         |             |                       | i |                    |
|         |             |                       | n |                    |
|         |             |                       | g |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | m |                    |
|         |             |                       | i |                    |
|         |             |                       | g |                    |
|         |             |                       | r |                    |
|         |             |                       | a |                    |
|         |             |                       | t |                    |
|         |             |                       | e |                    |
|         |             |                       | d |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       |   |                    |
|         |             |                       | d |                    |
|         |             |                       | a |                    |
|         |             |                       | t |                    |
|         |             |                       | a |                    |
|         |             |                       | b |                    |
|         |             |                       | a |                    |
|         |             |                       | s |                    |
|         |             |                       | e |                    |
|         |             |                       | s |                    |
+---------+-------------+-----------------------+---+--------------------+

## 

## Architecture decisions for Network

+--------------+---------------+---------------+-----------------------+
| **           | **            | **Decision**  | **Rationale**         |
| Architecture | Requirement** |               |                       |
| decision**   |               |               |                       |
+==============+===============+===============+=======================+
| Connectivity | Connectivity  | Redundant     | Preferred depending   |
| from Cloud   | needed        | Direct Link   | on security           |
| to           | between       | Connect       | requirements. Lower   |
| enterprise   | client and    | Connections   | cost than DL          |
|              | IBM Cloud     |               | Dedicated             |
+--------------+---------------+---------------+-----------------------+
| Connectivity | Secure,       | P2P VPN       | [VPN                  |
| from Managed | encrypted     | through VPC   | Gateway](https://clo  |
| Service      | connectivity  | VPN Gateway   | ud.ibm.com/docs/vpc?t |
| Providers    | between MSPs  |               | opic=vpc-using-vpn) - |
|              | and IBM Cloud |               | securely connect      |
|              |               |               | Virtual Private Cloud |
|              |               |               | (VPC) to another      |
|              |               |               | private network       |
|              |               |               | (site-2-site) for     |
|              |               |               | management purposes.  |
|              |               |               |                       |
|              |               |               | A VPN gateway         |
|              |               |               | consists of two       |
|              |               |               | back-end instances    |
|              |               |               | for high availability |
|              |               |               | in the same zone      |
+--------------+---------------+---------------+-----------------------+
| BYOIP/Edge   | Capability    | -   IBM Cloud | Client can [bring     |
| Gateway      | needed for    |     VPC       | their own             |
|              | customer to   |               | subnet](https:/       |
|              | provide       |   facilitates | /cloud.ibm.com/docs/v |
|              | isolation,    |     BYOIP     | pc?topic=vpc-configur |
|              | security and  |               | ing-address-prefixes) |
|              | edge routing  | -   Edge      | IP address range to a |
|              | services      |     Gateways: | IBM Cloud VPC         |
|              |               |     Palo      |                       |
|              |               |     Alto,     | Edge Gateway is       |
|              |               |     Fortinet, | client choice based   |
|              |               |     F5 -      | on requirements       |
|              |               |     client    |                       |
|              |               |     choice    |                       |
|              |               |     based on  |                       |
|              |               |               |                       |
|              |               |  requirements |                       |
+--------------+---------------+---------------+-----------------------+
| Network      | Ability to    | VPCs and      | Native VPC isolation  |
| Segmentati   | provide       | subnets       | through the use of    |
| on/Isolation | network       |               | separate VPCs and     |
|              | isolation     | Separate      | subnets for prod,     |
|              | across        | PowerVS LPARs | non-prod environments |
|              | workloads     |               | and separation of     |
|              |               |               | workload              |
|              |               |               |                       |
|              |               |               | Separate PowerVS      |
|              |               |               | LPARs                 |
+--------------+---------------+---------------+-----------------------+
| Cloud Native | Ability to    | Virtual       | Communicate with IBM  |
| Connectivity | connect to    | Private       | Cloud services over   |
| (to cloud    | cloud         | Endpoints     | the private network   |
| services)    | services over |               | using a virtual       |
|              | the private   |               | private endpoint      |
|              | network       |               | (VPE)                 |
+--------------+---------------+---------------+-----------------------+
| Cloud        | Connect       | Transit       | Use TGW to connect    |
| Landing-zone | across        | Gateway       | separate VPCs (Edge), |
| Connectivity | multiple VPCs |               | Classic (if needed)   |
|              | and to IBM    | Global        | and PowerVS. Global   |
| VPC to VPC   | Cloud Classic | Transit       | transit gateway to    |
|              | environments  | Gateway       | connect to            |
|              |               |               | environments in other |
|              |               |               | regions for           |
|              |               |               | resiliency data       |
|              |               |               | replication purposes. |
+--------------+---------------+---------------+-----------------------+
| Load         | Load          | Cloud         | Public load balancing |
| Balancing    | balancing     | Internet      | for resiliency needs  |
| (Public)     | over the      | Services      | as per SAP best       |
|              | public        | (CIS)         | practices. CIS also   |
|              | network       |               | provides DDoS         |
|              | across two    |               | services.             |
|              | regions in    |               |                       |
|              | the event of  |               |                       |
|              | an outage     |               |                       |
|              | (DR) for      |               |                       |
|              | failover to   |               |                       |
|              | the other     |               |                       |
|              | region.       |               |                       |
+--------------+---------------+---------------+-----------------------+
| Load         | Load          | SAP Web       | The ALB will load     |
| Balancing    | balancing     | Dispatcher    | balance               |
| (Private)    | workloads     |               | inter-application     |
|              | across        | IBM Cloud     | server requests       |
|              | multiple      | Application   | across Web Dispatcher |
|              | workload      | Load Balancer | hosts. The ALB is a   |
|              | instances     | (ALB)         | floating IP with      |
|              | over the      |               | multiple subnets or   |
|              | private       |               | servers attached to   |
|              | network       |               | the backend pool.     |
+--------------+---------------+---------------+-----------------------+
| Domain Name  | Ability to    | IBM will      | This is the default   |
| System (DNS) | resolve DNS   | continue to   | option in the absence |
|              | names on site | forward/relay | of a specific         |
|              |               | the DNS to    | customer requirement  |
|              |               | client DNS    | to manage DNS         |
|              |               | Servers       |                       |
|              |               | onsite        |                       |
+--------------+---------------+---------------+-----------------------+

## 

## Architecture decisions for security

+---------+---------------+---+--------------------+----------------------+
| **Archi | **            | * |                    | **Rationale**        |
| tecture | Requirement** | * |                    |                      |
| dec     |               | D |                    |                      |
| ision** |               | e |                    |                      |
|         |               | c |                    |                      |
|         |               | i |                    |                      |
|         |               | s |                    |                      |
|         |               | i |                    |                      |
|         |               | o |                    |                      |
|         |               | n |                    |                      |
|         |               | * |                    |                      |
|         |               | * |                    |                      |
+=========+===============+===+====================+======================+
| Primary | Ability to    |   | Power VS uses IBM  | Industry-Standard    |
| Storage | encrypt data  |   | FlashSystem        | AES-256 encryption   |
|         | at rest       |   | Storage with       | as provided by       |
|         |               |   | AES-256 (Advanced  | FlashSystem Storage  |
|         |               |   | Encryption         | for PowerVS          |
|         |               |   | Standard)          |                      |
|         |               |   | hardware-based     |                      |
|         |               |   | encryption         |                      |
+---------+---------------+---+--------------------+----------------------+
| Backup  | Ability to    |   | Cloud Object       | By default, all      |
| Storage | encrypt       |   | Storage Encryption | objects that are     |
| &       | backups at    |   |                    | stored in IBM Cloud  |
|         | rest          |   | Provider managed   | Object Storage are   |
| Archive |               |   | encryption         | encrypted by using   |
| Storage |               |   | provided by block  | randomly generated   |
|         |               |   | storage.           | keys and an          |
|         |               |   |                    | all-                 |
|         |               |   |                    | or-nothing-transform |
|         |               |   |                    | (AONT).              |
|         |               |   |                    |                      |
|         |               |   |                    | The file systems     |
|         |               |   |                    | associated within    |
|         |               |   |                    | any Classic          |
|         |               |   |                    | infrastructure is    |
|         |               |   |                    | IBM Cloud Block      |
|         |               |   |                    | Storage Volume or    |
|         |               |   |                    | file storage. Block  |
|         |               |   |                    | and File Storage     |
|         |               |   |                    | allows for Provider  |
|         |               |   |                    | managed encryption   |
|         |               |   |                    | (IBM Cloud managed   |
|         |               |   |                    | keys) which will be  |
|         |               |   |                    | configured by        |
|         |               |   |                    | default when storage |
|         |               |   |                    | is setup.            |
+---------+---------------+---+--------------------+----------------------+
| HANA    | Ability to    |   | HANA Data Volume   | DVE encrypts HANA    |
| Data    | encrypt SAP   |   | Encryption (DVE)   | data at the          |
| Enc     | HANA data at  |   |                    | persistence layer,   |
| ryption | rest          |   |                    | protecting data      |
|         |               |   |                    | stored on disk from  |
|         |               |   |                    | unauthorized access  |
|         |               |   |                    | at operating system  |
|         |               |   |                    | level.               |
+---------+---------------+---+--------------------+----------------------+
| Data -  | IaaS platform |   | -   FTPs and HTTPs | Client to server     |
| Enc     | must support  |   |     protocols      | encryption can be    |
| ryption | Data          |   |     (client to     | accomplished over    |
| in      | Encryption:   |   |     server)        | HTTPs (SSL);         |
| transit |               |   |                    |                      |
|         | 1.  Client to |   | -   Inflight       | File transfer        |
|         |     server    |   |     encryption for | encryption via FTPs  |
|         |               |   |     HANA DB        |                      |
|         | 2.  App. LPAR |   |     supported      | SAP supports         |
|         |     to DB     |   |     using TLS/SSL, | encryption between   |
|         |     LPAR      |   |     encryption for | application and      |
|         |               |   |     App to DB      | database using SSL.  |
+---------+---------------+---+--------------------+----------------------+
| I       | Securely      |   | IBM Cloud IAM      | Use IAM access       |
| dentity | authenticate  |   |                    | policies to assign   |
| Access  | users for     |   |                    | users, service IDs,  |
| & Role  | platform      |   |                    | and trusted profiles |
| Man     | services and  |   |                    | access to resources  |
| agement | control       |   |                    | within the IBM Cloud |
| (IDM)   | access to     |   |                    | account.             |
|         | resources     |   |                    |                      |
|         | consistently  |   |                    |                      |
|         | across IBM    |   |                    |                      |
|         | Cloud         |   |                    |                      |
+---------+---------------+---+--------------------+----------------------+
| Pri     | Privileged    |   | -   BYO Bastion    | Securely access      |
| vileged | access        |   |     host (or       | remote resources     |
| I       | management    |   |     Privileged     | over the private     |
| dentity | services for  |   |     Access         | network for          |
| &       | a             |   |     Gateway) with  | management purposes; |
| Access  | dministrative |   |     PAM SW         | bastion accessed via |
| Man     | purposes      |   |     deployed in    | SSH. Session         |
| agement |               |   |     Edge VPC       | recording, tracking  |
|         |               |   |                    | all activities,      |
|         |               |   | -   2FA            | successful or not,   |
|         |               |   |     Authentication | to note any          |
|         |               |   |     though IBM     | potential threats    |
|         |               |   |     Security       |                      |
|         |               |   |     Verify         |                      |
+---------+---------------+---+--------------------+----------------------+
| Core    | -   Strict    |   | -   Separate VPCs, | A design combination |
| Network |               |   |     Subnets, ACLs  | using:               |
| Pro     |    separation |   |     and Security   |                      |
| tection |     of duties |   |     Groups for     | Separate VPCs        |
|         |               |   |     non-SAP        | (transit,            |
|         | -   Isolated  |   |     workloads that | management,          |
|         |     security  |   |     may exist in   | workload) connected  |
|         |     zones     |   |     VPC.           | through transit      |
|         |     between   |   |                    | gateway and, the use |
|         |               |   | -   In addition to | of edge firewall     |
|         |  environments |   |     VPC            | capabilities.        |
|         |               |   |     capabilities,  | Subnets, Security    |
|         | -   Isolated, |   |     use of virtual | Groups and ACLs to   |
|         |     private   |   |     firewalls      | create an            |
|         |     cloud     |   |     deployed to    | Edge/Transit VPC     |
|         |               |   |     the            | design along with    |
|         |   environment |   |     Edge/Transit   | isolated LPARs on    |
|         |               |   |     VPC to provide | PowerVS              |
|         |               |   |     advance FW and |                      |
|         |               |   |     routing        |                      |
|         |               |   |     capabilities   |                      |
|         |               |   |     between VPC    |                      |
|         |               |   |     and PowerVS    |                      |
|         |               |   |                    |                      |
|         |               |   | -   Separation of  |                      |
|         |               |   |     application    |                      |
|         |               |   |     and DB in      |                      |
|         |               |   |     separate       |                      |
|         |               |   |     PowerVS LPARs  |                      |
|         |               |   |     well as        |                      |
|         |               |   |     separate       |                      |
|         |               |   |     production and |                      |
|         |               |   |     non-Production |                      |
|         |               |   |     environments.  |                      |
+---------+---------------+---+--------------------+----------------------+
| Threat  | -   Boundary  |   | BYO Virtual        | -   Provided by      |
| de      |               |   | Firewall (on VSI)  |     Enterprise       |
| tection |   protection: |   | in Edge VPC        |     Network DMZ      |
| and     |     highest   |   | (deployed across   |                      |
| r       |     level of  |   | availability       | -   In addition, if  |
| esponse |     isolation |   | zones) -- client   |     client requires: |
|         |     from      |   | choices            |                      |
|         |     external  |   |                    | -   Virtual FW on    |
|         |     network   |   | -   [Fort          |     VSI in the       |
|         |     threats   |   | igate](https://clo |     [Transit/Edge    |
|         |               |   | ud.ibm.com/catalog |     VPC](http        |
|         | -   IPS/IDS   |   | /content/ibm-forti | s://cloud.ibm.com/do |
|         |               |   | gate-AP-HA-terrafo | cs/solution-tutorial |
|         |    protection |   | rm-deploy-5dd3e4ba | s?topic=solution-tut |
|         |     at all    |   | -c94b-43ab-b416-c1 | orials-vpc-transit1) |
|         |     i         |   | c313479cec-global) |                      |
|         | ngress/egress |   |                    | -   Client           |
|         |               |   | -   [Juniper       |     preference       |
|         | -   Unified   |   |     vSRX](https:   |     however          |
|         |     Threat    |   | //cloud.ibm.com/ca |     recommendation   |
|         |               |   | talog/content/juni |     is Fortigate or  |
|         |    Management |   | per-vsrx-catalog-d |     Juniper.         |
|         |     (UTM)     |   | eploy-1.4-dc1e707c |                      |
|         |     Firewall  |   | -33dd-4321-b2a5-c2 | -   Fortigate        |
|         |               |   | 2dbf0dd0ee-global) |     supports native  |
|         |               |   |                    |     HA configuration |
|         |               |   | -   [Checkpoint    |                      |
|         |               |   |     Cloud          | -   Fortigate and    |
|         |               |   |     Guard](https:/ |     Juniper both     |
|         |               |   | /cloud.ibm.com/cat |     support both IPS |
|         |               |   | alog/content/check |     & IDS            |
|         |               |   | point-iaas-gw-ibm- |                      |
|         |               |   | vpc-1.0.7-9ed8dbde |                      |
|         |               |   | -2931-45f5-a7a7-0c |                      |
|         |               |   | 90ce0d2686-global) |                      |
|         |               |   |                    |                      |
|         |               |   | -   [Palo          |                      |
|         |               |   |     Al             |                      |
|         |               |   | to](https://cloud. |                      |
|         |               |   | ibm.com/catalog/co |                      |
|         |               |   | ntent/ibmcloud-vms |                      |
|         |               |   | eries-1.9-6470816d |                      |
|         |               |   | -562d-4627-86a5-fe |                      |
|         |               |   | 3ad4e94b30-global) |                      |
+---------+---------------+---+--------------------+----------------------+

## Architecture decisions for resiliency

+-----------+-----------+--------------+-------------------------------+
| **Arc     | **Requ    | **Decision** | **Rationale**                 |
| hitecture | irement** |              |                               |
| d         |           |              |                               |
| ecision** |           |              |                               |
+===========+===========+==============+===============================+
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
+-----------+-----------+--------------+-------------------------------+
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
+-----------+-----------+--------------+-------------------------------+
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
+-----------+-----------+--------------+-------------------------------+
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
+-----------+-----------+--------------+-------------------------------+
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
+-----------+-----------+--------------+-------------------------------+
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
+-----------+-----------+--------------+-------------------------------+
| Backup -  |           | IBM Storage  | Alternative: Veeam is         |
| AIX       |           | Protect      | supported on AIX. However, if |
|           |           |              | there is HANA/Linux in        |
|           |           |              | client's landscape, only one  |
|           |           |              | SAP certified backup tool     |
|           |           |              | should be used. Then IBM      |
|           |           |              | Storage Protect is            |
|           |           |              | recommended.                  |
+-----------+-----------+--------------+-------------------------------+
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
+-----------+-----------+--------------+-------------------------------+
| Backup -- |           | FalconStor   | Although SAP on IBM i/Power   |
| IBM i     |           | VTL          | Systems Virtual Servers is    |
|           |           |              | not SAP certified, client may |
|           |           |              | choose it if service provider |
|           |           |              | supports.                     |
+-----------+-----------+--------------+-------------------------------+
| Backup -- | Point in  | FlashCopy    | IBM Power Systems Virtual     |
| other     | time copy |              | Servers offering              |
|           | of data   |              |                               |
|           | volume    |              |                               |
+-----------+-----------+--------------+-------------------------------+

## 

## Architecture decisions for service management

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
+=========+===+==================+===+=============+==========================+
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
+---------+---+------------------+---+-------------+--------------------------+
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
+---------+---+------------------+---+-------------+--------------------------+
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
+---------+---+------------------+---+-------------+--------------------------+
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
+---------+---+------------------+---+-------------+--------------------------+
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
+---------+---+------------------+---+-------------+--------------------------+
| Mana    |   | Automate         |   | Ansible +   |                          |
| gement/ |   | management       |   | Terraform   |                          |
|         |   | processes to     |   |             |                          |
| Orches  |   | keep             |   |             |                          |
| tration |   | applications and |   |             |                          |
|         |   | infrastructure   |   |             |                          |
|         |   | secure, up to    |   |             |                          |
|         |   | date, and        |   |             |                          |
|         |   | available        |   |             |                          |
+---------+---+------------------+---+-------------+--------------------------+

**\
**

# 

# 

# References

[IBM Cloud for
SAP](https://cloud.ibm.com/docs/sap?topic=sap-get-started)

[IBM Power Systems Infrastructure environment
introduction](https://cloud.ibm.com/docs/sap?topic=sap-power-env-introduction)

[FAQ -- SAP on IBM
Cloud](https://cloud.ibm.com/docs/sap?topic=sap-faq-ibm-cloud-for-sap)

[Global Replication Services Solution using IBM Power Virtual
Server](https://cloud.ibm.com/media/docs/downloads/power-iaas/Global_Replication_Services_Solution_using_IBM_Power_Virtual_Server.pdf)

[SAP HANA on IBM Power Systems Virtual
Servers](https://www.redbooks.ibm.com/abstracts/redp5693.html)

[SAP HANA on IBM Power Systems Architectural
Summary](https://www.redbooks.ibm.com/redpieces/pdfs/redp5569.pdf)

[SAP HANA Administration
Guide](https://help.sap.com/docs/SAP_HANA_PLATFORM/6b94445c94ae495c83a19646e7c3fd56/330e5550b09d4f0f8b6cceb14a64cd22.html)

[Veeam Backup & Replication 12 Veeam Plug-ins for Enterprise
Applications
Guide](https://helpcenter.veeam.com/docs/backup/plugins/sap_hana_plugin.html?ver=120)

[IBM Spectrum Protect for Enterprise Resource Planning Data Protection
for SAP HANA Version
8.1.4](https://www.ibm.com/docs/en/SSER83_8.1.4/erp.hana/b_dp_erp_sap_hana_guide.pdf)
