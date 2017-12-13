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

# QuantsBrothers Framework gateway FIX API
message which represents 

## Logon < A> message

The Logon `<A>` message must be the first message sent by the application requesting to initiate a FIX session. The Logon `<A>` message authenticates an institution establishing a connection to Server.

Tag|Name|Req|Description
---|----|---|-----------
98|EncryptMethod|Y|Server does not support encryption. Valid values: 0 = None.
108|HeartBtInt|Y|Heartbeat `<0>` message interval (seconds).
141|ResetSeqNumFlag|N|Indicates that the both sides of the FIX session should reset sequence numbers. Valid values: Y = Yes, reset sequence numbers N = No.

## Logout < 5> message

The Logout `<5>` message initiates or confirms the termination of a FIX session. Disconnection without the exchange of Logout `<5>` messages should be interpreted as an abnormal condition.

## Workflow

After connecting, logging on, and synchronizing sequence numbers, the client can submit orders.

## New Order Single < D> message

To submit a new order to server, send a New Order Single `<D>` message.Server will respond to a New Order Single `<D>` message with an Execution Report `<8>`.

Tag|Name|Req|Description
---|----|---|-----------
11 | ClOrdID | Y | Unique identifier of the order as assigned by the client. Uniqueness must be guaranteed by the client for the duration of the lifetime the order.
1 | Account | N | Market account id
660 | AcctIDSource | Y* (required if use Account `<1>`) | Type of account. Should be 99 = Other (custom or proprietary) This field is required if 1 = Account contains account identifier.
40 | OrdType| Y | Order type. Valid values:   1 = Market   2 = Limit
44 | Price | Y* (required if OrdType `<40>` = 2  Limit) | Price per unit of quantity
38 | OrderQty | Y | Quantity ordered. Needed > 0
54 | Side |Y | Side of the order. Valid values:   1 = Buy    2 = Sell
55 | Symbol | Y | Ticker symbol. Common, "human understood" representation of the security. e.g. ETH/BTC
59 | TimeInForce | N | Specifies how long the order remains in effect. Valid values: 1 = Good Till Cancel (GTC) 3 = Immediate or Cancel (IOC) 4 = Fill or Kill (FOK)
60 | TransactTime | Y | Time of request creation


## Submitting an order

1. Client sends server → New Order Single `<D>` message
2. Does server accept the order?
  * Yes, order is accepted for initial processing
    * server sends client ← an Execution Report `<8>` for a new order with:
      * ExecType `<150>` set to I = Order Status
      * OrdStatus `<39>` set to A = Pending New
    * Is the order marketable?
      * Yes, server executes one or more initial fills
        1. server sends client ← an Execution Report `<8>` for each fill or partial fill
        2. Does the order have remaining quantity?.
            * Yes, server puts the remaining quantity on the book
            * No, server closes the order
      * No, server puts the entire quantity of the order on the book
  * No, order is rejected
    * server sends client ← an Execution Report `<8>` indicating the order was rejected with:
      * ExecType `<150>` set to 8 = Rejected
      * OrdStatus `<39>` set to 8 = Rejected
