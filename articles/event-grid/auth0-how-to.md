---
title: How to send events from Auth0 to Azure using Azure Event Grid
description: How to end events from Auth0 to Azure services with Azure Event Grid.
ms.topic: conceptual
ms.date: 03/29/2022
---

# Integrate Azure Event Grid with Auth0
This article describes how to connect your Auth0 and Azure accounts by creating an Event Grid partner topic.

> [!NOTE]
> See the [Auth0 event type codes](https://auth0.com/docs/logs/references/log-event-type-codes) for a full list of the events that Auth0 supports

## Send events from Auth0 to Azure Event Grid
To send Auth0 events to Azure:

1. [Register the Event Grid resource provider](subscribe-to-partner-events.md#register-the-event-grid-resource-provider) with your Azure subscription.
2. [Authorize Auth0](subscribe-to-partner-events.md#authorize-partner-to-create-a-partner-topic) to create a partner topic in your resource group.
3. Request Auth0 to enable events flow to a partner topic by [setting up an Auth0 partner topic](#set-up-an-auth0-partner-topic) in the Auth0 Dashboard.
4. [Activate partner topic](subscribe-to-partner-events.md#activate-a-partner-topic) so that your events start flowing to your partner topic.
5. [Subscribe to events](subscribe-to-partner-events.md#subscribe-to-events).

This article provides steps for doing the task #3 from the above list. All other tasks are documented in the [Subscribe to partner events](subscribe-to-partner-events.md) article. 

## Set up an Auth0 partner topic
Part of the integration process is to set up Auth0 for use as an event source by using the [Auth0 Dashboard](https://manage.auth0.com/).

1. Log in to the [Auth0 Dashboard](https://manage.auth0.com/).
1. Navigate to **Monitoring** > **Streams**.
1. Click **+ Create Log Stream**.
1. Select **Azure Event Grid** and enter a unique name for your new stream.
1. For **Subscription ID**, enter your Azure subscription ID. 
1. For **Azure Region**, select the Azure region in which the resource group exists. 
1. For **Resource Group**, enter the name of the resource group.
1. For **Filter by Event Category**, select **All** or filter for specific types of events.
1. Select **Use a specific day and time to start the stream from** option if you want the streaming to start on a specific day and time. 
1. Click **Save**.

You should see the partner topic in the resource group you specified. [Activate the partner topic](subscribe-to-partner-events.md#activate-a-partner-topic) so that your events start flowing to your partner topic. Then, [subscribe to events](subscribe-to-partner-events.md#subscribe-to-events).
 

## Verify the integration
To verify that the integration is working as expected:

1. Log in to the Auth0 Dashboard.
1. Navigate to **Logs** > **Streams**.
1. Click on your **Event Grid stream**.
1. Once on the stream, click on the **Health** tab. The stream should be active and as long as you don't see any errors, the stream is working.

Try [invoking any of the Auth0 actions that trigger an event to be published](https://auth0.com/docs/logs/references/log-event-type-codes) to see events flow.

## Delivery attempts and retries
Auth0 events are delivered to Azure via a streaming mechanism. Each event is sent as it's triggered in Auth0. If Event Grid is unable to receive the event, Auth0 will retry up to three times to deliver the event. Otherwise, Auth0 will log the failure to deliver in its system.

## Next steps

- [Auth0 Partner Topic](auth0-overview.md)
- [Partner topics overview](partner-events-overview.md)
- [Become an Event Grid partner](onboard-partner.md)