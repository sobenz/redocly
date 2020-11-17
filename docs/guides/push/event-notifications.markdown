---
layout: default
title: Event Notifications
permalink: /docs/guides/push/push-events
parent: Push
grand_parent: Guides
---

# Event Notifications
## n_invite
Response to request for Glympse invite.
```js
{ 
  "type": "n_invite",
  "code": "${invite_code}"
}
```

| Name	          | Description	          | Type     |
|-----------------|-----------------------|----------|
|**invite_code**	|Request invite code	|string|

## n_viewer
Response to active Glympse view.

## n_data_value
Response to user message.
```js
{
  "type": "n_data_value",
  "id": "${user_id}",
  "data": {
    "t": ${timestamp},
    "pid": ${partnerId},
    "n": "${name}",
    "v": "${value}"
  }
}
```

| Name	          | Description	          | Type     |
|-----------------|-----------------------|----------|
|**user_id**	|Glympse user id of the message sender	|string|
|**timestamp**	|Epoch time (ms) timestamp when message was sent	|int64|
|**partnerid**	|Identifier of partner organization that sender belongs to	|int64|
|**name**	|Name of sending user	|string|
|**value**	|Message contents	|object|

## n_refresh
Response to user, history, linked_accounts update.
```js
{
  "type": "n_refresh",
  "flags": "${flags}"
}
```

| Name	          | Description	          | Type     |
|-----------------|-----------------------|----------|
|**flags**	|One, some or all of the following values comma separated: user,history,linked_accounts	|string|

## n_echo
Response to echo message that Glympse received a PUSH.
```js
{
  "type": "n_echo",
  "data": "${data}"
}
```

| Name	          | Description	          | Type     |
|-----------------|-----------------------|----------|
|**data**	|Base64 encoded checksum	|string|
## n_sync
Response to change in user favorites.
```js
{
  "type": "n_sync",
  "favorites": "${version}"
}
```

| Name	          | Description	          | Type     |
|-----------------|-----------------------|----------|
|**version**	|Base64 encoded favorites version. See Favorites blob	|string|
## n_cards_feed
Response to a change in the list of cards.
```js
{
  "type": "n_cards_feed",
  "body": {
    "type": "${card_feed_event_type}",
    "card_id": "${card_id}                                                    
  }
}
```

| Name	          | Description	          | Type     |
|-----------------|-----------------------|----------|
|**card_feed_event_type**	|Event Type for notification. See Card Feed Event Type	|string|
|**card_id**	|Optional Id of card which was updated. Some events do not apply to a card	|string|
### Card Feed Event Type

| Name	          | Description	          |
|-----------------|-----------------------|
|**card_joined**	|This user joined a card with a given id.|
|**card_left**	|This user left a card with a given id.|
## n_card
Response to change in a card.
```js
{
  "type": "n_card",
  "body": {
    "type": "${card_event_type}",
    "kind": "${card_event_kind}",
    "card_id": "${card_id}",
    "member_id": "${member_id}",
    "component": "card|members|metadata|custom"
  }
}
```

| Name	          | Description	          | Type     |
|-----------------|-----------------------|----------|
|**card_event_type**	|Event Type for notification. See Card Event Type	|string|
|**card_event_kind**	|Either native or custom	|string|
|**card_id**	|ID of card which was updated.	|string|
|**member_id**	|ID of the member affected by the event.	|string|
|**component**	|Card component which was updated. See Card Component type table	|string|
### Card Event Type
| Name	          | Description	          |
|-----------------|-----------------------|
|**card_modifed**	|The card was modified such as name change, not in relation to members or metadata.|
|**member_added**	|A member was added to the card.|
|**member_removed**	|A member was removed from the card.|
|**member_modified**	|A member within the card changed, not related to sharing, requests or metadata.|
|**member_state_updated**	|Member state was changed.|
|**member_started_sharing**	|A member within the card started sharing location.|
|**member_stopped_sharing**	|A member within the card stopped sharing location.|
|**member_started_requesting**	|A member within the card started requesting location.|
|**member_stopped_requesting**	|A member within the card stopped requesting location.|
|**member_declined_request**	|A member within the card declined a sharing request.|
|**member_metadata_updated**	|A members metadata was updated.|
|**invite_added**	|An invite was added to the card.|
|**invite_removed**	|An invite was removed from the card.|
### Component
| Name	          | Description	          |
|-----------------|-----------------------|
|**card**	|The card information was updated, not related to members or metadata.|
|**members**	|The card members were updated.|
|**metadata**	|The card metadata was updated.|
|**invites**	|The card invites were updated.|
|**custom**	|A custom card event occurred.|