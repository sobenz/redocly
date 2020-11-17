---
layout: default
title: Account Linking Guide
permalink: /docs/guides/tutorials/account-linking-guide
parent: Tutorials
grand_parent: Guides
---

# Account Linking Guide
This guide shows how to use the Glympse REST API to link an email or phone number to a Glympse account.

Doing so enables several benefits, including:

 * Account recovery
 * Multiple device support
 * Participation in group features, including FamilyMap (cards).

## API Key Requirements
Account linking requires specific API key permissions. If you run into problems using any features mentioned in this guide, please contact your Glympse representative to verify your API key(s).

## Logging in with an Email or Phone Number
To verify a Glympse account:
 1. Obtain an email address or an SMS-capable phone number from the user.
 2. Next, call account/confirm:
  * `type` to `email` or `phone` as appropriate; note that `type` is specified as a query parameter and as part of the body.
  * `address` to the contact details provided by the user.

Example Query:
```js
POST account/confirm?api_key=${api_key}&type=${type}  
{    
   "type" : "${type}",   
   "address" : "${address}"   
}
```
Response:
```js
{
   "result": "ok",
   "response": {
      "expires": ${expiration_time}
   },
   "meta": {
      "time": ${metadata_time}
   }
 }
 ```
  Glympse then sends a confirmation code to the address.

 3. Ask the user to enter the confirmation code.

 4. Call account/create to submit the confirmation code.  
  Set:
  * `type` to match the previous call.
  * `address` to match the previous call.
  * `code` to the confirmation code entered by the user.

Example Query:
```js
POST account/create?api_key=${api_key}&type=${type}
{ 
   "type" : "${type}", 
   "address" : "${address}", 
   "code": "${code}"
}
```
Response:
```js
{
   "result": "ok",
   "response": {
      "type": "account",
      "id": "${account_id}",
      "password": "${password}"
   },
   "meta": {
     "time": ${metadata_time}
   }
}
```
When you do this, Glympse returns credentials linked to the address. If the user has previously linked the address to an account, the returned credentials include the previous account. If this is a new verification address, the credentials include a new account.

If the account existed previously, the user has access to earlier favorites, history, and other activities.

Whether the credentials are new or existed previously, use them to log in or refresh the access token.

## Linking Unverified Accounts
Certain accounts, such as those created with earlier apps, may not be verified. We recommend verifying all accounts to ensure all users enjoy the benefits of account recovery, multiple device support, and so on.

If your application already has credentials for a Glympse account and you wish to continue using these credentials (if possible):

 1. Obtain an email address or an SMS-capable phone number from the user.

 2. Next, call users/self/linked_accounts/:type/confirm and then set:
  * `type` to `email` or `phone` as appropriate. Note that `type` must appear as a query parameter and as a body attribute.
  * `address` to the contact details provided by the user.

Example Query:
```js
POST users/self/linked_accounts/:type/confirm
{ 
   "type" : "${type}", 
   "address" : "${address}" 
}
```
Response:
```js
{
   "result": "ok",
   "response": {
      "expires": ${expiration_time}
   },
   "meta": {
      "time": ${metadata_time}
   }
 }
 ```
Glympse then sends a confirmation code to the specified address.

 3. Obtain the confirmation code from the user.

 4. Call users/self/linked_accounts/:type/link to submit the confirmation code; set:
  * `type` to match the previous call.
  * `address` to match the previous call.
  * `code` to the confirmation code entered by the user.

Example Query:
```js
POST users/self/linked_accounts/:type/link
{ 
   "type" : "${type}", 
   "address" : "${address}", 
   "code": "${code}" 
}
```
Response:
```js
{
   "result": "ok",
   "response": {
      "type": "linked_account",
        "account": {
        "type": "${account_type}",
        "id": "${account_id}",
        "displayname": "${display_name}",
        "auth_enabled": ${enabled_value},
        "status": "ok"
      }
   },
   "meta": {
     "time": ${metadata_time}
   }
}
```
When successful, the current credentials are linked to the specified contact address.

If you receive an `existing_link` error, the contact address is already associated with a different set of credentials. For a limited time, you can use the confirmation code from Steps 3 and 4 to recover the previous credentials. If the code expires before you recover the credentials, you'll need to prompt the user to request a new one.

## Managing Previously Linked Accounts
Each Glympse account supports just one verified email address and/or phone number. This means an account:
 * may be associated with an email address or a phone number.
 * may be associated with an email address and a phone number.
 * may not be associated with multiple email addresses or phone numbers.

When using a confirmation code to link a Glympse account to a contact address, you may receive an an `existing_link` error. If this happens, we recommend one of the following:

 1. Log out of the existing credentials and use the previous credentials. To recover the previous credentials, use the confirmation code and the contact address to call `account/create`, as shown earlier.

    This is the preferred approach; it allows the user to retain favorites, history, and other activities associated with the original account. (Data associated with the current account, however, remains with the current account.)

    Note that confirmation codes are are limited and eventually expire.

 2. You can force the current account to link to the contact address by adding the `force=true` parameter to the query from Step 4 in the previous section. This is not recommended as it discards favorites, history, and activities associated with the earlier account.

Regardless of the approach you choose, data associated with the discarded account is not migrated to the linked account.

If you need additional assistance, contact your Glympse representative.