---
title: Update Microsoft Sentinel's SAP data connector agent | Microsoft Docs
description: This article shows you how to update an already existing SAP data connector to its latest version.
author: MSFTandrelom
ms.author: andrelom
ms.topic: reference
ms.date: 03/02/2022
---

# Update Microsoft Sentinel's SAP data connector agent

[!INCLUDE [Banner for top of topics](../includes/banner.md)]

This article shows you how to update an already existing SAP data connector to its latest version.

> [!IMPORTANT]
> The Microsoft Sentinel Threat Monitoring for SAP solution is currently in PREVIEW. The [Azure Preview Supplemental Terms](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) include additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

If you have a Docker container already running with an earlier version of the SAP data connector, run the SAP data connector update script to get the latest features available.

## Update SAP data connector agent

Make sure that you have the most recent versions of the relevant deployment scripts from the Microsoft Sentinel GitHub repository. 

Run:

```azurecli
wget -O sapcon-instance-update.sh https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/Solutions/SAP/sapcon-instance-update.sh && bash ./sapcon-instance-update.sh
```

The SAP data connector Docker container on your machine is updated. 

Be sure to check for any other available updates, such as:

- Relevant SAP change requests, in the [Microsoft Sentinel GitHub repository](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/CR).
- Microsoft Sentinel Threat Monitoring for SAP security content, in the **Microsoft Sentinel Threat Monitoring for SAP** solution.
- Relevant watchlists, in the [Microsoft Sentinel GitHub repository](https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists).

## Next steps

Learn more about the Microsoft Sentinel Threat Monitoring for SAP solutions:

- [Deploy Threat Monitoring for SAP](deployment-overview.md)
- [Prerequisites for deploying Threat Monitoring for SAP](prerequisites-for-deploying-sap-continuous-threat-monitoring.md)
- [Deploy SAP Change Requests (CRs) and configure authorization](preparing-sap.md)
- [Deploy and configure the SAP data connector agent container](deploy-data-connector-agent-container.md)
- [Deploy SAP security content](deploy-sap-security-content.md)
- [Deploy the Microsoft Sentinel Threat Monitoring for SAP data connector with SNC](configure-snc.md)
- [Enable and configure SAP auditing](configure-audit.md)
- [Collect SAP HANA audit logs](collect-sap-hana-audit-logs.md)

Troubleshooting:

- [Troubleshoot your Microsoft Sentinel Threat Monitoring for SAP solution deployment](sap-deploy-troubleshoot.md)
- [Configure SAP Transport Management System](configure-transport.md)

Reference files:

- [Microsoft Sentinel Threat Monitoring for SAP solution data reference](sap-solution-log-reference.md)
- [Microsoft Sentinel Threat Monitoring for SAP solution: security content reference](sap-solution-security-content.md)
- [Kickstart script reference](reference-kickstart.md)
- [Update script reference](reference-update.md)
- [Systemconfig.ini file reference](reference-systemconfig.md)

For more information, see [Microsoft Sentinel solutions](../sentinel-solutions.md).
