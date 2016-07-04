---
title: API Reference
language_tabs:
  - shell
  - ruby
  - python
  - javascript
toc_footers:
  - '<a href=''#''>Sign Up for a Developer Key</a>'
  - '<a href=''https://github.com/tripit/slate''>Documentation Powered by Slate</a>'
includes:
  - errors
search: true
published: true
---

# Introduction

Welcome to the Trusona API! You can use our API to access Trusona API endpoints, which can get information on various stuffs.

The Trusona API is organized around REST. Our API has predictable, resource-oriented URLs, and uses HTTP response codes to indicate API errors. JSON is returned by all API responses, including errors.

We have language bindings in Shell, Ruby, and Python! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

# Authentication

> To authorize, use this code:

```shell
Content-Type: application/json
x- date: Tue, 19 Jan 2016 17: 10:58 GMT
authorization: {access_token}:Base64(HMAC- SHA256( HTTP_METHOD + \n + MD5(HTTP_BODY ) + \n + CONTENT_TYPE + \n + Dat e() + \n + RESOURCE_URI ))
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
```

> Make sure to replace `meowmeowmeow` with your API key.

Trusona uses HMAC SHA-256 encruption to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Calls

## Create Resource

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

```shell
curl "http://example.com/api/kittens"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let kittens = api.kittens.get();
```

> The above command returns JSON structured like this:

```json
[
  {
     "trusona_id": "123456789",
     "action": "logi n",
     "resource": "Bank of XYZ",
     "agent _id": "ac1abbc1-15c1-4467-8583-6749c8d63bb4",
     "level": 1,
     "callback_url": "https://api .bankxyz.com/ auth/trusona/callback"
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`POST https://api.trusona.com/api/v1/verifications`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
trusona_id | false | If set to true, the result will also include cats.
action | true | If set to false, the result will include kittens that have already been adopted.
resource | | lorem ipsum
agent_id | | lorem ipsum
level | | lorem ipsum
callback_url | | lorem ipsum

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

> The above command returns JSON structured like this:

```json
[
  {
     "trusona_id": "123456789",
     "action": "logi n",
     "resource": "Bank of XYZ",
     "agent _id": "ac1abbc1-15c1-4467-8583-6749c8d63bb4",
     "level": 1,
     "callback_url": "https://api .bankxyz.com/ auth/trusona/callback"
  }
]
```

This endpoint <does this>.

### HTTP Request

`POST https://api.trusona.com/api/v1/verifications`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
trusona_id | false | If set to true, the result will also include cats.
action | true | If set to false, the result will include kittens that have already been adopted.
resource | | lorem ipsum
agent_id | | lorem ipsum
level | | lorem ipsum
callback_url | | lorem ipsum

### Response

A successful response (201) includes an array of strings in the following format:

```json
{

  "verification_id": "2cb9d511-8171-4113-a8af-201b20533cc0",
  "trusona_id": "123456789",
  "email": "robert.jordan@example.com",
  "action": "login",
  "resource": "Bank of XYZ",
  "agent _id": "ac1abbc1-15c1-4467-8583-6749c8d63bb4",
  "accepted_level": 1,
  "status": "ACCEPTED",
  "interval": 600,
  "resul t_id": "079fb6a7-937c-4e18-8ceb-776a55d6b01e",
  "created_date": "2016-01-19T17:10:58Z",
  "updated_date": "2016-01-19T17: 10:58Z"
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET https://api.trusona.com/api/v1/verifications/verification_id`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the data to retrieve
