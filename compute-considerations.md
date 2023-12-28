---

copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: pattern-sap-on-powervs

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Compute design
{: #compute-design}

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

PowerVS custom profiles are for non-production for testing or
development use only. Custom, non-SAP Certified profiles aren\'t
intended for production deployments and aren\'t supported or certified
for SAP production. Custom profiles should not be used to go from a
non-production environment to a production environment.

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

**SAP AnyDB**

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
