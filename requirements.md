---

copyright:
  years: 2023
lastupdated: "2023-11-28"

subcollection: <repo-name>

keywords:

---

{{site.data.keyword.attribute-definition-list}}

# Requirements
{: #requirements}

The following represents a baseline set of requirements are applicable to most clients and critical to a successful `<Pattern Name>` deployment.

<!--example Requirements Table – update with requirements for pattern-->

| Aspect | Requirements | 
| -------------- | -------------- |
| Compute | Provide properly isolated compute resources with adequate compute capacity for the applications. |
| Storage | Provide storage that meets the application and database performance requirements. |
| Networking | Deploy workloads in isolated environment and enforce information flow policies.  \n Provide secure, encrypted connectivity to the cloud’s private network for management purposes.  \n Distribute incoming application requests across available compute resources.  \n Provide public and private DNS resolution to support use of hostnames instead of IP addresses. |
| Security | Ensure all operator actions are executed securely through a bastion host.  \n Protect the boundaries of the application against denial-of-service and application-layer attacks.  \n Encrypt all application data in transit and at rest to protect from unauthorized disclosure.  \n Encrypt all backup data to protect from unauthorized disclosure.  \n Encrypt all security data (operational and audit logs) to protect from unauthorized disclosure.  \n Encrypt all data using customer managed keys to meet regulatory compliance requirements for additional security and customer control.  \n Protect secrets through their entire lifecycle and secure them using access control measures. |
| Resiliency | Support application availability targets and business continuity policies.  \n Provide highly available compute, storage, network, and other cloud services to handle application load and performance requirements.  \n Backup application data to enable recovery in the event of unplanned outages.  \n Provide highly available storage for security data (logs) and backup data.|
| Service Management | Monitor system and application health metrics and logs to detect issues that might impact the availability of the application.  \n Generate alerts/notifications about issues that might impact the availability of applications to trigger appropriate responses to minimize down time.  \n Monitor audit logs to track changes and detect potential security problems.  \n Provide a mechanism to identify and send notifications about issues found in audit logs. |
{: caption="Table 1. Pattern requirements" caption-side="bottom"}

