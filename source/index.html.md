---
title: API Reference
language_tabs:
#  - java
#  - ruby
#  - javascript
toc_footers:
  - ©2016 Trusona. All rights reserved.
##  - '<a href=''#''>Sign Up for a Developer Key</a>'
##  - '<a href=''https://github.com/tripit/slate''>Documentation Powered by Slate</a>'
includes:
 # - errors
search: false
published: true
---

# Introduction

Trusona provides a wide range of identity proofing and strong multi-factor authentication including: multi factor login, wire transfers authorization, password-less authentication, call center identity proofing...the list goes on. Even with the large number of options for integrating Trusona, there are ultimately just a few lines of code needed for each transaction. 

With the few lines of code, you specify which user you’d like to verify and at what level: Essential, Executive, or Elite. Then, Trusona simply lets you know if that user accepted, rejected, or did nothing with the verification. If they accepted, we also let you know at what level they accepted. We've provided a [couple of scenarios](#sarah) below to demonstrate what we mean.

<aside class="notice">The security and identity proofing that goes into the Trusona Elite level are so unbreakable that each wire transfer at this level are insured for one million dollars.</aside>

## <a name="sarah"></a> Sarah's Scenario

Sarah has been trusonafied at the Elite level. This mans that she uses the hard cryptographic token called TruToken and has already been identity proofed with her driver license and ePassport. Now, Sarah wants to login to the extranet at work which uses Trusona. 

On the login page, Sarah types in the email address she used to register with Trusona and clicks **LOGIN**. No password is required and Trusona sends her a push notification to her Trusona mobile app. She accepts authentication in the mobile app and used the hard cryptographic token, called TruToken, to accept authentication at the full Elite level. Accepting at the full Elite level means that she used the hard cryptographic token, TruToken.

## Marcus's Scenario

Marcus uses Trusona with his private wealth manager. Like Sarah, Marcus has been trusonafied at the Elite level. 

Marcus has instructed his private wealth manager to approve transactions over one million dollars only after Marcus verifies the transaction using his TruToken from Trusona. 

However, in cases where Marcus doesn’t have his TruToken with him, he has instructed his private wealth management firm to approve transactions for up to (but not above) one million dollars after Marcus verifies the transactions at the lower Executive level. At the Executive level Marcus can scan his driver license to accept a transaction. 

# The Trusona API

