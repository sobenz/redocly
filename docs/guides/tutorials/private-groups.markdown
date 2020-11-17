---
layout: default
title: Private Groups Guide
permalink: /docs/guides/tutorials/private-groups-guide
parent: Tutorials
grand_parent: Guides
---
# Private Groups Guide
The *Glympse Private Groups* and related experiences are created using the Cards API. Cards represent collections of members. Card members share locations, requests, invites, and other custom actions as defined by the card developer.

This article shows how to create a card and perform common tasks, like sharing data with the card, following card activity, and managing members.

## Creating Cards and Inviting Members
To create your own Glympse Private Group:
 1. Create the card.
 2. Invite members.
 
Here's how to do that using the REST API:
 1. Use POST cards to create a card.
  Request:
  ```js
 POST cards
 {
   "type_id": "${type_id}",
   "name":"${name}"
 }
 ```
  Response:
  ```js
 {
   "result": "ok",
   "response": { 
     ${card_info} 
   }
 }
 ```
  To learn more about the response object, see card information fields.

 2. Use POST cards/:id/invites to send invites to the people you want to join the card.

    Request:
    ```js
    POST cards/:id/invites
    { 
      "type": "${invite_type}", 
      "address": "${invite_address}",
      "send": "${sender}"
    }
    ```
    Response:
    ```js
   {
     "result": "ok",
     "response": {    
       "type": "card_invite",
       "id": "${invite_id}",
       "card_id": "${card_id}",
       "invite": {
         // ...
         "code": "${invite_code}",
         "url": "${invite_url}",
         "text": "${invite_text}"
       },
       "inviter": {
         "member_id": "${inviter_member_id}",
         "user_id": "${inviter_user_id}"
       },
       "expired_time": ${expired_time}
     }
   }
   ```
   To learn more about the response object, see invite response fields.

   *Note*: Card members must use verified accounts.
   You can invite new people or people with unverified accounts.
   When your invite is viewed with a supported device, the user is automatically given the chance to verify their account.

## Sharing To a Card
To share a ticket with a card:

 1. Create the ticket as normal.
 2. Use tickets/:id/append_data to append the card_id to the ticket.
 3. Use POST cards/:id/ticket to update the card.
  Request:
  ```js
 POST cards/:id/ticket 
 {
   "invite_code": "${invite_code}",
   "ticket_id": "${ticket_id}",
   "reference": ${reference} 
 }
  ```
  Response:
  ```js
 {
   "result": "ok",
   "response": {    
     "created_time": ${share_created_time},
   }
 }
 ```
To learn more, see POST cards/:id/ticket.

## Requesting From a Card
You can also request location from multiple card members; see POST cards/:id/request.

## Following Card Activity
As members interact with card, they generate events.
Use GET cards/:id/activity to follow these events:

  Request:
  ```js
GET cards/:id/activity
  ```
  Response:
  ```js
{
  "result": "ok",
  "response": [
    {
      "id": "${activity_id}",
      "card_id": "${card_id}",
      "type": "${activity_type}",
      "created_time": ${activity_createion_time},
      "last_modified": ${activity_last_modified_time},
      "member_id": "${card_member_id}",
      "user_id": "${user_id}",
      "kind": "${activity_kind}",
      "data": ${activity_data}
    }, ...
  ]
}
  ```

Common activities include:
 * Adding, updating, or removing members
 * Starting or stopping location sharing
 * Creating, removing, or declining a request
To learn more, see activity types.

## Managing Cards and Members
To remove a member from a card, you need the appropriate card_id and the member_id.

 1. Call GET cards to return a list of cards for the current user.

    Request:
    ```js
     GET cards
    ```
    Response:
    ```js
    {
      "result": "ok",
      "response": { 
        ${card_info} 
      }
    }
    ```
    Use the card information fields to identify the target card and the associated card_id value.

 2. Call GET cards/:id to obtain the members associated with the card.

    Request:
    ```js
      GET cards/id?members=true
    ```
    Response:
    ```js
    {
      "result": "ok",
      "response": { 
        ${card_info},
        "members": [
          ${member_info}, 
          ...
        ]
      } 
    }
    ```
    Use the member information fields to identify the target member and the associated member_id value.

 3. Use these values to call DELETE cards/:id/members/:mid:

    Request:
    ```js
      DELETE cards/:id/members/:member_id
    ```
    Response:
    ```js
    {
      "result": "ok",
      "response": {}
    }
    ```
## Additional Card Tasks
The following table lists common tasks associated with cards and describes the relevant REST endpoints:

|Task	                                    |REST endpoint                                 |
|-------------------------------------------|----------------------------------------------|
|Accept card invite	|POST cards/invites/:code/accept|
|Decline request to share location	|DELETE cards/:id/requests/:code|
|Determine invite intent	|GET cards/invites/:code|
|List self requests to join a card	|GET cards/requests|
|Request to join a card	|POST cards/requests|
|Retrieve available card type IDs	|GET cards/types|
|Retrieve details for an invite	|GET cards/:id/invites/:iid|
|Retrieve member information	|GET cards/:id/members/:mid|
|Revoke earlier request to join a card	|DELETE cards/requests/:id|
|Stop requesting location from a card	|DELETE cards/:id/request|
|Stop sharing location to a card	|DELETE cards/:id/ticket|
|Update a member's "last acknowledged" time	|POST cards/:id/state/acknowledge|
|Update card information	|PUT cards/:id|