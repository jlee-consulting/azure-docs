---
title: Configure active geo-replication for Enterprise Azure Cache for Redis instances
description: Learn how to replicate your Azure Cache for Redis Enterprise instances across Azure regions.
author: flang-msft
ms.service: cache
ms.topic: conceptual
ms.date: 06/15/2022
ms.author: franlanglois

---
# Configure active geo-replication for Enterprise Azure Cache for Redis instances

In this article, you learn how to configure an active geo-replicated cache using the Azure portal.

Active geo-replication groups up to five instances of Enterprise Azure Cache for Redis into a single cache that spans across Azure regions. All instances act as the local, primary caches. An application decides which instance or instances to use for read and write requests.

> [!NOTE]
> Data transfer between Azure regions is charged at standard [bandwidth rates](https://azure.microsoft.com/pricing/details/bandwidth/).
>

## Create or join an active geo-replication group

1. When creating a new Azure Cache for Redis resource, select the **Advanced** tab. Complete the first part of the form including clustering policy. For more information on choosing **Clustering policy**, see [Clustering Policy](quickstart-create-redis-enterprise.md#clustering-policy).

1. Select **Configure** to set up **Active geo-replication**.

    :::image type="content" source="media/cache-how-to-active-geo-replication/cache-active-geo-replication-configure.png" alt-text="Screenshot of advanced tab of create new Redis cache page.":::

1. Create a new replication group for a first cache instance. Or, select an existing one from the list.

    :::image type="content" source="media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png" alt-text="Screenshot showing replication groups.":::

1. Select **Configure** to finish.

1. Wait for the first cache to be created successfully. When complete, you see **Configured** set for **Active geo-replication**. Repeat the above steps for each cache instance in the geo-replication group.

     :::image type="content" source="media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png" alt-text="Screenshot showing active geo-replication is configured.":::

## Remove from an active geo-replication group

To remove a cache instance from an active geo-replication group, you just delete the instance. The remaining instances will reconfigure themselves automatically.

## Force-unlink if there's a region outage

In case one of the caches in your replication group is unavailable due to region outage, you can forcefully remove the unavailable cache from the replication group.

You should remove the unavailable cache because the remaining caches in the replication group start storing the metadata that hasn’t been shared to the unavailable cache. When this happens, the available caches in your replication group might run out of memory.

1. Go to Azure portal and select one of the caches in the replication group that is still available.

1. Select to **Active geo-replication** in the Resource menu on the left to see the settings in the working pane.

    :::image type="content" source="media/cache-how-to-active-geo-replication/cache-active-geo-replication-group.png" alt-text="Screenshot of active geo-replication group.":::

1. Select the cache that you need to force-unlink by checking the box.

1. Select **Force unlink** and then **OK** to confirm.

    :::image type="content" source="media/cache-how-to-active-geo-replication/cache-cache-active-geo-replication-unlink.png" alt-text="Screenshot of unlinking in active geo-replication.":::

1. Once the affected region's availability is restored, you need to delete the affected cache and recreate it to add it back to your replication group.

## Next steps

Learn more about Azure Cache for Redis features.

* [Azure Cache for Redis service tiers](cache-overview.md#service-tiers)
* [High availability for Azure Cache for Redis](cache-high-availability.md)
