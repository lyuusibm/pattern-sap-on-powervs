---

copyright:
  years: 2023
lastupdated: "2023-11-28"

subcollection: <repo-name>

keywords:

---

# Networking architecture decisions
{: #networking-architecture}

Let's write a short description...

## Enterprise connectivity architecture decisions
{: #enterprise-connectivity}

And, then let's introduce the table. Something like: "The following are enterprise connectivity architecture decisions for this design."

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Connectivity for management | Provide secure, encrypted connectivity to the cloud’s private network for management purposes. | * Client VPN for VPC \n * VPN for VPC | Client VPN for VPC | Client VPN for VPC provides client-to-site connectivity, which allows remote devices to securely connect to the VPC network using an OpenVPN software client. |
{: caption="Table 1. Enterprise connectivity architecture decisions" caption-side="bottom"}

## Network segmentation and isolation architecture decisions
{: #network-segmentation-isolation}

Intro text...

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Web App Deployment | * Deploy workloads in isolated environment and enforce information flow policies. \n * Provide isolated security zones between app tiers	- Virtual Private Clouds (VPCs) | * Virtual Private Clouds (VPCs) \n * Subnets \n * Security Groups (SGs) \n * ACLs | VPCs, subnets, Security Groups (SGs) and ACLs | VPCs provide secure, virtual networks for web apps which are logically isolated from other public cloud tenants. Subnets provide a range of private IP addresses for each Web app tier within a zone. Security Groups and ACLs are used as firewalls to limit access to virtual servers and web app tiers. |
{: caption="Table 2. Network segmentation and isolation architecture decisions" caption-side="bottom"}

## Cloud native connectivity architecture decisions
{: #cloud-native-connectivity}

Intro text...

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Connectivity to Cloud Services | Provide secure connection to Cloud Services | * VPC Gateway + Virtual Private Endpoints (VPE) \n * Private Cloud Service endpoints \n * Public Cloud Service Endpoints | VPC Gateway + Virtual Private Endpoints (VPE) | VPC Gateway + Virtual Private Endpoints enable connectivity to IBM Cloud services using private IP addresses allocated from a VPC subnet. |
| VPC to VPC Connectivity | Connect two or more VPCs over private network | * Global Transit Gateway \n * Local Transit Gateway (TGW) | Local Transit Gateway (TGW) | The Local Transit Gateway enables connectivity between the Management and Workload VPCs. |
{: caption="Table 3. Cloud native connectivity architecture decisions" caption-side="bottom"}

## Load balancing architecture decisions
{: #network-segmentation-isolation}

Intro text...

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Application Load Balancer | Route web user http/https requests | * VPC ALB \n * VPC NLB | VPC ALB | * VPC ALB is recommended for web-based workloads. \n * Provides layer 4 and layer 7 load balancing \n * Supports HTTP, HTTPS, and TCP requests \n * Supports SSL offloading. |
| Local Load Balancing: App & DB Tiers | Distribute requests across zones for high availability | * Public VPC ALB \n * Private VPC ALB \n * Public VPC NLB \n * Private VPC NLB | Private ALB | The Private VPC ALB distributes traffic among virtual servers within the app and DB tiers. The VPC ALB is configured with subnets across multiple zones for multi-zone availability. |
{: caption="Table 4. Load balancing architecture decisions" caption-side="bottom"}

## Domain name system architecture decisions
{: #dns}

Intro text...

| Architecture Decision | Requirement | Alternative | Decision | Rationale |
| -------------- | -------------- | -------------- | -------------- | -------------- |
| Public DNS | Provide DNS resolution to support use of hostnames instead of IP addresses for applications | * IBM Cloud Internet Services (CIS) \n * IBM Cloud DNS	IBM Cloud Internet Services (CIS) | IBM Cloud DNS	IBM Cloud Internet Services (CIS) | IBM Cloud Internet Services supports provisioning and configuring DNS records for public DNS resolution and can be integrated with the public VPC ALBs for the web tier. |
{: caption="Table 5. Domain name system (DNS) architecture decisions" caption-side="bottom"}