These scenarios, and others like them, are enabled by the Trusona API. With the Trusona RESTful API, you can submit a [request for a resource](#resource) to be trusonafied or request an [existing trusonafication ID](#get). 

The Trusona API is organized around REST. Our API has predictable, resource-oriented URLs, and uses HTTP response codes to indicate API errors. JSON is returned by all API responses, including errors.

You can view code examples in the dark area to the right.

## What It Takes to Integrate

The nuts and bolts of the few lines of code needed to integrate Trusona into your workflow are straightforward. They are:

1. Request header that includes an HMAC signature for [authentication](#authentication).
1. [Request body and schema](#requestBody) that includes the attributes of the performed action

# <a name="authentication"></a>Authentication

For enhanced security, Trusona employs a Keyed-**H**ashing **M**essage **A**uthentication **C**ode (HMAC) algorithm to ensure the integrity of the message, verify authentication of the sender and receiver to the client, and to prevent replay attacks.

All requests require an HMAC authentication, <anything else> as part of the message body. The API Account key and API Secret key to be used for HMAC encryption are provided by your sales engineer.

The cryptographic strength of the HMAC depends upon the size of the secret key that is used. As such, the SHA-256 function is used to generate a 256-bit signature for the submitted text.

The following instructions assume that the `Date` header is present.

<aside class="error">
If your HTTP client does not allow you to set the date header or access it before the request is sent, you may set an X-Date header with the current date in RFC1123 (<code>HttpDate</code>) and use that value in your HMAC authentication.
</aside>

```shell
   Content-Type: application/json
   x- date: Tue, 19 Jan 2016 17: 10:58 GMT
   authorization: {access_token}:
                   Base64(HMAC- SHA256
                         (HTTP_METHOD + \n + 
                          MD5(HTTP_BODY) + \n + 
                          CONTENT_TYPE + \n + 
                          Date() + \n + 
                          RESOURCE_URI))
  ```

An authenticated request must include the `Authorization` header. The `Authorization` header contains three parts:

1.  Version of the authentication scheme: HmacSHA256

1.  **API Account Key** to identify the user.

1.  The **Signature** proves who the user is and that the message has not been tampered with. The signature is generated using the following inputs:
  - The `HTTP_METHOD+"\n"+MD5(HTTP_BODY)+"\n"+CONTENT_TYPE+"\n"+Date()+"\n"+RESOURCE_URI))` that you hashed using HMAC-SHA256.
  - The API Shared Secret that you retrieve from your Trusona sales engineer.

To authenticate a request, you must sign the request with the shared secret for the account that is making the request and pass that signature an HTTP request header as follows:

```
Authorization: TRUSONA 796286d6-80b3-452b-``9677``-a57ccc6f0a23:lgzYy7bnC/7wlUuONzPmqPO2qZA369BiPitSwZP6YOQ=
```

`Authorization: TRUSONA <ApiKey>:<Base64(Signature)>`

<aside class="notice">
You must replace <code>ApiAccountKey</code> with your personal API key and <code>Signature</code> with the hashed signature. Have a look at the example on the right to see what the HTTP request header should look like.
</aside>


## Generating the Signature

Use the example in the panel on the right to generate the signature.

```java
import javax.crypto.*;
import javax.crypto.spec.*;
import java.util.prefs.*;

String secretKey = "secret";  // hmac private key
String payload = "payload";   // payload string -> POST + '\n' + Date … etc

String UTF_8 = "UTF8";
String algorithm = "HmacSHA256";

Mac macGenerator = Mac.getInstance(algorithm);
macGenerator.init(new SecretKeySpec(secretKey.getBytes(UTF_8), algorithm));
byte[] hmacBytes = macGenerator.doFinal(payload.getBytes(UTF_8));
Base64.byteArrayToBase64(hmacBytes);
```

```ruby
import <placeholder>

api = placeholder.authorize('placeholder')
```

```javascript
const placeholder = require('placeholder');

let api = placeholder.authorize('placeholder');
```

> Make sure to replace `secret` with your API key and `payload` with the payload data described in the next section.

## Payload Data

This section contains information that describe the elements that are put into the payload that is used to generate the signature.

<aside class="warning">
These values <b>must</b> match the request that is being sent. If they are different <b>in any way</b>, the signature will be wrong and you will receive a 401 response. This means that character case, spelling, date format, JSON message, etc. must be identical.

For example, <code>POST</code> and <code>post</code> do not match and will cause you to receive a 401 response.
</aside>

```shell
POST
Sun, 06 Jan 2014 08:49:37 GMT
api/v1/verifications
```
```json
[
  {
     "trusona_id": "123456789",
     "email": "email@domain.com",
     "action": "login",
     "resource": "Bank of XYZ",
     "agent_id": "ac1abbc1-15c1-4467-8583-6749c8d63bb4",
     "desired_level": 1,
     "callback_url": "https://api.bankxyz.com/auth/trusona/callback"
  }
]
```

| Name | Description |
| ---- | ----------- |
| Verb | HTTP method. Set this value to POST. |
| Date | Contents of the `Date` header. This value must be present and the value included in the signature and the value you send over the wire must match. The following is an example of the Date header value: `Sun, 06 Jan 2014 08:49:37 GMT`. Other date formats are accepted, but the format must match the HTTP 1.1 specification: [http://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html\#sec3.3](http://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.3). |
| resource | URI path. For example: `api/v1/verifications` |
| Body | Request body. The request body should contain a single JSON message object similar to the sample to the right. Specific examples for the `Create` and `Get` requests are included below.

# <a name="resource"></a>Create Resource

Creates a resource to be trusonafied.

## HTTP Request

`POST https://api.trusona.com/api/v1/verifications`

## <a name="requestBody"></a>Request Example

The full request includes the header, body, and schema. Check out the example on the right.

```shell
Content-Type: application/json
   x- date: Tue, 19 Jan 2016 17: 10:58 GMT
   authorization: {access_token}:
                   Base64(HMAC- SHA256
                         (HTTP_METHOD + \n + 
                          MD5(HTTP_BODY) + \n + 
                          CONTENT_TYPE + \n + 
                          Date() + \n + 
                          RESOURCE_URI))

```
```json
[
  {
     "trusona_id": "123456789",
     "email": "email@domain.com",
     "action": "login",
     "resource": "Bank of XYZ",
     "agent_id": "ac1abbc1-15c1-4467-8583-6749c8d63bb4",
     "desired_level": 1,
     "callback_url": "https://api.bankxyz.com/auth/trusona/callback"
  }
]

{
    "$schema": "http://json-schema.org/draft-04/schema#",
    "type": "object",
    "properties": {
      "trusona_id": {
        "type": "string",
        "description": "Trusona ID of referenced."
      },
      "email": {
        "type": "string",
        "description": "Registered email of referenced user."
      },
      "action": {
        "type": "string",
        "description": "Requested action to trusonafy."
      },
      "resource": {
        "type": "string",
        "description": "Resource to be acted on by specified action."
      },
      "agent_id": {
        "type": "string",
        "description": "Account handle of agent acting on behalf of the relying party (Must also be TruNotary for relying party)."
      },
      "desired_level": {
        "type": "number",
        "enum": [
          1,
          2,
          3,
          4
        ],
        "description": "Desired level of insurance."
      "callback_url": {
        "type": "string",
        "description": "URL to be fetched via GET when verification is COMPLETE."
      }
    },
    "required": [
      "action",
      "resource",
      "desired_level"
    ],
    "oneOf": [
      {"required": ["email"]},
      {"required": ["trusona_id"]}
    ]
}
```


## Response Example

A successful response (201) includes a header, response body, and schema. Check out the response example on the right.

```shell
Content-Type: application/json
```

```json
{
  "verification_id": "2cb9d511-8171-4113-a8af-201b20533cc0",
  "trusona_id": "123456789",
  "email": "robert.jordan@example.com",
  "action": "login",
  "resource": "Bank of XYZ",
  "agent_id": "ac1abbc1-15c1-4467-8583-6749c8d63bb4",
  "accepted_level": 1,
  "status": "ACCEPTED",
  "interval": 600,
  "result_id": "079fb6a7-937c-4e18-8ceb-776a55d6b01e",
  "created_date": "2016-01-19T17:10:58Z",
  "updated_date": "2016-01-19T17: 10:58Z"
}

{
  "$schema": "http://json-schema.org/draft-04/ schema#",
  "type": "object",
  "properties": {
    "verification_id": {
      "type": "string",
      "description": "Verification ID in UUID form."
    },
    "trusona_id": {
      "type": "string",
      "description": "Trusona ID of referenced user."
    },
    "email": {
      "type": "string",
      "description": "Registered email of referenced user."
    },
    "action": {
      "type": "string",
      "description": "Requested action to trusonafy."
    },
    "resource": {
      "type": "string",
      "description": "Resource to be acted on by spefied action."
    },
    "agent_id": {
      "type": "string",
      "description": "Account handle of agent acting on behalf of the relying party (Must also be notary for relying party)."
    },
    "accepted_level": {
      "type": "number",
      "enum": [
        1,
        2,
        3,
        4
      ],
      "description": "Accepted at this level of insurance."
    },
    "status": {
      "type": "string",
      "enum": [
        "ACCEPTED",
        "ACCEPTED_AT_LOWER_LEVEL",
        "ACCEPTED_AT_HI GHER_LEVEL",
        "REJECTED",
        "IN_PROGRESS"
      ],
      "description": "Status of verification"
    },
    "interval": {
      "type": "number",
      "description": "Next suggested polling interval in seconds."
    },
    "result_id": {
      "type": "string",
      "description": "Result ID of completed verification details."
    },
    "created_date": {
      "type": "string",
      "description": "Date record was created."
    },
    "updated_date": {
      "type": "string",
      "description": "Date record was last updated."
    }
  }
}
```

Attribute | Type | Description
--------- | ---- | -----------
verification_id | string | Verification ID in UUID form.
trusona_id | string | Trusona ID of referenced user.
email | string | Registered email of referenced user.
action | string | Action performed to trigger ID trusonafication.
resource | string | Resource to be acted on by spefied action.
agent_id | string | Account handle of agent acting on behalf of the relying party (Must also be notary for relying party).
accepted_level | enum[number] | Accepted level of insurance. Possible responses are: `1`, `2`, `3`, or `4`.
status | enum[string] | Status of the verification. Possible responses are `ACCEPTED`, `ACCEPTED_AT_LOWER_LEVEL`, `ACCEPTED_AT_HIGHER_LEVEL`, `REJECTED`, OR `IN_PROGRESS`.
interval | number | Next suggested polling interval in seconds.
result_id | string | Result ID of completed verification details.
created_date | string | Date record was created.
updated_date | string | Date record was last updated.

### Create Resource Response Codes

HTTP Response Code | Message
---------- | ------
201 | Everything worked as expected. You'll receive a response as shown on the right.
403 | Full authentication is required to access this resource.
422 | Unprocessible entity. This means the server understands the content type of the request entity and the syntax of the request entity is correct, but was unable to process the contained instructions.

# <a name="get"></a>Get Resource

Gets the trusonafication ID.


## HTTP Request

`GET https://api.trusona.com/api/v1/verifications/verification_id`

## Request Example

The full request includes the header, body, and schema. Check out the example on the right.

```shell
Content-Type: application/json
   x- date: Tue, 19 Jan 2016 17: 10:58 GMT
   authorization: {access_token}:
                   Base64(HMAC- SHA256
                         (HTTP_METHOD + \n + 
                          MD5(HTTP_BODY) + \n + 
                          CONTENT_TYPE + \n + 
                          Date() + \n + 
                          RESOURCE_URI))

```
```json
{
  "verification_id": "2cb9d511-8171-4113-a8af-201b20533cc0"
}

{
    "$schema": "http://json-schema.org/draft-04/schema#",
    "type": "object",
    "properties": {
      "trusona_id": {
        "type": "string",
        "description": "Trusona ID of referenced."
      },
      "email": {
        "type": "string",
        "description": "Registered email of referenced user."
      },
      "action": {
        "type": "string",
        "description": "Requested action to trusonafy."
      },
      "resource": {
        "type": "string",
        "description": "Resource to be acted on by specified action."
      },
      "agent_id": {
        "type": "string",
        "description": "Account handle of agent acting on behalf of the relying party (Must also be TruNotary for relying party)."
      },
      "desired_level": {
        "type": "number",
        "enum": [
          1,
          2,
          3,
          4
        ],
        "description": "Desired level of insurance"
      "callback_url": {
        "type": "string",
        "description": "URL to be fetched via GET when verification is COMPLETE."
      }
    },
    "required": [
      "action",
      "resource",
      "desired_level"
    ],
    "oneOf": [
      {"required": ["email"]},
      {"required": ["trusona_id"]}
    ]
}
```

Parameter | Type | Description
--------- | ---- | -----------
verification_id | string | Verification ID from Trusona in UUID form.


## Response Example

> A successful response (200) includes an array of strings in the following format:

```shell
Content-Type: application/json
```

```json
{
  "verification_id": "2cb9d511-8171-4113-a8af-201b20533cc0",
  "trusona_id": "123456789",
  "email": "robert.jordan@example.com",
  "action": "login",
  "resource": "Bank of XYZ",
  "agent_id": "ac1abbc1-15c1-4467-8583-6749c8d63bb4",
  "accepted_level": 1,
  "status": "ACCEPTED",
  "interval": 600,
  "result_id": "079fb6a7-937c-4e18-8ceb-776a55d6b01e",
  "created_date": "2016-01-19T17:10:58Z",
  "updated_date": "2016-01-19T17: 10:58Z"
}

{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "type": "object",
  "properties": {
    "verification_id": {
      "type": "string",
      "description": "Verification ID in UUID form."
    },
    "trusona_id": {
      "type": "string",
      "description": "Trusona ID of referenced user."
    },
    "email": {
      "type": "string",
      "description": "Registered email of referenced user."
    },
    "action": {
      "type": "string",
      "description": "Requested action to trusonafy."
    },
    "resource": {
      "type": "string",
      "description": "Resource to be acted on by specfied action."
    },
    "agent_id": {
      "type": "string",
      "description": "Account handle of agent acting on behalf of the relying party (Must also be notary for relying party)."
    },
    "accepted_level": {
      "type": "number",
      "enum": [
        1,
        2,
        3,
        4
      ],
      "description": "Accepted at this level of insurance."
    },
    "status": {
      "type": "string",
      "enum": [
        "ACCEPTED",
        "ACCEPTED_AT_LOWER_LEVEL",
        "ACCEPTED_AT_HIGHER_LEVEL",
        "REJECTED",
        "IN_PROGRESS"
      ],
      "description": "Status of verification."
    },
    "interval": {
      "type": "number",
      "description": "Next suggested polling interval in seconds."
    },
    "result_i d": {
      "type": "string",
      "description": "Result ID of completed verification details."
    },
    "created_date": {
      "type": "string",
      "description": "Date record was created."
    },
    "updated_date": {
      "type": "string",
      "description": "Date record was last updated."
    }
  }
}
```

Attribute | Type | Description
--------- | ---- | -----------
verification_id | string | Verification ID in UUID form.
trusona_id | string | Trusona ID of referenced user.
email | string | Registered email of referenced user
action | string | Action performed to trigger ID trusonafication.
resource | string | Resource to be acted on by spefied action.
agent_id | string | Account handle of agent acting on behalf of the relying party (Must also be notary for RP).
accepted_level | enum[number] | Accepted level of insurance. Possible responses are: `1`, `2`, `3`, or `4`.
status | enum[string] | Status of the verification. Possible responses are `ACCEPTED`, `ACCEPTED_AT_LOWER_LEVEL`, `ACCEPTED_AT_HIGHER_LEVEL`, `REJECTED`, OR `IN_PROGRESS`.
interval | number | Next suggested polling interval in seconds.
result_id | string | Result ID of completed verification details.
created_date | string | Date record was created.
updated_date | string | Date record was last updated.

### Get Resource Response Codes

HTTP Response Code | Message
--------- | ----------
200 | Everything worked as expected. You'll receive a response as shown on the right.
403 | Full authentication is required to access this resource.