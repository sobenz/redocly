---
layout: default
title: Sharing Guide
permalink: /docs/guides/location-sharing/sharing-guide
parent: Location Sharing
grand_parent: Guides
---

# Sharing Guide
This guide describes basic flow involved in sharing real-time location on Glympse platform.

## Authentication
Most applications authenticate users via the mechanism of account linking, allowing the same user account to be accessed from multiple devices. See account/login endpoint for details.

In the most common case the following API call is made to exchange user credentials for access token. Caller Application is responsible for caching the token for its duration. API key required for making this call can be retrieved from My Applications page.
```js
POST /v2/account/login
  ?api_key=${api_key}
  &id=${username}
  &password=${password}
  &device=${device}
```

## Creating a Ticket
Creating a ticket is a two phase process.

The first phase involves creating ticket object itself. Ticket object is a container for real-time location data and additional properties supplied during sharing.

```js
POST /v2/users/self/create_ticket
  ?duration=${duration}
See POST users/self/create_ticket endpoint for details.
```

Ticket is a private object which is accessible exclusively to sharing party. Ticket invite needs to be created in order for ticket data to be exposed to potential viewers. Invite creation involves specifying delivery details (including transport type and address) and results into unique invite code to be created, returned to caller and optionally delivered to recipient.

```js
POST /v2/tickets/:id/create_invite
  ?type=${transport_type}
  &send=${delivery_party}
  &name={$recipient_name}
  &address=${recipient_address}
 ```
 
See POST tickets/:id/create_invite endpoint for details.

## Uploading Initial Properties
Ticket object can be populated with additional metadata upon creation.

```js
POST /v2/tickets/:id/append_data
[
  {
    "t": ${property_timestamp},
    "pid": 0,
    "n": "${property_name}",
    "v": ${property_value}
  }, ...
]
```
In this example ticket message is provided:

```js
POST /v2/tickets/10001/append_data
[
  {
    "t": 1469998688756,
    "pid": 0,
    "n": "message",
    "v": "Heading home!"
  }
]
```

See POST tickets/:id/append_data endpoint for details.

## Uploading Location Data
Sharing application is responsible for uploading location data on regular basis for the entire duration of a ticket. Location points can be uploaded using the following endpoint call:
```js
POST /v2/tickets/:id/append_location
[
  [
    $timestamp,
    $latitude,
    $longitude,
    $speed,
    $heading,
    $altitude,
    $horizontal_accuracy,
    $vertical_accuracy
  ], ...
]
```
See POST tickets/:id/append_location endpoint for details.

## Retrieving Viewing Stats
Glympse-enabled application that is actively broadcasting user's location might be interested in providing user with real-time feedback on how active tickets are being watched. The following metrics can be exposed to the user:

 * total number of distinct viewers;
 * a number of current viewers;
 * the list of viewers;
 * invite delivery status.
All these metrics are provided on per invite basis.

These metrics can be retrieved from Glympse

 * via a dedicated API call GET users/self/invites;
 * or directly from POST tickets/:id/append_location (the call application makes on regular basis to upload locaiton point anyways).
### Initial Snapshot
Initial state of all active invites can be retrieved passing `since=0` when retrieving the list of invites or uploading the next batch of location points.

GET /v2/users/self/invites
  ?since=0
This results into an array of invites and **last_refresh** value to be included in the response.
```js
{
  "result": "ok",
  "response": {
    "type": "invites",
    "items": [ ... ],
    "last_refresh": ${last_refresh}
  }
}
```
See GET users/self/invites for more details on response format.

### Polling Updates
Application then passes previously retrieved **last_refresh** value in order to receive just changes that has occurred in between invocations.
```js
GET /v2/users/self/invites
  ?since=${last_refresh}
```
**Note** | The endpoint `GET users/self/invites` is interchangeable with `POST tickets/:id/append_location` in this context.