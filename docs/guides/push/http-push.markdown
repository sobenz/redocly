---
layout: default
title: HTTP Push
permalink: /docs/guides/push/http-push
parent: Push
grand_parent: Guides
---

# HTTP Callback Event Notification
In addition to device-specific Push Notification channels, Glympse supports generic HTTP Callback event notifications. Partners must contact Glympse to enable HTTP Callback notifications for their account.

## Events
Partners can receive all [Glympse Events](/docs/guides/push/push-events) via the HTTP Callback mechanism.

## Setup
Contact Glympse to setup an HTTP callback notification channel for your account. The following information is required to setup a callback notification channel:
 * `url`: internet-facing callback endpoint URL (must be HTTPS)
 * `max_retries`: max callback retry count
 * `initial_backoff`: wait time before first retry
 * `backoff_multiplier`: multiplier applied to `initial_backoff` on each subsequent retry
## Device Registration
Once you have your HTTP Callback endpoint registered with Glympse, User devices can register for HTTP Push notifications using the standard Device Registration API with parameters:
 * `provider`: http
 * `auth_id`: {partner identifier}
## Callback
### Format
`POST {Partner endpoint URL provided to Glympse}`
```js
{
  "auth_id": AUTH_ID,
  "payload": EVENT_PAYLOAD
}
```
 * `AUTH_ID`: `auth_id` device provided during device registration
 * `EVENT_PAYLOAD`: [Glympse Events](/docs/guides/push/push-events) JSON data
### Authentication
Callback Requests are sent over SSL with no Glympse-enforced authentication mechanism. Partners should consider using device's `auth_id` (provided during device registration and propagated by Glympse to the Callback endpoint) for implementing desired security model.