---
title: Retreaver Call Control 1.0 API Reference Docs

language_tabs:
  - shell: cURL

toc_footers:
  - <a href='https://retreaver.com/users/sign_up'>Sign Up for a Developer Key</a>
  - <a href='https://support.retreaver.com/'>Knowledge Base</a>

includes:
  - errors

search: true
---

# Introduction

**This API is currently under development.**

The Retreaver Call Control API provides control over complex call flows using revocable API keys which are scoped by 
Contact Handler or Handler Group and action. 

For instance, I can provide my call center with an API key which only allows them to reject calls that have been routed 
to them, but which doesn't allow them to perform any other actions or to modify calls routed to other Contact Handlers.


## Version

This is version 1.0 of the Call Control API.

## Multi-format

The Call Control API can be accessed by JSON or XML. Simply change the file extension and Content-Type header to match your preferences.

We recommend using JSON since XML is archaic, has high overhead, and is generally awful.

Format | Content-Type     | Extension
------ | ---------------- | ---------
JSON   | application/json | json
XML    | text/xml         | xml


## Paginated

Retreaver is a RESTful paginated API that returns 25 results per page. Each relevant index response will have a [Link HTTP header](https://www.w3.org/wiki/LinkHeader) present.

`Link: <https://retreaver.com/calls.json?api_key=woofwoofwoof&company_id=1&sort_by=created_at&order=asc&page=6996>; rel="last", <https://retreaver.com/calls.json?api_key=woofwoofwoof&company_id=1&sort_by=created_at&order=asc&page=2>; rel="next"`

By parsing the Link header, you can determine the last, next, and previous pages.


## Nomenclature

Please note, this documentation uses our Enterprise Edition nomenclature.

Old          | Performance Marketing Edition | Enterprise Edition
-------------|-------------------------------|-------------------
Affiliate    | Publisher                     | Source
Target       | Buyer                         | Contact Handler/Call Endpoint
Target Group | Buyer Group                   | Handler Group


## How Calls Work


### Legs of a Call


### In-band Signals vs Out-of-band Signals




# Authentication

> To authorize, use this code:

```shell
# With cURL, you can just pass the correct API with each request.
curl "https://api.retreaver.com/call_control/v1/active_calls.json?api_key=woofwoofwoof"
```

> Make sure to replace `woofwoofwoof` with your API key.

Retreaver uses API keys to allow access to the API. The Call Control api uses separate API keys depending on the action
taken by your Contact Handler or Group. 

# Active Calls

The Active Call API allows the call handler to determine if a call that was routed to them was routed through Retreaver,
and if so, to retrieve the Call's UUID and the caller's real CallerID.

The Active Calls API only returns calls that are in progress and which have a matching CallerID in either the 
caller_number or caller_number_sent fields, which have been routed to the Handler or Group matching the API key used.

## List Active Calls

```shell
curl "https://api.retreaver.com/call_control/v1/active_calls.json?api_key=woofwoofwoof"
```

> The above command returns JSON structured like this:

```json
{
  "success": true,
  "results": [
    {
      "caller_number": "+18668987878",
      "caller_number_sent": "+18665551234",
      "dialed_out_number": "+14166686980",
      "call_uuid": "e64268f8-18a3-4881-b8ed-80e06b17fa74",
      "started_at": 1511218175475,
      "dialed_out_at": 1511218175475
    }
  ]
}
```

If one or more call is found, success is set to true. Results are returned in reverse-chronological order with the most
recently dialed calls first.

### HTTP Request

`GET https://api.retreaver.com/call_control/v1/active_calls.json?api_key=woofwoofwoof`

### Request Parameters

Parameter | Description
--------- | -----------
api_key | An API Key belonging to a Contact Handler or Group.


### Response Parameters

Parameter | Description
--------- | -----------
caller_number | The actual phone number of the caller, as received by Retreaver or as entered in by the transferring agent.
caller_number_sent | The CallerID used by Retreaver when sending the call to the Call Endpoint. This may be a randomized value if a [Static Caller Number](https://retreaver.github.io/core-api-docs/#static-caller-numbers) is detected.
dialed_out_number | This is the number of the Call Endpoint which we dialed.
call_uuid | This is the universally unique identifier used to access the call.
started_at | The time the call was received by Retreaver, in milliseconds since Unix epoch.
dialed_out_at | The time the call was dialed out to the Call Endpoint, in milliseconds since Unix epoch.

# Call Transfer

The Call Transfer API provides the functionality needed to perform attended or unattended call transfers.

## Transfer a Call

Using the Call UUID fetched via the Active Calls API above, the Call Endpoint can then transfer the call in either an 
attended or unattended fashion.

```shell
curl ""
curl -s \
    -X POST \
    https://api.retreaver.com/call_control/v1/calls/CALL_UUID/transfer.json?api_key=woofwoofwoof \
    -H "Content-Type: application/json" \
    -d '{"hold_caller":true,"transfer_to":"+18668987878"}'
```

> The above command returns JSON structured like this on success:

```json
{
  "success": true,
  "transfer_uuid": "d12c9c93-19eb-491d-9afa-e30cbb02e832"
}
```

> or like this on failure:

```json
{
  "success": false,
  "error": "call not live"
}
```


### Request Parameters

Parameter | Description
--------- | -----------
api_key | An API Key belonging to a Contact Handler or Group.
hold_caller | When true, the caller is placed on hold and an unattended transfer is initiated. When not true, an attended transfer is initiated with all three parties conferenced at once.
transfer_to | The phone number or SIP address to transfer the call to.


### Response Parameters

Parameter | Description
--------- | -----------
success | Whether the call transfer was successful or not.
transfer_uuid | The UUID of the transfer, which must be recorded to perform further actions against the transfer.
error | An error message.

### Error Messages

Error | Meaning
----- | -------
call not live | The call has already terminated.
cannot dial transfer_to number | The number you are attempting to dial could not be dialed, most likely due to restrictions on your account.
call already transferred | A call transfer is already in progress on this call and it cannot be re-transferred using this API key. You must use the transfer key of whatever Endpoint the call was transferred to.
not authorized | The API key is invalid.

## Connect the Caller (unattended transfer)

Once the call has been transferred with hold_caller "true", the caller can be bridged in to the conference by using the 
Transfer UUID.

```shell
curl -s \
 -X POST \
 https://api.retreaver.com/call_control/v1/transfers/7df81876-6e46-4b50-945e-7f5ad97a555e/conference_caller.json \
 -H "Content-Type: application/json"
```

> The above command returns JSON structured like this on success:

```json
{
  "success": true
}
```

> or like this on failure:

```json
{
  "success": false,
  "error": "call not live"
}
```

### Request Parameters

Parameter | Description
--------- | -----------
transfer_uuid | The UUID of the transfer retrieved from the Transfer API above.

### Response Parameters

Parameter | Description
--------- | -----------
success | Whether the request was successful or not.
error | An error message.

### Error Messages

Error | Meaning
----- | -------
call not live | The call has already terminated.
caller hung up | The caller disconnected while on hold.
caller not held | The caller was not transferred using hold_caller "true" and can hear everything your agent is saying!
transfer not found | The transfer UUID is invalid.


## Stop the Transfer

If the Call Endpoint that you attempted to transfer the call to doesn't pick up or can't take the call, you can 
terminate the outbound leg to the Endpoint using the method below.

```shell
curl -s \
 -X DELETE \
 https://api.retreaver.com/call_control/v1/transfers/7df81876-6e46-4b50-945e-7f5ad97a555e.json \
 -H "Content-Type: application/json"
```

> The above command returns JSON structured like this on success:

```json
{
  "success": true
}
```

> or like this on failure:

```json
{
  "success": false,
  "error": "call not live"
}
```

### Request Parameters

Parameter | Description
--------- | -----------
transfer_uuid | The UUID of the transfer retrieved from the Transfer API above.

### Response Parameters

Parameter | Description
--------- | -----------
success | Whether the request was successful or not.
error | An error message.

### Error Messages

Error | Meaning
----- | -------
call not live | The call has already terminated.
transfer not in progress | The outbound leg going to the transferee has already terminated.
transferor not present | The agent who originally accepted the call is no longer on the call. We will not terminate the call in this case because it would leave the caller with dead air.
transfer not found | The transfer UUID is invalid.

### HTTP Request

`DELETE https://api.retreaver.com/call_control/v1/transfers/7df81876-6e46-4b50-945e-7f5ad97a555e.json`


# Live Call Return

Allows the transferee to "return" a call that has been transferred to them, even though they have already picked up the
call. This allows call centers to pick up a call and play on-hold messages or ringing while they queue the caller.

Once they execute a call return, the call is "taken back" by Retreaver and the next Contact Handler is determined and 
dialed. 

Live Call Return API keys are associated with a Contact Handler or Group and a timeout given in seconds. Once a call is
transferred to a Handler, the timer starts, and after the timeout duration has passed the call can no longer be 
returned.

## Return a Call


# Live Call Acceptance

Allows the transferee to "accept" a call that has been transferred to them, preventing the system from "taking back" a
call that has already been connected to them.

In a way the Live Call Acceptance API is the opposite of the Live Call Return API. With the Call Return API, Retreaver
assumes a call is accepted by the Handler as soon as it is picked up. With the Call Acceptance API, Retreaver assumes 
the call is not accepted until the acceptance endpoint is requested.

Which paradigm is used depends on the settings of the individual Call Handler.

## Accept a Call
