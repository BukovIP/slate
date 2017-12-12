---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - C#
  - python
  - javascript

toc_footers:
  - <a href='#'>-------------</a>
  - <a href='http://www.QuantBrothers.com'>QuantBrothers</a>

includes:
  - errors

search: true
---

# Index
789456123
# !!!

<a href='## Logon_<A>_message'>Logon <A> message</a><br>
Logout <5> message<br>
Workflow<br>
New Order Single '<D>' message<br>
Submitting an order<br>
Execution Report <8> message<br>
Order Cancel/Replace Request `<G>` message<br>
Submitting an order changes    10
Order Cancel Request <F> message    11
Submitting an order cancel    12
Order Status Request <H> message    13
Order Mass Status Request <AF> message    14
Security List Request <x> message    15
Security List <y> message    16
Market Data Request <V> message    18
Market Data - Snapshot/Full Refresh <W> message    20
Market Data - Incremental Refresh <X> message    21



## Logon_<A>_message

Logon `<A>` message
The Logon '<A>' message must be the first message sent by the application requesting to initiate a FIX session. The Logon <A>message authenticates an institution establishing a connection to Server.

Tag|Name|Req|Description
---|----|---|-----------
98|EncryptMethod|Y|Server does not support encryption. Valid values: 
0 = None
108|HeartBtInt|Y|Heartbeat <0> message interval (seconds)
141|ResetSeqNumFlag|N|Indicates that the both sides of the FIX session should reset sequence numbers. 
Valid values:<br>Y = Yes, reset sequence numbers   N = No


## Logout <5> message
The Logout <5> message initiates or confirms the termination of a FIX session. Disconnection without the exchange of Logout <5> messages should be interpreted as an abnormal condition.

<aside class="notice">
## Workflow
After connecting, logging on, and synchronizing sequence numbers, the client can submit orders.
</aside>


## New Order Single <D> message
To submit a new order to server, send a New Order Single <D> message.
Server will respond to a New Order Single <D> message with an Execution Report <8>.

Tag|Name|Req|Description
11|ClOrdID|Y|Unique identifier of the order as assigned by the client. Uniqueness must be guaranteed by the client for the duration of the lifetime the order.
1|Account|N|Market account id
660|AcctIDSource|Y* (required if use Account <1>)|Type of account. Should be 99 = Other (custom or proprietary) This field is required if 1 = Account contains account identifier.
40|OrdType|Y|Order type. Valid values:   1 = Market   2 = Limit
44|Price|Y* (required if OrdType <40> = 2  Limit)|Price per unit of quantity
38|OrderQty|Y|Quantity ordered. Needed > 0
54|Side|Y|Side of the order. Valid values:   1 = Buy    2 = Sell
55|Symbol|Y|Ticker symbol. Common, "human understood" representation of the security. e.g. ETH/BTC
59|TimeInForce|N|Specifies how long the order remains in effect. Valid values:    1 = Good Till Cancel (GTC)   3 = Immediate or Cancel (IOC)   4 = Fill or Kill (FOK)
60|TransactTime|Y|Time of request creation

<aside class="notice">
## Submitting an order
1. Client sends server → New Order Single <D> message
2. Does server accept the order?
   -Yes, order is accepted for initial processing
	* server sends client ← an Execution Report <8> for a new order with:
		* ExecType <150> set to I = Order Status
		* OrdStatus <39> set to A = Pending New
	*Is the order marketable?
		* Yes, server executes one or more initial fills
			1. server sends client ← an Execution Report <8> for each fill or partial fill
			2. Does the order have remaining quantity?
				* Yes, server puts the remaining quantity on the book
				* No, server closes the order
		* No, server puts the entire quantity of the order on the book
  -No, order is rejected
	* server sends client ← an Execution Report <8> indicating the order was rejected with:
	* ExecType <150> set to 8 = Rejected
	* OrdStatus <39> set to 8 = Rejected
</aside>













# QuantBrothers

Welcome to the Kittn API! You can use our API to access Kittn API endpoints, which can get information on various cats, kittens, and breeds in our database.

We have language bindings in Shell, Ruby, and Python! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

This example API documentation page was created with [Slate](https://github.com/lord/slate). Feel free to edit it and use it as a base for your own API's documentation.

# Authentication

> To authorize, use this code:

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
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

Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://example.com/developers).

Kittn expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Kittens

## Get All Kittens

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
    "id": 1,
    "name": "Fluffums",
    "breed": "calico",
    "fluffiness": 6,
    "cuteness": 7
  },
  {
    "id": 2,
    "name": "Max",
    "breed": "unknown",
    "fluffiness": 5,
    "cuteness": 10
  }
]
```

This endpoint retrieves all kittens.

### HTTP Request

`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2"
  -X DELETE
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete


