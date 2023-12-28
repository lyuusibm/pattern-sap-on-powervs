---

copyright:
  years: 2023
lastupdated: "2023-12-26"

subcollection: pattern-sap-on-powervs

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Storage design
{: #storage-design}

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
