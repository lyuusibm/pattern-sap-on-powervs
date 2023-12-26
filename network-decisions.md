---

copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: pattern-sap-on-powervs

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Architecture decisions for network
{: #network-decisions}

+--------------+---------------+---------------+-----------------------+
| **           | **            | **Decision**  | **Rationale**         |
| Architecture | Requirement** |               |                       |
| decision**   |               |               |                       |
| Connectivity | Connectivity  | Redundant     | Preferred depending   |
| from Cloud   | needed        | Direct Link   | on security           |
| to           | between       | Connect       | requirements. Lower   |
| enterprise   | client and    | Connections   | cost than DL          |
|              | IBM Cloud     |               | Dedicated             |
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
| Cloud Native | Ability to    | Virtual       | Communicate with IBM  |
| Connectivity | connect to    | Private       | Cloud services over   |
| (to cloud    | cloud         | Endpoints     | the private network   |
| services)    | services over |               | using a virtual       |
|              | the private   |               | private endpoint      |
|              | network       |               | (VPE)                 |
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
| Domain Name  | Ability to    | IBM will      | This is the default   |
| System (DNS) | resolve DNS   | continue to   | option in the absence |
|              | names on site | forward/relay | of a specific         |
|              |               | the DNS to    | customer requirement  |
|              |               | client DNS    | to manage DNS         |
|              |               | Servers       |                       |
|              |               | onsite        |                       |
{: caption="Table 2. Architecture decisions for network" caption-side="bottom"}
