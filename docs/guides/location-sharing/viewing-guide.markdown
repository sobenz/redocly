---
layout: default
title: Viewing Guide
permalink: /docs/guides/location-sharing/viewing-guide
parent: Location Sharing
grand_parent: Guides
---

# Viewing Guide
This guide describes basic flow involved in viewing someone's real-time location on Glympse platform.

## Decoding a Ticket
The flow usually begins with viewer application receiving Glympse invite code. Glympse invite code can be represented as a value of its own (6 or 8 character code with dash in the middle) or can be shared as a part of Glympse URL (ie "[https://glympse.com/bot-002](https://glympse.com/bot-002)").

## Invite Preview
The first step of the flow involves previewing the invite. This steps confirms that the invite at hand is truly a ticket invite (vs invite that corresponds to a Glympse request that needs to be dealt with differently).

The following endpoint call retrieves invite type and recent values of all ticket properties (in case of invite of ticket type).
```js
GET /v2/invites/:code/properties
```
See GET invites/:code/properties endpoint for details.

## Initial Snapshot
Make this API call to retrieve initial snapshot of ticket data (including data properties and most recent location points).
```js
GET /v2/invites/:code
  ?next=0
```
See GET invites/:code endpoint for details.

## Polling Updates
Upon retrieving initial snapshot of ticket data, viewer application switches to polling mode. Viewer application is free to choose polling frequency that suits best to the requirements of its use case. 5 second refresh interval is recommended for most applications.

The following API call should be made to retrieve ticket updates throughout the entire duration of viewing session. Note that next param contains the value retrieved in response to previous invocation of the endpoint.
```js
GET /v2/invites/:code
  ?next=${next}
```

**Note**: In most cases this endpoint call responds with the list of location points and data points appended since the call has been made before. However, Glympse Core API reserves the right to respond with the latest snapshot of ticket data (identical to invites/:code?next=0) in case if the gap between calls is significant.

**Important**: It is important that application keeps track of its state and pauses polling when data is no longer presented to the user (application goes to the background, etc.).