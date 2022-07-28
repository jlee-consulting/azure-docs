---
title: Azure Spring Apps geo-disaster recovery | Microsoft Docs
description: Learn how to protect your Spring application from regional outages
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: karler
ms.custom: devx-track-javamicro, event-tier1-build-2022
---

# Azure Spring Apps disaster recovery

> [!NOTE]
> Azure Spring Apps is the new name for the Azure Spring Cloud service. Although the service has a new name, you'll see the old name in some places for a while as we work to update assets such as screenshots, videos, and diagrams.

**This article applies to:** ✔️ Java ✔️ C#

**This article applies to:** ✔️ Basic/Standard tier ✔️ Enterprise tier

This article explains some strategies you can use to protect your applications in Azure Spring Apps from experiencing downtime. Any region or data center may experience downtime caused by regional disasters, but careful planning can mitigate impact on your customers.

## Plan your application deployment

Applications in Azure Spring Apps run in a specific region. Azure operates in multiple geographies around the world. An Azure geography is a defined area of the world that contains at least one Azure Region. An Azure region is an area within a geography, containing one or more data centers. Each Azure region is paired with another region within the same geography, together making a regional pair. Azure serializes platform updates (planned maintenance) across regional pairs, ensuring that only one region in each pair is updated at a time. In the event of an outage affecting multiple regions, at least one region in each pair will be prioritized for recovery.

Ensuring high availability and protection from disasters requires that you deploy your Spring applications to multiple regions. Azure provides a list of [paired regions](../availability-zones/cross-region-replication-azure.md) so that you can plan your Spring app deployments to regional pairs. We recommend that you consider three key factors when designing your architecture: region availability, Azure paired regions, and service availability.

* Region availability: Choose a geographic area close to your users to minimize network lag and transmission time.
* Azure paired regions: Choose paired regions within your chosen geographic area to ensure coordinated platform updates and prioritized recovery efforts if needed.
* Service availability: Decide whether your paired regions should run hot/hot, hot/warm, or hot/cold.

## Use Azure Traffic Manager to route traffic

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) provides DNS-based traffic load-balancing and can distribute network traffic across multiple regions. Use Azure Traffic Manager to direct customers to the closest Azure Spring Apps service instance to them. For best performance and redundancy, direct all application traffic through Azure Traffic Manager before sending it to your Azure Spring Apps service.

If you have applications in Azure Spring Apps running in multiple regions, use Azure Traffic Manager to control the flow of traffic to your applications in each region. Define an Azure Traffic Manager endpoint for each service using the service IP. Customers should connect to an Azure Traffic Manager DNS name pointing to the Azure Spring Apps service. Azure Traffic Manager load balances traffic across the defined endpoints. If a disaster strikes a data center, Azure Traffic Manager will direct traffic from that region to its pair, ensuring service continuity.

## Create Azure Traffic Manager for Azure Spring Apps

1. Create Azure Spring Apps in two different regions.
You will need two service instances of Azure Spring Apps deployed in two different regions (East US and West Europe). Launch an existing application in Azure Spring Apps using the Azure portal to create two service instances. Each will serve as primary and fail-over endpoint for Traffic.

**Two service instances info:**

| Service Name | Location | Application |
|--|--|--|
| service-sample-a | East US | gateway / auth-service / account-service |
| service-sample-b | West Europe | gateway / auth-service / account-service |

2. Set up Custom Domain for Service
Follow [Custom Domain Document](./tutorial-custom-domain.md) to set up custom domain for these two existing service instances. After successful set up, both service instances will bind to custom domain: bcdr-test.contoso.com

3. Create a traffic manager and two endpoints: [Create a Traffic Manager profile using the Azure portal](../traffic-manager/quickstart-create-traffic-manager-profile.md).

Here is the traffic manager profile:
* Traffic Manager DNS Name: `http://asa-bcdr.trafficmanager.net`
* Endpoint Profiles:

| Profile | Type | Target | Priority | Custom Header Settings |
|--|--|--|--|--|
| Endpoint A Profile | External Endpoint | service-sample-a.azuremicroservices.io | 1 | host: bcdr-test.contoso.com |
| Endpoint B Profile | External Endpoint | service-sample-b.azuremicroservices.io | 2 | host: bcdr-test.contoso.com |

4. Create a CNAME record in DNS Zone: bcdr-test.contoso.com CNAME asa-bcdr.trafficmanager.net.

5. Now, the environment is completely set up. Customers should be able to access the app via: bcdr-test.contoso.com

## Next steps

* [Quickstart: Deploy your first Spring Boot app in Azure Spring Apps](./quickstart.md)
