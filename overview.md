---

copyright:
  years: 2024
lastupdated: "2024-01-12"

subcollection: pattern-sap-on-powervs
keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Overview
{: #overview}

The objective of this document is to provide an IBM Solution Design
for the deployment of SAP on IBM Power Virtual Server (PowerVS) to:

- Accelerate and simplify solution design by providing a standard IBM Cloud deployment architecture reference solution for SAP on IBM PowerVS enterprise-class deployments following the [IBM Architecture Framework](/docs/architecture-framework?topic=architecture-framework-intro).

- Provide a prescriptive, end-2-end enterprise-class solution design, with diagrams, component architecture decisions along with rationale for cloud component selection for a secure, resilient SAP on PowerVS deployment.

- Ensure that requirements can be met from performance, system availability, and security perspectives.

This baseline solution is a pattern containing the design and architecture decisions for an SAP deployment on IBM Cloud Power Virtual Server to meet common requirements. Actual client solutions depend on specific client needs. This document does not cover SAP configuration and SAP component deployment scenarios, it is limited to IBM cloud infrastructure options to support SAP workloads.

It describes the deployment of SAP NetWeaver, SAP S/4HANA, BW/4HANA and other SAP products.

IBM Cloud SAP-Certified Infrastructure provides the flexibility to run SAP workloads in the IBM Cloud and the ability to quickly address issues such as:

- Moving SAP workloads to the cloud
- Rapidly expanding or contracting capacity
- Supplementing an existing private cloud architecture
- Setting up Disaster Recovery for On-Premise SAP workloads

Enterprise-class, mission-critical workloads need to be resilient and provide disaster recovery (DR) capabilities and high availability (HA). This pattern illustrates a Single-Zone, Multi-Region design deployed to IBM Power Virtual Server, which can provide both DR and HA to provide 99.95 infrastructure availability for an SAP S/4HANA, SAP BW/4HANA or SAP NetWeaver/AnyDB solution.

