---
title: Data field differences between MMA and AMA 

description: Documents that field lever data changes made in the migration.
author: JeffreyWolford
ms.author: jeffwo
ms.reviewer: guywild
ms.topic: reference
ms.date: 06/21/2024

Customer intent: As an azure administrator, I want to understand which Log Analytics Workspace queries I may need to update after AMA migration.

---

# AMA agent data field differences from MMA
[Azure Monitor Agent (AMA)](./agents-overview.md) replaces the Log Analytics agent, also known as Microsoft Monitor Agent (MMA) and OMS, for Windows and Linux machines, in Azure and non-Azure environments, on-premises and other clouds. The agent introduces a simplified, flexible method of configuring data collection using [Data Collection Rules (DCRs)](../essentials/data-collection-rule-overview.md). The article provides information on the data fields that change when collected by AMA, which is critical information for you to migrate your LAW queries.

Each of the data changes was carefully considered and the rational for each change is provided in the table. If you encounter a data field that isn't in the tables file a support request. Your help keeping the tables current and complete is appreciated.

## Log analytics workspace tables
### W3CIISLog Table for Internet Information Services (IIS)
This table collects log data from the Internet Information Service on Window systems.

|LAW Field | Difference | Reason| Additional Information |
|---|---|---|---|
| sSiteName | Not be populated | depends on customer data collection configuration | The MMA agent could turn on collection by default, but by principle is restricted from making configuration changes in other services.<p>Enable the `Service Name (s-sitename)` field in W3C logging of IIS. See [Select W3C Fields to Log](/iis/manage/provisioning-and-managing-iis/configure-logging-in-iis#select-w3c-fields-to-log).|
| Fileuri | No longer populated | not required for MMA parity | MMA doesn't collect this field. This field was only populated for IIS logs collected from Azure Cloud Services through the Azure Diagnostics Extension.|


## Next steps
- [Azure Monitor Agent migration helper workbook](./azure-monitor-agent-migration-helper-workbook.md)
- [DCR Config Generator](./azure-monitor-agent-migration-data-collection-rule-generator.md)

