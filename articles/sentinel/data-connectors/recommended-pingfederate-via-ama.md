---
title: "[Recommended] PingFederate via AMA connector for Microsoft Sentinel"
description: "Learn how to install the connector [Recommended] PingFederate via AMA to connect your data source to Microsoft Sentinel."
author: cwatson-cat
ms.topic: how-to
ms.date: 10/23/2023
ms.service: microsoft-sentinel
ms.author: cwatson
ms.collection: sentinel-data-connector
---

# [Recommended] PingFederate via AMA connector for Microsoft Sentinel

The [PingFederate](https://www.pingidentity.com/en/software/pingfederate.html) data connector provides the capability to ingest [PingFederate events](https://docs.pingidentity.com/bundle/pingfederate-102/page/lly1564002980532.html) into Microsoft Sentinel. Refer to [PingFederate documentation](https://docs.pingidentity.com/bundle/pingfederate-102/page/tle1564002955874.html) for more information.

## Connector attributes

| Connector attribute | Description |
| --- | --- |
| **Log Analytics table(s)** | CommonSecurityLog (PingFederate)<br/> |
| **Data collection rules support** |[Azure Monitor Agent DCR](/azure/azure-monitor/agents/data-collection-rule-azure-monitor-agent)|
| **Supported by** | [Microsoft Corporation](https://support.microsoft.com) |

## Query samples

**Top 10 Devices**
   ```kusto
PingFederateEvent
 
   | summarize count() by DvcHostname
 
   | top 10 by count_
   ```



## Prerequisites

To integrate with [Recommended] PingFederate via AMA make sure you have: 

- ****: To collect data from non-Azure VMs, they must have Azure Arc installed and enabled. [Learn more](/azure/azure-monitor/agents/azure-monitor-agent-install?tabs=ARMAgentPowerShell,PowerShellWindows,PowerShellWindowsArc,CLIWindows,CLIWindowsArc)
- ****: Common Event Format (CEF) via AMA and Syslog via AMA data connectors must be installed [Learn more](/azure/sentinel/connect-cef-ama#open-the-connector-page-and-create-the-dcr)


## Vendor installation instructions


> [!NOTE]
   >  This data connector depends on a parser based on a Kusto Function to work as expected [**PingFederateEvent**](https://aka.ms/sentinel-PingFederate-parser) which is deployed with the Microsoft Sentinel Solution.


2. Secure your machine 

Make sure to configure the machine's security according to your organization's security policy


[Learn more >](https://aka.ms/SecureCEF)



## Next steps

For more information, go to the [related solution](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azuresentinel.azure-sentinel-solution-pingfederate?tab=Overview) in the Azure Marketplace.
