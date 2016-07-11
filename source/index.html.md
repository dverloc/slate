---
title: API Reference
language_tabs:
  - java
  - ruby
  - javascript
toc_footers:
##  - '<a href=''#''>Sign Up for a Developer Key</a>'
##  - '<a href=''https://github.com/tripit/slate''>Documentation Powered by Slate</a>'
includes:
  - errors
search: true
published: true
---

# Introduction

Welcome to the Trusona API! You can use our API to access Trusona API endpoints, which can get information on various stuffs.

The Trusona API is organized around REST. Our API has predictable, resource-oriented URLs, and uses HTTP response codes to indicate API errors. JSON is returned by all API responses, including errors.

We have language bindings in Java, Ruby, and Javascript! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

# Authentication

For enhanced security, Trusona employs a Keyed-**H**ashing **M**essage **A**uthentication **C**ode (HMAC) algorithm to ensure the integrity of the message, verify authentication of the sender and receiver to the client, and to prevent replay attacks.

All requests require an HMAC authentication, <anything else> as part of the message body. The API Account key and API Secret key to be used for HMAC encryption are provided by your sales engineer.

The cryptographic strength of the HMAC depends upon the size of the secret key that is used. As such, the SHA-256 function is used to generate a 256-bit signature for the submitted text.

The following instructions assume that the `Date` header is present.

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
Authorization: HmacSHA256 796286d6-80b3-452b-``9677``-a57ccc6f0a23:lgzYy7bnC/7wlUuONzPmqPO2qZA369BiPitSwZP6YOQ=
```

`Authorization: HmacSHA256 <ApiAccountKey>:<Signature>`

<aside class="notice">
You must replace <code>ApiAccountKey</code> with your personal API key and <code>Signature</code> with the hashed signature.
</aside>





## Generating the Signature

Use the tabs on the right to view the appropriate code snippet to generate the signature.

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

> Make sure to replace `secret` with your API key.

## Payload Data

This section contains information that describe the elements that are put into the payload that is used to generate the signature.

<aside class="warning">
These values <b>must</b> match the request that is being sent. If they are different <b>in any way</b>, the signature will be wrong and you will receive a 401 response. This means that character case, spelling, date format, JSON message, etc. must be identical.

For example, <code>POST</code> and <code>post</code> do not match and will cause you to receive a 401 response.
</aside>

```
POST
Sun, 06 Jan 2014 08:49:37 GMT
api/v1/verifications
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
| Body | Request body. The request body should contain a single JSON message object similar to the sample to the right.

The attributes included in the body are described below.

Attribute | Type | Description
--------- | ---- | ------------
trusona_id | `string`, required | Trusona ID of referenced user.
email |  `string`, required | Registered email of referenced user.
action | `string`, required | Requested action to trigger ID trusonification.
resource | `string`, required | Resource to be acted upon by specified action.
agent_id | `string`, required | Account handle of agent acting on behalf of the relying party (Must also be TruNotary for RP).
desired_level | `enum[number]`, required | Desired level of insuranced. Possible values are 1, 2, 3, or 4.
callback_url | `string`, required | URL to be fetched via `GET` when verification is `COMPLETE`.

# Calls

## Create Resource

Creates a resource to be trusonafied.

### HTTP Request

`POST https://api.trusona.com/api/v1/verifications`

### Full Request Example

The full request includes the header, body, and schema. Check out the full example on the right.

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
        "description": "Trusona ID of referenced"
      },
      "email": {
        "type": "string",
        "description": "Registered email of referenced user"
      },
      "action": {
        "type": "string",
        "description": "Requested action to trusonafy"
      },
      "resource": {
        "type": "string",
        "description": "Resource to be acted on by specified action"
      },
      "agent_id": {
        "type": "string",
        "description": "Account handle of agent acting on behalf of the relying party (Must also be TruNotary for RP)"
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
        "description": "URL to be fetched via GET when verification is COMPLETE"
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


## Create Resource Response

> A successful response (201) includes an array of strings in the following format:

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
```

Attribute | Type | Description
--------- | ---- | -----------
verification_id | `string` | Verification ID in UUID form.
trusona_id | `string` | Trusona ID of referenced user.
email | `string` | Registered email of referenced user
action | `string` | Action user performed to trigger ID verification.
resource | `string` | lorem ipsum
agent_id | `string` | lorem ipsum
accepted_level | `string` | Field description for accepted_level
status | `string` | Filler text
interval | `string` | Time between 
result_id | `string` | Result from Trusona integration
created_date | `string` | Date XX was created.
updated_date | `string` | Date update was made to XX.

## GET Resource

### HTTP Request

`GET https://api.trusona.com/api/v1/verifications/verification_id`

> The body of the request should include:

```json
{
  "verification_id": "2cb9d511-8171-4113-a8af-201b20533cc0",
}
```

### Query Parameters

Parameter | Description
--------- | -----------
verification_id | Verification ID from Trusona in UUID form.


### Response

> A successful response (200) includes an array of strings in the following format:

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
```

Parameter | Description
--------- | -----------
verification_id | ID to verify identity.
trusona_id | Trusona ID associated to the user.
email | User's email address
action | Action user performed to trigger ID verification.
resource | lorem ipsum
agent_id | lorem ipsum
accepted_level | Field description for accepted_level
status | Filler text
interval | Time between 
result_id | Result from Trusona integration
created_date | Date XX was created.
updated_date | Date update was made to XX.
