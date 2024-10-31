---
copyright:
  years: 2024
lastupdated: "2024-10-25"

subcollection: pattern-sap-on-powervs

keywords:
---
{{site.data.keyword.attribute-definition-list}}

# Deployment Guide
{: #deployment-guide}

## Before you begin
{: #before-you-begin}

Before you begin:
* An [IBM Cloud account](https://cloud.ibm.com/registration){: external}.
* Understand how [IBM Cloud IAM](https://cloud.ibm.com/docs/secure-enterprise?topic=secure-enterprise-iamoverview) works.
* Understand how to create [IBM Cloud API Key](https://test.cloud.ibm.com/docs-draft/account?topic=account-userapikey&interface=ui) 
* Understand how to create [SSH Key](https://test.cloud.ibm.com/docs/vpc?topic=vpc-ssh-keys&interface=ui).

IBM provides Terraform IBM Modules to jump start SAP deployment on IBM Power System Virtual Servers. Make sure to review the following of each module:
* Prerequisites, for example, IAM, SSH Keys, Schematics ID and so on
* Reference Architectures and Heat Maps
* Understand which components are included


## Deploy Power workspace with a VPC landing zone
{: #deploy-power-wp-vpc-landing-zone}
Per the recommended reference architecture, a VPC infrastructure should be deployed as a landing zone. 
1.	Use [IBM Cloud portal](https://cloud.ibm.com/catalog/architecture/deploy-arch-ibm-pvs-inf-2dd486c7-b317-4aaa-907b-42671485ad96-global?catalog_query=aHR0cHM6Ly9jbG91ZC5pYm0uY29tL2NhdGFsb2cjZGVwbG95YWJsZV9hcmNoaXRlY3R1cmVfdGFi)
2.	Use [Terraform IBM Modules](https://github.com/terraform-ibm-modules/terraform-ibm-powervs-sap)
3.	More information from [IBM Cloud Docs](//users/lifanyu/Downloads/1.https:/cloud.ibm.com/docs/sap-powervs?topic=sap-powervs-automation-solution-overview) 


## Deploy Power Virtual Server for SAP HANA and SAP S/4HANA 
{: #deploy-pvs-hana}
The Terraform IBM Modules provide multiple variations to deploy SAP S4/HANA on IBM Power Systems Virtual Servers, with one HANA instance, 0 to N SAP Netweaver instances, with or without SAP installation and tuning. 
1.	Use [IBM Cloud Portal](https://cloud.ibm.com/catalog/architecture/deploy-arch-ibm-pvs-sap-9aa6135e-75d5-467e-9f4a-ac2a21c069b8-global?catalog_query=aHR0cHM6Ly9jbG91ZC5pYm0uY29tL2NhdGFsb2cjZGVwbG95YWJsZV9hcmNoaXRlY3R1cmVfdGFi) 
2.	Use [Terraform IBM Module](https://github.com/terraform-ibm-modules/terraform-ibm-powervs-sap)
3.	More information on [IBM Docs](https:/cloud.ibm.com/docs/sap-powervs?topic=sap-powervs-automation-solution-overview)

## Deploy Secure Automated Backup with Compass 
{: #deploy-compass}
As noted in the architecture decisions in prior sections, IBM Cloud offers multiple ways to backup SAP workloads. The recommendation is to use [Secure Automated Backup with Compass](https://cloud.ibm.com/catalog/services/secure-automated-backup-with-compass?catalog_query=aHR0cHM6Ly9jbG91ZC5pYm0uY29tL2NhdGFsb2c%2Fc2VhcmNoPWNvbXBhc3Mjc2VhcmNoX3Jlc3VsdHM%3D) which is a backup service with SAP certified tools for SAP workloads including HANA

## Customize your deployment: 
{: #customize-deployment}

1. Enterprise connections from On-premise to IBM Cloud:
  1.1 Direct Link Connect
    This deployment guide assumes the remote side of this connection is already integrated with the exchange provider’s network. If not, please contact the provider to establish this connection.
    * Review the list of Direct Link Connect [providers and locations](https://test.cloud.ibm.com/docs-draft/dl?topic=dl-locations#connect-locations) to select a provider.
    * Review [partner-specific](https://test.cloud.ibm.com/docs-draft/dl?topic=dl-how-to-order-ibm-cloud-dl-connect#instructions-partner) instructions.
    * Order [Direct Link Connect](https://test.cloud.ibm.com/docs-draft/dl?topic=dl-how-to-order-ibm-cloud-dl-connect) to each Region
  1.2 Alternative:  [Direct Link Dedicated](https://test.cloud.ibm.com/docs-draft/dl?topic=dl-how-to-order-ibm-cloud-dl-dedicated) connections can be ordered
2.	To order more Client VPNs for VPC, use [IBM Cloud Portal](https://cloud.ibm.com/vpc-ext/provision/vpnserver?catalog_query=aHR0cHM6Ly9jbG91ZC5pYm0uY29tL2NhdGFsb2c%2Fc2VhcmNoPVZQTiNzZWFyY2hfcmVzdWx0cw%3D%3D)
3.	To order more Transit Gateways, including Global Transit Gateway, use [IBM Cloud portal](https://cloud.ibm.com/interconnectivity/transit/provision?catalog_query=aHR0cHM6Ly9jbG91ZC5pYm0uY29tL2NhdGFsb2c%2Fc2VhcmNoPXRyYW5zaXQlMjUyMGdhdGV3YXkjc2VhcmNoX3Jlc3VsdHM%3D), or [Terraform IBM Module](https://github.com/terraform-ibm-modules/terraform-ibm-transit-gateway)
4.	To order Cloud Internet Services (CIS) , use the [IBM Cloud portal](https://cloud.ibm.com/catalog/services/internet-services?catalog_query=aHR0cHM6Ly9jbG91ZC5pYm0uY29tL2NhdGFsb2c%2Fc2VhcmNoPWNpcyNzZWFyY2hfcmVzdWx0cw%3D%3D), or the [Terraform IBM Module](https://github.com/terraform-ibm-modules/terraform-ibm-cis).
5.	To create more Power workspaces, use [Terraform IBM Module](https://github.com/terraform-ibm-modules/terraform-ibm-powervs-workspace)
6.	To create more Power virtual instances, use [Terraform IBM Module](file:///Users/lifanyu/Downloads/14.4.2	https:/github.com/terraform-ibm-modules/terraform-ibm-powervs-instance)
7.	To create more VPCs, use [Terraform IBM Module](https://github.com/terraform-ibm-modules/terraform-ibm-vpc)
8.	To create more VSIs in VPC, use [Terraform IBM Module](https://github.com/terraform-ibm-modules/terraform-ibm-vpc-vsi)
9.	For examples of Ansible automation on SAP Netweaver, SAP S/4HANA, SAP HANA installations, please look into the [code](https://github.com/terraform-ibm-modules/terraform-ibm-powervs-sap/tree/main/modules/ansible)
10. [IBM Cloud Observability](https://cloud.ibm.com/catalog/7a4d68b4-cf8b-40cd-a3d1-f49aff526eb3/architecture/deploy-arch-ibm-observability-a3137d28-79e0-479d-8a24-758ebd5a0eab-global){: external} for provisioning and configuring logging, monitoring, and activity tracking.
11. [IBM Cloud Event Notifications](https://cloud.ibm.com/catalog/7a4d68b4-cf8b-40cd-a3d1-f49aff526eb3/architecture/deploy-arch-ibm-event-notifications-c7ac3ee6-4f48-4236-b974-b0cd8c624a46-global){: external} for a high-throughput message bus that is built with Apache Kafka.
12. [Security and Compliance Center](https://cloud.ibm.com/catalog/7a4d68b4-cf8b-40cd-a3d1-f49aff526eb3/architecture/deploy-arch-ibm-scc-9423f9bc-1290-4c71-a9ac-01898bfa7ccc-global){: external} for compliance posture of your deployed resources.
13. To deploy [IBM Cloud Internet Services](https://github.com/terraform-ibm-modules/terraform-ibm-cis){: external} powered by Cloudflare, which provides a fast, highly performant, reliable, and secure internet service.
14. To deploy [IBM Storage Protect](https://cloud.ibm.com/catalog/content/SPonIBMCloud-20c54034-d319-48c0-beb6-0b4adc54265c-global?catalog_query=aHR0cHM6Ly9jbG91ZC5pYm0uY29tL2NhdGFsb2c%2Fc2VhcmNoPXN0b3JhZ2UlMjUyMHByb3RlY3Qjc2VhcmNoX3Jlc3VsdHM%3D){: external} instead of using Secure Automated Backup Service by Compass.
