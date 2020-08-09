---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  # - shell

# toc_footers:
#  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: Yes
---

# Introduction

Welcome to the Banxa partner integration API. 

This API documentation will guide you through the integration with Banxa payment APIs and processes.

<aside class="success">
All API responses are in <strong>JSON</strong> format and follow <a href="https://jsonapi.org">JSON:API</a> format
</aside>

Please contact your account representative or <a href="mailto:support@banxa.com">support@banxa.com</a> to receive your production and testing URLs and your API credentials.

The URLs will be of the format https://[partner].banxa.com and https://[partner].banxa-sandbox.com 

## Checkout Approaches
There are 2 approaches available to our partners for integrating with Banxa to take the customer through the on-ramp experience.

### Redirect
Redirect is when the partner redirects the customer to our domain to complete the checkout process. In this case you will take advantage of the checkout_url that is returned in the response to the Create Order. The pros to this approach are:

* Lowest development effort to setup, as do not need to setup a location for the iframe to be hosted.
* Best approach if you have not yet integrated with our Create Identity endpoint as otherwise it will force the customer's browser to be redirected to our site to complete KYC 

### Widget
Widget is when the partner hosts our site in an iframe to complete the checkout process. In this case you will take advantage of the checkout_iframe that is returned in the response. 
Note this will only be returned if you send the iframe_domain in the request body of the Create Order. The pros to this approach are:

* More integrated experience for your customer. For many payment methods the customer will not leave your site for the whole flow (see below for details).
* If used in collaboration with the checkout look and feel below it will feel as part of your own experience.

The Widget will force a redirect to have the order completed on our site in the following scenarios:

* If the browser does not support 3rd party cookies, e.g. Safari. In some browsers they have strict rules around what iframes of 3rd party sites can do, and one of them is the ability to store cookies. Because we require this to maintain state, if this cannot be saved the customer will be redirected to our site to complete.
* If the customer needs to complete KYC. Due to potential restrictions of accessing the camera from an iframe if the customer requires KYC then we will redirect to our site to complete this. Note that when the customer makes their next order it will all be done from the widget.
* If the customer uses iDEAL payment method. For security reasons, iDEAL does not work within iframes and therefore we need to redirect the customer in order to have them login into their bank. They will then be redirected to the order status page on our site where they can then redirect back to the partner site.
* If the customer uses Credit Card they will be redirected for the order status only. This is a limitation of our provider where they will redirect the customer to our site, where they can then go back to the partner page after viewing their status. 

The iframe should be loaded within a modal div and below is the recommended iframe format:

`
<iframe src="{checkout_iframe}" style="border:0; width: 100%; min-height: 80vh;"></iframe>
`

## Checkout Look and Feel
If you would like the colour scheme of your site to be reflected on Banxa then please get in touch with your account manager and provide the following colours for us to setup this customization:

* Primary Font Colour in HEX e.g #2D99FF
* Secondary Font Colour in HEX e.g. #2DAFA0
* Background Colour in HEX e.g. #121638
* Font Colour for out of app errors which will go directly on background (e.g. 404) in HEX e.g. #EDF2F7
* Footer Background Colour in HEX e.g. #FFFFFF
* Footer Font Colour in HEX e.g. #999999
* Base footer Font Colour in HEX e.g. #A0AEC0
* Border colour at the footer in HEX e.g. #EDF2F7
* Font Family e.g. Noto Sans, -apple-system, Helvetica Neue, sans-serif


# Headers

Please include the Accept header for Json as per the below in all requests

<aside style="background-color:#dddddd">
<p style="font-family:monospace; font-size: 1.1em; padding-top: 1em;">
"Accept: application/json"
</p>
</aside>


# Authentication

> Example authentication header (GET):

```shell
"Authorization: Bearer " + "PARTNER-API-KEY" + ":"
  + hmac_sha256('GET' + "\n" 
  + '/api/payment-methods?source=AUD' + "\n"
  + '1560227834'
  , "PARTNER-API-SECRET") + ":" + "1560227834"
```

> Example authentication header (POST):

```shell
"Authorization: Bearer " + "PARTNER-API-KEY" + ":"
  + hmac_sha256('POST' + "\n" 
  + '/api/orders' + "\n"
  + '1560227834' + "\n"
  + '{"account_reference":"partner_ref","coin_code":"BTC","wallet_address":"1BvBMSEYstWetqTFn5Au4m4GFg7xJaNVN2","return_url_on_success":"https://partner-site.com/callback/success"}'
  , "PARTNER-API-SECRET") + ":" + "1560227834"
```

