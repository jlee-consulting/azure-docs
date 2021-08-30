---
title: Best practices for connection resilience
titleSuffix: Azure Cache for Redis
description: Learn how to make your Azure Cache for Redis connections resilient.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
---

# Connection resilience

## Retry commands

Configure your client connections to retry commands with exponential backoff. For more information, see [retry guidelines](/azure/architecture/best-practices/retry-service-specific#azure-cache-for-redis).

## Test resiliency

Test your system's resiliency to connection breaks using a [Reboot](cache-administration.md#reboot) to simulate a patch. For more information on testing your performance, see [Performance testing](cache-best-practices-performance.md).

## Configure appropriate timeouts

Configure your client library to use *connect timeout* of 10 to 15 seconds and a *command timeout* of 5 seconds. The *connect timeout* is the time your client waits to establish a connection with Redis server. Most client libraries have another timeout configuration for *command timeouts*, which is the time the client waits for a response from Redis server.

Some libraries have the *command timeout* set to 5 seconds by default. Consider setting it higher or lower depending on your scenario and the sizes of the values that are stored in your cache.

If the *command timeout* is too small, the connection can look unstable. However, if the *command timeout* is too large, your application might have to wait for a long time to find out whether the command is going to timeout or not.

Configure your client library to use a *connect timeout* of at least 15 seconds, giving the system sufficient time to connect even under higher CPU conditions. A small *connection timeout* value doesn't guarantee a connection is established in that time frame.

If something goes wrong (high client CPU, high server CPU, and so on), then a short connection timeout value causes the connection attempt to fail. This behavior often makes a bad situation worse. Instead of helping, shorter timeouts aggravate the problem by forcing the system to restart the process of trying to reconnect, which can lead to a *connect -> fail -> retry* loop.

We generally recommend that you leave your *connection timeout* at 15 seconds or higher. It's better to let your connection attempt to succeed after 15 or 20 seconds than to have it fail quickly only to retry. Such a retry loop can cause your outage to last longer than if you let the system just take longer initially.

## Avoid client connection spikes

Avoid creating many connections at the same time when reconnecting after a connection loss. Similar to the way that [short connect timeouts](#configure-appropriate-timeouts) can result in longer outages, starting many reconnect attempts at the same time can also increase server load and extend how long it takes for all clients to reconnect successfully.

If you're reconnecting many client instances, consider staggering the new connections to avoid a steep spike in the number of connected clients.

> [!NOTE]
> When you use the `StackExchange.Redis` client library, set `abortConnect` to `false` in your connection string.  We recommend letting the `ConnectionMultiplexer` handle reconnection. For more information, see [StackExchange.Redis best practices](/azure/azure-cache-for-redis/cache-planning-faq#stackexchangeredis-best-practices).

## Avoid leftover connections

Caches have limits on the number of client connections per cache tier. Ensure that when your client application recreates connections that it closes and removes the old connections.

## Advance maintenance notification

Use notifications to learn of upcoming maintenance. For more information, see [Can I be notified in advance of a planned maintenance?](cache-failover.md#can-i-be-notified-in-advance-of-a-planned-maintenance).

## Schedule maintenance window

Adjust your cache settings to accommodate maintenance. For more information about creating a maintenance window to reduce any negative effects to your cache, see [Schedule updates](cache-administration.md#schedule-updates).

## More design patterns for resilience

Apply design patterns for resiliency. For more information, see [How do I make my application resilient?](cache-failover.md#how-do-i-make-my-application-resilient).

## Idle timeout

Azure Cache for Redis currently has a 10-minute idle timeout for connections, so the idle timeout setting in your client application should be less than 10 minutes. Most common client libraries have a configuration setting that allows client libraries to send Redis `PING` commands to a Redis server automatically and periodically. However, when using client libraries without this type of setting, customer applications themselves are responsible for keeping the connection alive.
