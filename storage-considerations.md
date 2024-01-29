---

copyright:
  years: 2024
lastupdated: "2024-01-12"

subcollection: pattern-sap-on-powervs

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Storage design
{: #storage-design}

SAP solutions want to ensure that there is enough available storage to
accommodate the existing environment and allow for additional data
growth for primary, backup, and archive storage. You need to choose
the appropriate SAP Certified profile to meet primary storage and growth
requirements for workloads.

The storage tiers in Power Systems Virtual Server are based on I/O
operations per second (IOPS). It means that the performance of your
storage volumes is limited to the maximum number of IOPS based on volume
size and storage tier.

For each Power Systems Virtual Server instance, you must select a Flash Storage from an IBM FS9000 series device [storage
tier](/docs/power-iaas?topic=power-iaas-about-virtual-server#storage-tiers):
Tier 1 is set to 10 IOPS/GB or Tier 3 is set to 3
IOPS/GB.

When you plan an SAP deployment, IOPS and its effect on latency needs to
also be considered. A 100 GB PowerVS Tier 3 storage volume can receive up
to 300 IOPs, and a 100 GB Tier 1 storage volume can receive up to 1000
IOPS. After the IOPS limit is reached for the storage volume, the I/O
latency increases.

Storage tiers differ and Storage tiers cannot be mixed.

For SAP HANA, only Tier 1 storage with 10 IOPS is supported and is required.

For SAP NetWeaver, both block Tier 1 with 10 IOPS and block Tier 3 with 3 IOPS are supported.

For SAP NetWeaver and SAP AnyDB databases (such as IBM Db2 or Oracle
DB), Tier 1 (NVMe) is recommended but Tier 3 (SSD) can be used.
Typically, Tier 3 storage tier is not suitable for production workloads.
When choosing a storage tier, make sure that you consider not just the
average I/O load, but more importantly the peak IOPS of your storage
workload.

The client is responsible for backing up data on PowerVS FlashSystem FS9000 storage. Custom backup solutions that use Veeam/Spectrum Protect on Bare metal with local disk for operational and Cloud Object Storage for 2nd copy and long-term archiving. Backups over the network need to be considered for backup windows and restores.

[Other Storage
Considerations](https://cloud.ibm.com/docs/sap?topic=sap-storage-design-considerations)
