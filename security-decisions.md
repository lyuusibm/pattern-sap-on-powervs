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
| Primary | Ability to    |   | Power VS uses IBM  | Industry-Standard    |
| Storage | encrypt data  |   | FlashSystem        | AES-256 encryption   |
|         | at rest       |   | Storage with       | as provided by       |
|         |               |   | AES-256 (Advanced  | FlashSystem Storage  |
|         |               |   | Encryption         | for PowerVS          |
|         |               |   | Standard)          |                      |
|         |               |   | hardware-based     |                      |
|         |               |   | encryption         |                      |
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
| HANA    | Ability to    |   | HANA Data Volume   | DVE encrypts HANA    |
| Data    | encrypt SAP   |   | Encryption (DVE)   | data at the          |
| Enc     | HANA data at  |   |                    | persistence layer,   |
| ryption | rest          |   |                    | protecting data      |
|         |               |   |                    | stored on disk from  |
|         |               |   |                    | unauthorized access  |
|         |               |   |                    | at operating system  |
|         |               |   |                    | level.               |
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
{: caption="Table 1. Architecture decisions for security" caption-side="bottom"}