Banxa uses HMAC Authentication to secure the API which utilises an API Key and Secret to hash the message payload. The API credentials will be provided as part of the on-boarding process.

For all of the requests, add an authentication digest to the header.
The authentication header structure must be in the following format:

<aside style="background-color:#dddddd">
<p style="font-family:monospace; font-size: 1.1em; padding-top: 1em;">
"Authorization: Bearer <strong>API_Key</strong>:<strong>Signature</strong>:<strong>Nonce</strong>"
</p>
</aside>

### API Key
The API public key credential. This is the one provided by us during on-boarding.

### Signature
Message authentication signature. The request message is hashed with the API secret key using SHA256 algorithm.
The structure of the message:
<ul>
 <li>Request method. (e.g. "GET or "POST")</li>
 <li>Request URI including the query parameters for GET.</li>
 <li>The <em>nonce</em> value (e.g. using unix timestamp value)</li>
 <li>The payload in JSON format only for POST.</li>
</ul>

<aside class="notice">
To ensure the signature is successfully validated, please be aware of the following:
<ul>
    <li>Each piece of information in the signature message should be separated by a new line. (see the example)</li>
    <li>The payload should not contain any whitespace between the names and values</li>
    <li>The Request URI should not contain the host name. e.g. `/api/orders` instead of `https://[partner].banxa.com/api/orders`</li>
</ul>
</aside>      

### Nonce
A numeric value with value greater than the one sent with the previous successful request. (e.g. unix timestamp)

# Currencies

## Get Fiat Currencies

> Example Request:

```shell
curl -H "Authorization: Bearer xxxxxxxx:xxxx-xxxx-xxxx:xxxxxxxx" \
  -H "Accept: application/json" \
  "https://[partner].banxa.com/api/fiats"
```

> Example Response:

```json
{
  "data" : {
    "fiats": [
      {
        "fiat_code": "AUD",
        "fiat_name": "Australian Dollar",
        "fiat_symbol": "A$"
      }
    ]  
  }
}
```

Retrieve all available fiat currencies

### Request

`GET https://[partner].banxa.com/api/fiats`

<aside class="notice">
Requires <strong>Authentication</strong>
</aside>      

### Response

Field | Description | Format
--------- | -------- | -----------
`data.fiats.fiat_code`    | The fiat currency | string
`data.fiats.fiat_name`    | The fiat currency full name | string
`data.fiats.fiat_symbol`    | The fiat currency symbol | string

## Get Crypto Currencies

> Example Request:

```shell
curl -H "Authorization: Bearer xxxxxxxx:xxxx-xxxx-xxxx:xxxxxxxx" \
  -H "Accept: application/json" \
  "https://[partner].banxa.com/api/coins"
```

> Example Response:

```json
{
  "data": {  
    "coins": [
      {
        "coin_code": "BTC",
        "coin_name": "Bitcoin"
      },
      {
        "coin_code": "ETH",
        "coin_name": "Ethereum"
      }
    ]
  }
}
```

Retrieve all available crypto currencies

### Request

`GET https://[partner].banxa.com/api/coins`

<aside class="notice">
Requires <strong>Authentication</strong>
</aside>      

### Response

Field | Description | Format
--------- | -------- | -----------
`data.coins.coin_code`    | The crypto currency | string
`data.coins.coin_name`    | The crypto currency full name | string

# Payment Methods

## Get Payment Methods

> Example Request:

```shell
curl -H "Authorization: Bearer xxxxxxxx:xxxx-xxxx-xxxx:xxxxxxxx" \
  -H "Accept: application/json" \
  "https://[partner].banxa.com/api/payment-methods?source=AUD"
```

> Example Response:

```json
{
  "data": {
    "payment_methods": [
      {
        "id": "1",
        "name": "POLi",
        "description": "POLi is an online payment option that allows you to use your internet banking system to securely buy crypto currency",
        "logo_url": "https://banxa.com/img/poli.png",
        "status": "ACTIVE",
        "type": "FIAT_TO_CRYPTO",
        "supported_fiat": ["AUD"],
        "supported_coin": ["BTC", "ETH"],
        "transaction_fees": [
          {
            "fiat_code": "AUD",
            "coin_code": "BTC",
            "fees": [
              {
                "name": "surcharge",
                "amount": 2,
                "type": "percentage"
              }
            ]
          }
        ],
        "transaction_limits": [
            {
              "fiat_code": "AUD",
              "min": 100,
              "max": 2000
            }
        ]
      },
      {
        "id": "2",
        "name": "Newsagent",
        "description": "Pay for your crypto in cash at your local newsagent",
        "logo_url": "https://banxa.com/img/newsagent.png",
        "status": "ACTIVE",
        "type": "FIAT_TO_CRYPTO",
        "supported_fiat": ["AUD"],
        "supported_coin": ["BTC", "ETH"],
        "transaction_fees": [
          {
            "fiat_code": "AUD",
            "coin_code": "BTC",
            "fees": [
              {
                "name": "surcharge",
                "amount": 1,
                "type": "percentage"
              }
            ]
          }
        ],
        "transaction_limits": [
            {
              "fiat_code": "AUD",
              "min": 100,
              "max": 9000
            }
        ]
      }
    ]
  }
}
```

Retrieve all possible payment methods or filter the available by a fiat or coin code. The type of exchange is determined based on the codes provided to the source and target.

### Request

`GET https://[partner].banxa.com/api/payment-methods`

<aside class="notice">
Requires <strong>Authentication</strong>
</aside>      

### Query Parameters

Parameter | Required | Description
--------- | -------- | -----------
`source` | No  | The source currency of type *string* e.g. 'AUD' for buy and 'BTC' for sell
`target` | No  | The target currency of type *string* e.g. 'BTC' for buy and 'AUD' for sell

### Response

Field | Description | Format
--------- | -------- | -----------
`data.payment_methods.id`    | The id of the payment method | string
`data.payment_methods.name`    | The name of the payment method | string
`data.payment_methods.description`    | Description of the payment method which can be displayed to customers | string
`data.payment_methods.logo_url`    | Url to get a logo for this payment method | uri
`data.payment_methods.status`    | ACTIVE or INACTIVE | string
`data.payment_methods.type`                   | The exchange type e.g. 'FIAT_TO_CRYPTO' | string
`data.payment_methods.supported_fiat`    | List of supported currencies | array&lt;string&gt;
`data.payment_methods.supported_coin`    | List of supported crypto coins | array&lt;string&gt;
`data.payment_methods.transaction_fees.fiat_code`    | The currency of the fee | string
`data.payment_methods.transaction_fees.coin_code`    | The coin this fee applies to | string
`data.payment_methods.transaction_fees.fees.name`    | The name of the fee | string
`data.payment_methods.transaction_fees.fees.amount`    | The amount of the fee based on the type, e.g. if 5 with percentage then read as 5% | number
`data.payment_methods.transaction_fees.fees.type`    | The fee type. One of percentage or fixed | string
`data.payment_methods.transaction_limits.fiat_code`    | The currency of the limits | number
`data.payment_methods.transaction_limits.min`    | The minimum order size for this payment method | number
`data.payment_methods.transaction_limits.max`    | The maximum order size for this payment method | number

# Price

## Get Prices

> Example Request (without filtering):

```shell
curl -H "Authorization: Bearer xxxxxxxx:xxxx-xxxx-xxxx:xxxxxxxx" \
  -H "Accept: application/json" \
  "https://[partner].banxa.com/api/prices?source_amount=200&source=AUD&target=BTC"
```

> Example Response:

```json
{
  "data": {
    "spot_price": 15402,
    "prices": [
      {
        "payment_method_id": "1",
        "type": "FIAT_TO_CRYPTO",
        "spot_price_fee": 770,
        "spot_price_including_fee": 14632,
        "coin_amount": 0.01230770,
        "coin_code": "BTC",
        "fiat_amount": 200,
        "fiat_code": "AUD",
        "fee_amount": 10
      },
      {
        "payment_method_id": "2",
        "type": "FIAT_TO_CRYPTO",
        "spot_price_fee": 1078,
        "spot_price_including_fee": 14324,
        "coin_amount": 0.01230026,
        "coin_code": "BTC",
        "fiat_amount": 200,
        "fiat_code": "AUD",
        "fee_amount": 14
      }
    ]
  }
}
```

> Example Request (with filtering):

```shell
curl -H "Authorization: Bearer xxxxxxxx:xxxx-xxxx-xxxx:xxxxxxxx" \
  -H "Accept: application/json" \
  "https://[partner].banxa.com/api/prices?source_amount=200&source=AUD&target=BTC&payment_method_id=1"
```

> Example Response:

```json
{
  "data": {
    "spot_price": 15402,
    "prices": [
      {
        "payment_method_id": "1",
        "type": "FIAT_TO_CRYPTO",
        "spot_price_fee": 770,
        "spot_price_including_fee": 14632,
        "coin_amount": 0.01230770,
        "coin_code": "BTC",
        "fiat_amount": 200,
        "fiat_code": "AUD",
        "fee_amount": 10
      }
    ]
  }  
}
```

Get prices for the different payment methods that Banxa offer.

### Request
`GET https://[partner].banxa.com/api/prices`

<aside class="notice">
Requires <strong>Authentication</strong>
</aside>      

### Query Parameters

Parameter | Required | Description
--------- | -------- | -----------
`source`                   | Yes  | The source currency code of type *string* e.g. 'AUD' for buy and 'BTC' for sell
`target`                   | Yes  | The target currency code of type *string*. e.g. 'BTC' for buy and 'AUD' for sell
`source_amount`                 | No  | The source amount of type *float*, cannot be sent with target_amount
`target_amount`                 | No  | The target amount of type *float*, cannot be sent with source_amount 
`payment_method_id`           | No  | The id of the payment method to get prices for

### Response

Field | Description | Format
--------- | -------- | -----------
`data.spot_price`        | The spot price of the cryptocurrency | number
`data.prices.payment_method_id`        | The id of the payment method | string
`data.prices.type`                   | The exchange type e.g. 'FIAT_TO_CRYPTO' | string
`data.prices.spot_price_fee`        | The fee charged on the spot price | string
`data.prices.spot_price_including_fee`        | The adjusted spot price including the fee | string
`data.prices.coin_amount`        | The amount of this crytocurrency to receive based on the fiat amount and fees | string
`data.prices.coin_code`        | The cryptocurrency code | string
`data.prices.fiat_amount`        | The fiat amount the customer is spending | string
`data.prices.fiat_code`        | The fiat currency | string
`data.prices.fee_amount`        | The fees calculated on the fiat amount | string

# Order

## Get Orders

> Example Request:

```shell
curl -H "Authorization: Bearer xxxxxxxx:xxxx-xxxx-xxxx:xxxxxxxx" \
  -H "Accept: application/json" \
  "https://[partner].banxa.com/api/orders?start_date=2020-06-01&end_date=2020-06-02&per_page=50"
```

> Example Response:

```json
{
  "data": {
    "orders": [
      {
          "id": "7f954c2eaea52db2d7e57d839620a764",
          "account_id": "564d914ba50d51d67095817f1559e0a7",
          "account_reference": "098f6bcd4621d373cade4e832627b4f6",
          "order_type": "CRYPTO-BUY",
          "payment_type": "POLI",
          "ref": 1,
          "fiat_code": "AUD",
          "fiat_amount": 200,
          "coin_code": "BTC",
          "coin_amount": 0.015,
          "wallet_address": "1LbQ1WNTsm1Nzj1hbh3WDCbEim1oUg5rfi",
          "fee": 4.3,
          "payment_fee": 0,
          "commission": 0.03,
          "tx_hash": "323bf75d66fd5a4fe8c903f950d81c5f0170bacd0f872cde8f4f3d6e7c94db35",
          "created_at": "2020-06-01 00:20:51",
          "status": "complete",
          "meta_data": "{'tracking_id': 'HSKJDGHKSJG0393LJKJF'}"
      }
    ]
  },
  "meta": {
    "current_page": 1,
    "from": 1,
    "last_page": 1,
    "per_page": 50,
    "to": 1,
    "total": 1
  }
}
```

Get details for orders that have been submitted for a time range and optionally by customer.

Start and End date must be dates of the format YYYY-MM-DD. The system times are all returned in UTC. We default the end time to be end of that day. 
So if you want just the 1st of June 2020, the start and end dates would be 2020-06-01. 

### Request
`GET https://[partner].banxa.com/api/orders`

<aside class="notice">
Requires <strong>Authentication</strong>
</aside>      

### Query Parameters

Parameter | Required | Description
--------- | -------- | -----------
`start_date`                   | Yes | The start date for the lookup. Format YYYY-MM-DD 
`end_date`                     | Yes | The end date for the lookup. Format YYYY-MM-DD
`per_page`                     | No  | This is the page size. It is defaulted to 100
`page`                         | No  | This is the page to get. The number of pages available is given in the meta object in the response
`account_reference`            | No  | This is the reference that was passed in when creating an order. Use this to get all orders for a customer

### Response

Field | Description | Format
--------- | -------- | -----------
`data.orders.id`        | The id of the order | number
`data.orders.account_id`| The account ID for the order | string
`data.orders.account_reference`| The account reference for the customer | string
`data.orders.order_type`      | Order type | string
`data.orders.payment_type`      | Payment type | string
`data.orders.ref`      | Order reference | string
`data.orders.fiat_code` | Fiat currency code | string
`data.orders.fiat_amount`     | Fiat currency value for the order | decimal
`data.orders.coin_code`       | Cryptocurrency code | string
`data.orders.coin_amount`     | Cryptocurrency value for the order | decimal
`data.orders.wallet_address`  | Cryptocurrency wallet address | string
`data.orders.wallet_address_tag`  | Cryptocurrency wallet address tag (if required) | string
`data.orders.fee`  | Order fee in fiat currency | string
`data.orders.fee_tax`  | Tax charged on the order fee in fiat currency | string
`data.orders.payment_fee`  | Payment fee in fiat currency | string
`data.orders.payment_fee_tax`  | Tax charged on the payment fee in fiat currency | string
`data.orders.commission`  | Commission amount in fiat currency | string
`data.orders.tx_hash`  | Blockchain transaction hash | string
`data.orders.tx_confirms`  | Number of blockchain confirmations | string
`data.orders.created_at`      | Timestamp when order was created in UTC time | string
`data.orders.created_date`      | Date when order was created in UTC time | string
`data.orders.status`    | Order status | string
`data.orders.merchant_fee`    | Merchant defined fee | string
`data.orders.merchant_commission`    | Merchant defined commission | string
`data.orders.meta_data` | Returning meta data that was passed in the init order | string
`meta.current_page`  | The page requested
`meta.last_page`  | The last page that can be requested
`meta.from`  | Index of the first item in this response
`meta.to`  | Index of the last item in this response
`meta.per_page`  | The max number of items per page
`meta.total`  | The total number of items found

## Get Order

> Example Request:

```shell
curl -H "Authorization: Bearer xxxxxxxx:xxxx-xxxx-xxxx:xxxxxxxx" \
  -H "Accept: application/json" \
  "https://[partner].banxa.com/api/orders/7f954c2eaea52db2d7e57d839620a764"
```

> Example Response:

```json
{
  "data": {
    "order": {
      "id": "7f954c2eaea52db2d7e57d839620a764",
      "account_id": "564d914ba50d51d67095817f1559e0a7",
	  "account_reference": "098f6bcd4621d373cade4e832627b4f6",
      "order_type": "CRYPTO-BUY",
      "payment_type": "POLI",
      "ref": 1,
      "fiat_code": "AUD",
      "fiat_amount": 200,
      "coin_code": "BTC",
      "coin_amount": 0.015,
      "wallet_address": "1LbQ1WNTsm1Nzj1hbh3WDCbEim1oUg5rfi",
      "fee": 4.3,
      "payment_fee": 0,
      "commission": 0.03,
      "tx_hash": "323bf75d66fd5a4fe8c903f950d81c5f0170bacd0f872cde8f4f3d6e7c94db35",
      "created_at": "16-Sep-2019 00:20:51",
      "status": "complete"
    }
  }
}
```

Get details for an order that has been submitted

### Request
`GET https://[partner].banxa.com/api/orders/{orderId}`

<aside class="notice">
Requires <strong>Authentication</strong>
</aside>      


### Response

Field | Description | Format
--------- | -------- | -----------
`data.order.id`        | The id of the order | number
`data.order.account_id`| The account ID for the order | string
`data.order.account_reference`| The account reference for the customer | string
`data.order.order_type`      | Order type | string
`data.order.payment_type`      | Payment type | string
`data.order.ref`      | Order reference | string
`data.order.fiat_code` | Fiat currency code | string
`data.order.fiat_amount`     | Fiat currency value for the order | decimal
`data.order.coin_code`       | Cryptocurrency code | string
`data.order.coin_amount`     | Cryptocurrency value for the order | decimal
`data.order.wallet_address`  | Cryptocurrency wallet address | string
`data.order.fee`  | Order fee in fiat currency | string
`data.order.payment_fee`  | Payment fee in fiat currency | string
`data.order.commission`  | Commission amount in fiat currency | string
`data.order.tx_hash`  | Blockchain transaction hash | string
`data.order.created_at`      | Timestamp when order was created in UTC time | string
`data.order.status`    | Order status | string


### Order Statuses

Below is a list of the possible order statuses. 

Status | Description
-------|------------
pendingPayment | Order has been created and is pending for payment to be made by customer
waitingPayment | Payment has been initiated by customer and is being verified
paymentReceived | Payment has been received and verified
inProgress | Payment has been received, order is being processed
coinTransferred | Payment has been received, order is being processed, cryptocurrency has been sent
complete | Cryptocurrency has been sent and have received confirmations
cancelled | Payment has been cancelled
declined | Payment has been declined
expired | Payment has not been made, order has been expired
refunded | Payment was made, but we were unable to complete the order due to our checks, so the customer was refunded

## Create Order

> Example Request:

```shell
curl -X POST "https://[partner].banxa.com/api/orders" \
  -H "Authorization: Bearer xxxxxxxx:xxxx-xxxx-xxxx:xxxxxxxx" \
  -H "Accept: application/json" \
  -d \
  '{"account_reference": "partner_ref",
    "source": "AUD",
    "target": "BTC",
    "wallet_address": "1BvBMSEYstWetqTFn5Au4m4GFg7xJaNVN2",
    "return_url_on_success": "https:\/\/partner-site.com\/callback\/success"}'
```

> Example Response:

```json
{
  "data": {
	"order": {
	  "id": "0164d962448fbd34f644ffd65624d8ef",
	  "account_id": "098f6bcd4621d373cade4e832627b4f6",
	  "account_reference": "098f6bcd4621d373cade4e832627b4f6",
      "order_type": "CRYPTO-BUY",
      "fiat_code": "AUD",
      "coin_code": "BTC",
      "wallet_address": "39Mn6uYF1C1ZHbi5KgmyAjrTPX5RCWThbp",
      "checkout_url": "https://[partner].banxa.com/portal?expires=xxx&oid=xxx&signature=xxx",
      "created_at": "16-May-2019 10:30:43"
	}
  }
}
```

This allows the partner to create an order in Banxa. Upon success of this request, the response will contain a checkout_url which will be unique for this order. 
The customer should be redirected to this URL to complete the checkout process. 
The URL will expire after 1 minute if a redirect does not occur. The type of order is determined based on the codes provided to the source and target.

The `account_reference` that is passed in the request should be unique per customer. This is used on our side to remember a customer. In order to avoid customers from having to enter personal information or KYC again please use the same string for each order from the same customer.

### Request
`POST https://[partner].banxa.com/api/orders`

<aside class="notice">
Requires <strong>Authentication</strong>
</aside>      

### Body Parameters

Parameter | Required | Description
--------- | -------- | -----------
`account_reference`           | Yes | Partner's account reference for the customer / user string.
`account_id`                  | No  | The account_id the order belongs to
`payment_method_id`           | No  | The payment method to be used for this order
`payment`                     | No  | The code for the payment method to be used for this order 
`fiat_amount`                 | No  | The fiat currency amount of type *string* e.g. '200' (**deprecated**)
`fiat_code`                   | No  | The fiat currency code of type *string* e.g. 'AUD' (**deprecated**)
`coin_amount`                 | No  | The cryptocurrency amount of type *string* e.g '0.12345678' (**deprecated**) 
`coin_code`                   | No | The cryptocurrency code of type *string*. e.g. 'BTC' (**deprecated**)
`source`                      | Yes  | The source currency code of type *string* e.g. 'AUD' for buy and 'BTC' for sell
`source_amount`               | Yes  | The source amount of type *string* e.g. '200'
`target`                      | Yes  | The target currency code of type *string*. e.g. 'BTC' for buy and 'AUD' for sell
`target_amount`               | No  | The target amount of type *string* e.g. '0.12345678'. This will be overridden if a source_amount is also passed based on our conversion calculation 
`wallet_address`              | Yes† | Wallet address of type *string*. We would prefer you to do the validation on your side.
`wallet_address_tag`          | Yes†† | Wallet address tag/memo of type *string*. This is required when sending wallet address for BNB (Memo) or XRP (Tag)
`return_url_on_success`     | Yes | The return URL when the customer completed the checkout process
`return_url_on_cancelled`   | No  | The return URL when the customer cancelled the checkout process
`return_url_on_failure`     | No  | The return URL when the customer failed to complete the checkout process
`iframe_domain`             | No  | The domain what will be authorised to display the returned checkout_iframe URL in an iframe e.g. example.com. This must be the exact domain so if this is on a subdomain we would expect sub.example.com
`meta_data`                 | No  | A free form string that the partner can use to send us any information that will be returned in the GET /orders request

<aside class="notice">
† Wallet address is only required for a buy. For a sell we will send back the wallet to send to in the response<br/>
†† Wallet address tag is only required for orders where the target is BNB or XRP. This is used to handle the additional Memo or Tag fields respectively that these coins require for sending. 
If this field is not included for order creation for these coins it will fail with error code 422
</aside>      
<aside class="notice">
fiat_amount, fiat_code, coin_amount and coin_code have all been deprecated in place of source_amount, source, target_amount and target to allow for a better BUY and SELL flow. 
The old ones will continue to work for now but will be removed at a later date. Please update accordingly.
</aside>      


### Response

Field | Description | Format
--------- | -------- | -----------
`data.order.id`        | Unique ID of the order| string
`data.order.account_id`| The account ID for the order | string
`data.order.account_reference`| Partner's account reference for the customer / user string. | string
`data.order.order_type`      | Payment type | string
`data.order.fiat_code` | Fiat currency code | string
`data.order.fiat_amount`     | Fiat currency value for the order | decimal
`data.order.coin_code`       | Cryptocurrency code | string
`data.order.coin_amount`     | Cryptocurrency value for the order | decimal
`data.order.wallet_address`  | Cryptocurrency wallet address. For buy this is the customers wallet. For sell this is our wallet to send crypto to | string
`data.order.created_at`      | Timestamp when order was created in UTC time | string
`data.order.checkout_url`    | The url redirect for the customer to complete the checkout process | string

# Identity

## Create Identity

> Example Request:

```shell
curl -X POST "https://[partner].banxa.com/api/identities" \
  -H "Authorization: Bearer xxxxxxxx:xxxx-xxxx-xxxx:xxxxxxxx" \
  -H "Accept: application/json" \
  -d \
'{
	"account_reference": "test001001",
    "mobile_number": "61431000001",
    "email": "test@bitcoin.com.au",
    "nationality": "AU",
    "customer_identity": {
        "given_name": "Joe",
        "surname": "Bloggs",
        "dob": "01-01-1990",
        "residential_address": {
            "street_number": "44",
            "street_name": "Gwynne",
            "street_type": "Street",
            "suburb": "Cremorne",
            "post_code": "3121",
            "state": "VIC",
            "country": "AU"
        }
    },
    "identity_documents": [
    	{
    	"type": "PASSPORT",
    	"data": {
    		"given_name": "Joe",
	    	"surname": "Bloggs",
	    	"dob": "01-01-1990",
	    	"gender": "M",
	    	"country": "AU",
	    	"number": "11111111",
	    	"expiry_date": "01-01-2030"
    	},
        "images": [{
            "link": "http://www.orimi.com/pdf-test.pdf"
        }],
    	"verification": {
    		"verified": true,
	    	"verified_at": "20-02-2020",
	    	"link": "http://testlink.com.au",
	    	"authorizer": "rapidid",
	    	"comment": "Test comment"
    	}
    }],
}'
```

> Example Response:

```json
{
    "data": {
        "account": {
            "account_id": "d3be8dfe1eea6f55725d5784a83d4ce9",
            "account_reference": "test001001"
        }
    }
}
```

This allows the partner to create a customer's identity in Banxa. This will allow us to pre-identify a customer based on KYC that the partner has completed already. 
Please speak to your account manager about setting up the processing rules. 

The `account_reference` that is passed in the request should be the same one passed when the order is created. 

Although certain properties are optional, they may still be required for us to complete the checkout process. If this is the 
case we may still ask customers for additional information in order to fulfil our regulatory requirements.

### Request
`POST https://[partner].banxa.com/api/identities`

<aside class="notice">
Requires <strong>Authentication</strong>
</aside>      

### Body Parameters

Parameter | Required | Description
--------- | -------- | -----------
`account_reference`           | Yes | Partner's account reference for the customer / user string.
`mobile_number`               | No | The customers mobile phone number
`email`                       | No | The customers email 
`nationality`                 | No | The nationality of the customer based on their documents
`customer_identity`           | No | The customers personal information
`customer_identity.given_name`                  | No | The customers first name
`customer_identity.middle_name`                 | No  | The customers middle name
`customer_identity.surname`                     | No | The customers last name 
`customer_identity.dob`                         | No | The customers date of birth (D-M-Y format)
`customer_identity.residential_address`         | No  | The customers address
`customer_identity.residential_address.street_number` | No | The number of the house on the street 
`customer_identity.residential_address.street_name`   | No | The name of the street without the number or type
`customer_identity.residential_address.street_type`   | No | The street type, e.g. Road, Street, etc
`customer_identity.residential_address.address_line_1`   | No | Address line 1 or street address. Basically a combination of street number, name and type
`customer_identity.residential_address.address_line_2`   | No | Additional address line
`customer_identity.residential_address.suburb`        | No | The suburb, town or city of the address
`customer_identity.residential_address.state`         | No | The state, county or principality of the address
`customer_identity.residential_address.post_code`     | No | The postal code or zip code of the address
`customer_identity.residential_address.country`       | Yes | The country of the address 
`customer_identity.verification` | No | The verification proof of the customers information
`customer_identity.verification.verified` | No | Whether or not the partner verification was successful (If not given assumed to be false) 
`customer_identity.verification.verified_at` | No | The date the document was last verified (D-M-Y format) 
`customer_identity.verification.authorizer` | No | The name of the 3rd party  (If not given assumed to be manual)
`customer_identity.verification.is_manual` | No | True if the information was manually verified by the partner and not the 3rd party (If not given assumed to be false)
`customer_identity.verification.link` | No | Link to the verification document provided by identity 3rd party 
`customer_identity.verification.comment` | No | Any additional comments that were added to this verification 
`identity_documents`          | No | A list of identity documents for the customer
`identity_documents.type`     | Yes | The type of the document (PASSPORT, DRIVING_LICENSE, IDENTIFICATION, SELFIE, PROOF_OF_ADDRESS, SOURCE_OF_FUNDS)
`identity_documents.images`     | No | The list of images that are of the original document
`identity_documents.images.link`     | No | The link to download the document of this type
`identity_documents.images.tag`     | No | An optional tag to describe this image, e.g. FRONT or BACK for driving license
`identity_documents.data`     | No | The data extracted from this document
`identity_documents.data.number`          | No | The number on the document
`identity_documents.data.country`         | No | The country on the document
`identity_documents.data.given_name`      | No | The first name on the document 
`identity_documents.data.middle_name`     | No  | The middle name on the document
`identity_documents.data.surname`         | No | The last name on the document
`identity_documents.data.dob`             | No | The date of birth on the document (D-M-Y format)
`identity_documents.data.gender`          | No | The gender on the document
`identity_documents.data.state`           | No | The state on the document
`identity_documents.data.expiry_date`     | No | The expiry date on the document (D-M-Y format)
`identity_documents.verification` | No | The verification proof of the document
`identity_documents.verification.verified` | No | Whether or not the partner verification was successful (If not given assumed to be false) 
`identity_documents.verification.verified_at` | No | The date the document was last verified (D-M-Y format) 
`identity_documents.verification.authorizer` | No | The name of the 3rd party  (If not given assumed to be manual) 
`identity_documents.verification.is_manual` | No | True if the information was manually verified by the partner and not the 3rd party (If not given assumed to be false)
`identity_documents.verification.link` | No | Link to the verification document provided by identity 3rd party 
`identity_documents.verification.comment` | No | Any additional comments that were added to this verification 

<strong> Customer Identity </strong>

Note: while all fields are not required, we do recommend you send at least given name, surname, dob and address. 
Otherwise we will request the rest of the information from the customer when they are redirected to our platform

With address we request you send either the street number, name and type or the address line 1 depending on how your addresses are formatted. 
To classify this as a complete address we also request you send the country, and where available the suburb, state and post code (or equivalent).

<strong> Identity Documents </strong>

Note: With the passport, driving license and identification types, we request you send the data field to provide the document information. These are not required with the others.

For the image link, this should ideally be a one time link to download the document that shows the information captured, to ensure it will not be downloaded by any other party.

<strong> Verification </strong>

For both customer and document information we request you identify the steps take to verify the authenticity. 
Preferably this should be a trusted 3rd party who have a good history of accurate KYC verification. We ask you to provide 
a link to the certificate provided by the 3rd party so that we may audit any KYC performed.

If the document was manually verified by your support team then the is_manual flag should be set and the authorizer field can be excluded. 
Please attach any additional information from your system in the comment field   

### Response

Field | Description | Format
--------- | -------- | -----------
`data.account.account_id`| The account ID for the customer | string
`data.account.account_reference`| Partner's account reference for the customer / user string. | string

# Callbacks

## Customer Return URL

The redirect URLs are used to navigate the customers back to the partner page when they complete the checkout process.
The redirect URL must be specified in **Create Order** API parameters. 

When the customer completes the checkout page the customer will be redirected to return_url_on_success.

If there are any issues completing the order then they will be redirected to return_url_on_failure.

If the customer cancels the flow at any point, they will be redirected to the return_url_on_cancelled.

# Changelog

## 09 Aug 2020

* Added meta_data to the Create Order request and Get Orders response

## 01 Jul 2020

* Added account_reference to the Get Order response

## 24 Jun 2020

* Added Body Parameter to Create Order for triggering the widget checkout
* Updated documentation to detail the different checkout approaches

## 21 Jul 2020

* Added Get Orders API documentation
* Added refunded order status

## 12 Jun 2020

* Added Body Parameter to Create Order for specifying the address tag for use with XRP and BNB coins
