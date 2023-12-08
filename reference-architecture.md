---

copyright:
  years: 2023
lastupdated: "2023-11-28"

subcollection: <repo-name>

keywords:

# The release that the reference architecture describes
version: 1.0

# Use if the reference architecture has deployable code.
# Value is the URL to land the user in the IBM Cloud catalog details page for the deployable architecture.
# See https://test.cloud.ibm.com/docs/get-coding?topic=get-coding-deploy-button
deployment-url: url

# use-case from 'code' column in
# https://github.ibm.com/digital/taxonomy/blob/main/topics/topics_flat_list.csv
use-case:

# industry from 'code' column in
# https://github.ibm.com/digital/taxonomy/blob/main/industries/industries_flat_list.csv
industry:

# compliance from 'code' column in
# https://github.ibm.com/digital/taxonomy/blob/main/compliance_entities/compliance_entities_flat_list.csv
compliance:

content-type: reference-architecture

---

{{site.data.keyword.attribute-definition-list}}

<!--
The following line inserts all the attribute definitions. Don't delete.
-->
{{site.data.keyword.attribute-definition-list}}

<!--
Don't include "reference architecture" in the following title.
Specify a title based on a use case. If the architecture has a module
or tile in the IBM Cloud catalog, match the title to the catalog. See
https://test.cloud.ibm.com/docs/solution-as-code?topic=solution-as-code-naming-guidance.
-->

# Title
{: #title-id}
{: toc-content-type="reference-architecture"}
{: toc-industry="value"}
{: toc-use-case="value"}
{: toc-compliance="value"}
{: toc-version="value"}

<!--
The IDs, such as {: #title-id} are required for publishing this reference architecture in IBM Cloud Docs. Set unique IDs for each heading. Also include
the toc attributes on the H1, repeating the values from the YAML header.

:information_source: **Tip:** For more information about this template, see [Creating reference architectures](https://test.cloud.ibm.com/docs/writing?topic=writing-reference-architectures).

-->


Include a short description, summary, or overview in a single paragraph that describes/summarizes the architecture.

## Architecture diagram
{: #architecture-diagram}

Include the architecture diagram SVG file that was created by using drawio and the IBM2 library. 

![Enter image alt text here.](example-architecture-diagram.svg "Title text that shows on hover here"){: caption="Figure 1. A description that prints on the page" caption-side="bottom"}

## Design requirements
{: #design-requirements}

Customize the design requirement heat map template image and highlight the scope of the architecture. Publishing in IBM Cloud Docs requires a caption to meet accessibility requirements.

![Enter image alt text here.](heatmap.svg "Title text that shows on hover here"){: caption="Figure 1. A description that prints on the page" caption-side="bottom"}

For more information about creating a design requirements heat map image, see [Design requirements heat map](https://test.cloud.ibm.com/docs/architecture-framework?topic=architecture-framework-heat-map).

After the heat map image, include in this section a write up of the typical use case for the architecture. The use case might include the motivation for the architecture composition, business challenge, or target cloud environments.

## Components
{: #components}

Update Table below with solution components unique to this pattern.

| Aspects | Solution Comonents | How the component is used |
| -------------- | -------------- | -------------- |
| Compute | PowerVS | Web, App, and database servers |
| Storage | PowerVS | Database servers shared storage for RAC |
|  | VPC Block Storage | Web app storage if neededt |
| Networking | VPC Virtual Private Network (VPN) | Remote access to manage resources in private network |
|  | Virtual Private Gateway & Virtual Private Endpoint (VPE) | For private network access to Cloud Services, e.g., Key Protect, COS, etc. |
|  | VPC Load Balancers | Application Load Balancing for web servers, app servers, and database servers |
|  | Public Gateway | For web server access to the internet |
| Security | IAM | IBM Cloud Identity & Access Management |
|  | BYO Bastion Host on VPC VSI | Remote access with Privileged Access Management |
|  | Key protect or HPCS | Hardware security module (HSM) and Key Management Service |
|  | Secrets Manager | Certificate and Secrets Management |
| Resiliency | PowerVS | Multiple PowerVS on separate physical servers with VM and Storage anti-affinity policy |
| Service Management | IBM Cloud Monitoring | Apps and operational monitoring |
|  | IBM Log Analysis | Apps and operational logs |
|  | Activity Tracker Event Routing | Audit logs |
| Other  use if there is  additional aspect(s)  Name Aspect | Cell content | Cell content |
{: caption="Table 1. Components" caption-side="bottom"}

