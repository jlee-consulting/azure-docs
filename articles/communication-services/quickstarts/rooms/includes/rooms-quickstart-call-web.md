---
title: include file
description: include file
services: azure-communication-services
author: radubulboaca
manager: mariusu

ms.service: azure-communication-services
ms.date: 07/13/2022
ms.topic: include
ms.custom: include file
ms.author: radubulboaca
---

## Join a room call

To join a room call, set up your web application using the [Add video calling to your client app](../../voice-video-calling/get-started-with-video-calling.md?pivots=platform-web) guide. Alternatively, you can download the video calling quickstart on [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling).

Once you have an initialized and authenticated `callAgent`, you may specify a context object with the `roomId` property as the `room` identifier. To join the call, use the `join` method and pass the context instance.

```js

const context = { roomId: '<RoomId>' }

const call = callAgent.join(context);

```
