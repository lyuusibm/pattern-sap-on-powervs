---

copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: pattern-sap-on-powervs
keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Overview
{: #overview}

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
