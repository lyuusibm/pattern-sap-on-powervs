---

copyright:
  years: 2024
lastupdated: "2024-01-12"

subcollection: pattern-sap-on-powervs

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Compute design
{: #compute-design}

Power Virtual Servers are available with flexible hardware
configurations on both Power S922 and E980. In
accordance with existing SAP NetWeaver or SAP AnyDB for IBM Power
Systems best practices and guidance from SAP, it is permitted to define a
custom size of the IBM Power Virtual Server to use for SAP NetWeaver.

The Flexibility of IBM Power Systems Virtual Servers hardware capability
includes:

-   Cores (CPU)

-   Memory (RAM)

-   Data volume size

-   Data volume type / performance tier

-   Network interfaces

-   PowerVM Host Pinning Policy (soft or hard)

-   PowerVM Host CPU Binding (dedicated or shared)

PowerVS custom profiles are for nonproduction for testing or
development use only. Custom, non-SAP Certified profiles aren't
intended for production deployments and aren't supported or certified
for SAP production. Custom profiles should not be used to go from a
nonproduction environment to a production environment.

Multiple SAP Certified Power Virtual Server profiles are available.

NetWeaver PowerVS profiles:
* [IBM Power Virtual Server certified profiles for SAP NetWeaver](/docs/sap?topic=sap-nw-iaas-offerings-profiles-power-vs)

HANA PowerVS profiles:
* [IBM Power Virtual Server certified profiles for SAP HANA](/docs/sap?topic=sap-hana-iaas-offerings-profiles-power-vs)

For more sizing information, see [Sizing process for SAP Systems](/docs/sap?topic=sap-sizing&interface=ui)

For a distributed SAP installation, it is best to have all nodes in the
same location (Availability Zone or Datacenter). Deviation from this
setup can cause latency and timeouts, which render your SAP system
unresponsive. Therefore, SAP does not support the application layer and database to be deployed across different zones or COLOs. The application and database layers must exist in the same availability zone or COLO.

Databases typically experience growth over time so database size and
expected data growth rates should be considered when you
plan database deployments.

## SAP AnyDB
{: #sap-anydb}

AnyDB refers to any non-HANA, SAP supported database. SAP supports: IBM
Db2, Oracle, Microsoft SQL Server, SAP Max DB, SAP ASE, and SAP IQ.

Deploying SAP AnyDB on IBM PowerVS is similar to deployments with
infrastructure with on-premises data centers. You can use the information that is provided from SAP and the RDBMS providers. The following links contain design considerations for each database:

* [IBM Db2](/docs/sap?topic=sap-anydb-ibm-db2)
* [SAP Max DB](/docs/sap?topic=sap-anydb-sap-maxdb)
* [SAP ASE](/docs/sap?topic=sap-anydb-sap-ase)
* [SAP IQ](/docs/sap?topic=sap-anydb-sap-iq)

Oracle RAC for high availability and Oracle Data Guard for disaster
recovery can be deployed to PowerVS as an AnyDB option. Best practices
and guidance for both can be found at the following links:

* [Oracle RAC on PowerVS](/docs/pattern-oracle-rac-on-powervs?topic=pattern-oracle-rac-on-powervs-overview)

* [Oracle Disaster Recovery that uses Data Guard](/docs/pattern-oracle-disaster-recovery-on-powervs?topic=pattern-oracle-disaster-recovery-on-powervs-Oracle-dr-ibm-pvs#oracle-data-guard)
