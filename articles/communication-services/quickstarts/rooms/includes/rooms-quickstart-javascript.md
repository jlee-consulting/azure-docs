---
title: include file
description: include file
services: azure-communication-services
author: radubulboaca
manager: mariusu

ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 01/26/2022
ms.topic: include
ms.custom: include file
ms.author: radubulboaca
---

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- An active Communication Services resource and connection string. [Create a Communication Services resource](../../create-communication-resource.md).
- Two or more Communication User Identities. [Create and manage access tokens](../../access-tokens.md?pivots=programming-language-csharp) or [Quick-create identities for testing](../../identity/quick-create-identity.md).
- The latest versions of [Node.js](https://nodejs.org/en/download/) Active LTS and Maintenance LTS versions.

## Setting up

### Create a new web application
In a terminal or console window, create a new folder for your application and navigate to it.

```console
mkdir acs-rooms-quickstart && cd acs-rooms-quickstart
```

Run `npm init` to create a package.json file with default settings.

```console
npm init -y
```

### Install the packages

Use the `npm install` command to install the below Communication Services SDKs for JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-identity --save
npm install @azure/communication-signaling --save
```

### Initialize a room client

Create a new `RoomsClient` object that will be used to create new `rooms` and manage their properties and lifecycle. The connection string of your `Communications Service` will be used to authenticate the request. For more information on connection strings, see [this page](../../create-communication-resource.md#access-your-connection-strings-and-service-endpoints).

```javascript
const connectionString =
    process.env["COMMUNICATION_CONNECTION_STRING"] ||
    "endpoint=https://<resource-name>.communication.azure.com/;<access-key>";

const identityClient = new CommunicationIdentityClient(connectionString);
const user1 = await identityClient.createUserAndToken(["voip"]);
const user2 = await identityClient.createUserAndToken(["voip"]);

// create RoomsClient
const roomsClient = new RoomsClient(connectionString);
```

### Create a room

Create a new `room` with default properties using the code snippet below:

```javascript
// create a room with the request payload
const createRoom = await roomsClient.createRoom(createRoomRequest);
const roomId = createRoom.id;
```

Since `rooms` are server-side entities, you may want to keep track of and persist the `roomId` in the storage medium of choice. You can reference the `roomId` to view or update the properties of a `room` object. 

### Get properties of an existing room

Retrieve the details of an existing `room` by referencing the `roomId`:

```javascript
// retrieves the room with corresponding ID
const getRoom = await roomsClient.getRoom(roomId);
console.log(`Retrieved Room with ID ${roomId}`);
```

### Update the lifetime of a room

The lifetime of a `room` can be modified by issuing an update request for the `ValidFrom` and `ValidUntil` parameters.

```javascript
validFrom.setTime(validUntil.getTime());
validUntil.setTime(validFrom.getTime() + 5 * 60 * 1000);

// request payload to update a room
const updateRoomRequest = {
validFrom: validFrom,
validUntil: validUntil,
roomJoinPolicy: "CommunicationServiceUsers",
participants: [
    new RoomParticipant(user1.user, "Consumer"),
    new RoomParticipant(user2.user, "Presenter"),
],
};

// updates the specified room with the request payload
const updateRoom = await roomsClient.updateRoom(roomId, updateRoomRequest);
``` 

### Add new participants 

To add new participants to a `room`, use the `addParticipants` method exposed on the client.

```javascript
  // request payload to add participants
  const addParticipantsRequest = {
    participants: [
      {
        id: user1.user,
        role: "Consumer",
      },
    ],
  };

  // add user2 to the room with the request payload
  const addParticipants = await roomsClient.addParticipants(roomId, addParticipantsRequest);
```

Participants that have been added to a `room` become eligible to join calls.

### Remove participants

To remove a participant from a `room` and revoke their access, use the `removeParticipants` method.

```javascript
  // request payload to delete both users from the room
  const removeParticipantsRequest = {
    participants: [user1.user, user2.user],
  };

  // remove both users from the room with the request payload
  await roomsClient.removeParticipants(roomId, removeParticipantsRequest);
```

### Delete room
If you wish to disband an existing `room`, you may issue an explicit delete request. All `rooms` and their associated resources are automatically deleted at the end of their validity plus a grace period. 

```javascript
// deletes the specified room
await roomsClient.deleteRoom(roomId);
```
