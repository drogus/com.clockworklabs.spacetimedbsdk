# SpacetimeDB SDK for Unity 3D Engine

## Overview

This repository contains the [Unity](https://unity.com/) SDK for SpacetimeDB. The SDK allows to interact with the database server and is prepared to work with code generated from a SpacetimeDB backend code.

## Documentation

The Unity SDK uses the same code as the C# SDK. You can find the documentation for the C# SDK in the [C# SDK Reference](https://spacetimedb.com/docs/client-languages/csharp/csharp-sdk-reference)

There is also a comprehensive Unity tutorial available in the [Unity Tutorial](https://spacetimedb.com/docs/unity-tutorial/unity-tutorial-part-1)

## Installation

The SDK is available as a [Unity Package](https://docs.unity3d.com/Manual/Packages.html). To install it, follow these steps:

1. Open the package manager window in Unity.
2. Click the plus button in the top left corner and select "Add package from git URL".
3. Paste the following URL: `https://github.com/clockworklabs/com.clockworklabs.spacetimedbsdk.git`

## Usage

### NetworkManager

The Unity SDK for SpacetimeDB requires that there is a `NetworkManager` component attached ot a GameObject in the scene. The NetworkManager component is responsible for connecting to SpacetimeDB and managing the connection. The NetworkManager component is a singleton and there can only be one instance of it in the scene.

### Connecting to SpacetimeDB

To connect to SpacetimeDB, you need to call the `Connect` method on the `SpacetimeDBClient` class. The `Connect` method takes the following parameters:

- `token`: The authentication token to use to connect to SpacetimeDB. This token is generated by the backend code and is used to authenticate the client.
- `hostName`: The hostname of the SpacetimeDB server. This is the same hostname that you use to connect to the SpacetimeDB web interface.
- `moduleAddress`: The address of the module to connect to. This is the same address that you use to connect to the SpacetimeDB web interface.
- `sslEnabled`: Whether to use SSL to connect to SpacetimeDB. This is the same value that you use to connect to the SpacetimeDB web interface.

Example:

```csharp
using SpacetimeDB;

SpacetimeDBClient.instance.Connect(TOKEN, HOST, DBNAME, SSL_ENABLED);
```

### AuthToken

The `AuthToken` class is an optional helper class that can be used to store the local client's authentication token locally in the Unity PlayerPrefs.

Example:

```csharp
using SpacetimeDB;

// called when we receive the client identity from SpacetimeDB
SpacetimeDBClient.instance.onIdentityReceived += (token, identity, address) => {
    AuthToken.SaveToken(token);
    local_identity = identity;
};

SpacetimeDBClient.instance.Connect(AuthToken.Token, hostName, moduleAddress, sslEnabled);
```

### Subscribing to tables

To subscribe to a table, you need to call the `Subscribe` method on the `SpacetimeDBClient` class. The `Subscribe` method takes a list of queries as a parameter. The queries are the same queries that you use to subscribe to tables in the SpacetimeDB web interface.

### Listening to events

To listen to events, you need to register callbacks on the `SpacetimeDBClient` class. The following callbacks are available:

- `onConnect`: Called when the client connects to SpacetimeDB.
- `onConnectError`: Called when the client fails to connect to SpacetimeDB.
- `onDisconnect`: Called when the client disconnects from SpacetimeDB.
- `onIdentityReceived`: Called when the client receives its identity from SpacetimeDB.
- `onSubscriptionApplied`: Called when the client receives the initial data from SpacetimeDB after subscribing to tables.

You can register for row update events on a table. To do this, you need to register callbacks on the table class. The following callbacks are available:

- `OnInsert`: Called when a row is inserted into the table.
- `OnUpdate`: Called when a row is updated in the table.
- `OnBeforeDelete`: Called before a row is deleted from the table.
- `OnDelete`: Called when a row is deleted from the table.
- `OnRowUpdate`: Called when a row is inserted, updated, or deleted from the table.

Example:

```csharp
using SpacetimeDB.Types;

PlayerComponent.OnInsert += PlayerComponent_OnInsert;
PlayerComponent.OnUpdate += PlayerComponent_OnUpdate;
PlayerComponent.OnDelete += PlayerComponent_OnDelete;
PlayerComponent.OnBeforeDelete += PlayerComponent_OnBeforeDelete;
PlayerComponent.OnRowUpdate += PlayerComponent_OnRowUpdate;
```

You can register for reducer call updates as well.

- `OnREDUCEREvent`: Called when a reducer call is received from SpacetimeDB. (If a) you are subscribed to the table that the reducer modifies or b) You called the reducer and it failed)

Example:

```csharp
using SpacetimeDB.Types;

Reducer.OnMovePlayerEvent += Reducer_OnMovePlayerEvent;
```
 
### Accessing the client cache

The client cache is a local cache of the data that the client has received from SpacetimeDB. The client cache is automatically updated when the client receives updates from SpacetimeDB. 

When you run the CLI generate command, SpacetimeDB will automatically generate a class for each table in your database. These classes are generated in the `SpacetimeDB.Types` namespace. Each class contains a set of static methods that allow you to query the client cache. The following methods are available:

- `FilterByCOLUMN`: Filters the table by the specified column value.
- `Iter`: Returns an iterator over the table.
- `Count`: Returns the number of rows in the table.

### Calling Reducers

To call a reducer, you need to call the autogenerated method on the `Reducer` class. The autogenerated method takes the reducer arguments as parameters. The reducer arguments are the same arguments that are expected in your server module.

Example:

```csharp
using SpacetimeDB.Types;

Reducer.MovePlayer(new StdbVector2(0.0f, 0.0f), new StdbVector2(1.0f, 1.0f));
```


