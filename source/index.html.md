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
  + '{"account_reference":"partner_ref","coin_code":"BTC","wallet_address":"1BvBMSEYstWetqTFn5Au4m4GFg7xJaNVN2","redirect_url_on_success":"https://partner-site.com/callback/success"}'
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
  "https://[partner].banxa.com/api/payment-methods"
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
        "info_url": "https://banxa.com/pay/poli",
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
        "info_url": "https://banxa.com/pay/newsagent",
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

Retrieve all possible payment methods or filter the available by a fiat or coin code. The type of exchange is determined based on the codes provided to the source and target. Currently only fiat codes are supported in source and crypto codes in target.

### Request

`GET https://[partner].banxa.com/api/payment-methods`

<aside class="notice">
Requires <strong>Authentication</strong>
</aside>      

### Query Parameters

Parameter | Required | Description
--------- | -------- | -----------
`source` | No  | The source currency of type *string* e.g. 'AUD'
`target` | No  | The target currency of type *string* e.g. 'BTC'

### Response

Field | Description | Format
--------- | -------- | -----------
`data.payment_methods.id`    | The id of the payment method | string
`data.payment_methods.name`    | The name of the payment method | string
`data.payment_methods.description`    | Description of the payment method which can be displayed to customers | string
`data.payment_methods.logo_url`    | Url to get a logo for this payment method | uri
`data.payment_methods.info_url`    | Url to direct user to get more information | uri
`data.payment_methods.status`    | ACTIVE or INACTIVE | string
`data.payment_methods.type`                   | The exchange type e.g. 'FIAT_TO_CRYPTO' | string
`data.payment_methods.supported_fiat`    | List of supported currencies | array&lt;string&gt;
`data.payment_methods.supported_coin`    | List of supported crypto coins | array&lt;string&gt;
`data.payment_methods.transaction_fees.fiat_code`    | The currency of the fee | string
`data.payment_methods.transaction_fees.coin_code`    | The coin this fee applies to | string
`data.payment_methods.transaction_fees.fees.name`    | The name of the fee | string
`data.payment_methods.transaction_fees.amount`    | The amount of the fee based on the type, e.g. if 5 with percentage then read as 5% | number
`data.payment_methods.transaction_fees.type`    | The fee type. One of percentage or fixed | string
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

Get prices for the different payment methods that Banxa offer. Currently only fiat codes are supported in source and crypto codes in target.

### Request
`GET https://[partner].banxa.com/api/prices`

<aside class="notice">
Requires <strong>Authentication</strong>
</aside>      

### Query Parameters

Parameter | Required | Description
--------- | -------- | -----------
`source`                   | Yes  | The source currency code of type *string* e.g. 'AUD'
`target`                   | Yes  | The target currency code of type *string*. e.g. 'BTC'
`source_amount`                 | No  | The source amount of type *float*, cannot be sent with target_amount
`target_amount`                 | No  | The target amount of type *float*, cannot be sent with source_amount 
`payment_method_id`           | No  | Wallet address of type *string*. We would prefer you to do the validation on your side.

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

## Create Order

> Example Request:

```shell
curl -X POST "https://[partner].banxa.com/api/orders" \
  -H "Authorization: Bearer xxxxxxxx:xxxx-xxxx-xxxx:xxxxxxxx" \
  -H "Accept: application/json" \
  -d \
  '{"account_reference": "partner_ref",
    "coin_code": "BTC",
    "wallet_address": "1BvBMSEYstWetqTFn5Au4m4GFg7xJaNVN2",
    "redirect_url_on_success": "https:\/\/partner-site.com\/callback\/success"}'
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
      "coin_code": "BTC",
      "wallet_address": "39Mn6uYF1C1ZHbi5KgmyAjrTPX5RCWThbp",
      "checkout_url": "https://[partner].banxa.com/portal?expires=xxx&oid=xxx&signature=xxx",
      "created_at": "16-May-2019 10:30:43"
	}
  }
}
```

This allows the partner to create an order in Banxa. Upon success of this request, the response will contain a checkout_url which will be unique for this order. The customer should be redirected to this URL to complete the checkout process. The URL will expire after 1 minute if a redirect does not occur.

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
`fiat_amount`                 | No  | The fiat currency amount of type *float* up to *2* decimal points.
`fiat_code`                   | No  | The fiat currency code of type *string* e.g. 'AUD'
`coin_amount`                 | No  | The cryptocurrency amount of type *float* up to *8* decimal points. e.g 0.12345678 
`coin_code`                   | Yes | The cryptocurrency code of type *string*. e.g. 'BTC'
`wallet_address`              | Yes | Wallet address of type *string*. We would prefer you to do the validation on your side.
`redirect_url_on_success`     | Yes | The return URL when the customer completed the checkout process
`redirect_url_on_cancelled`   | No  | The return URL when the customer cancelled the checkout process
`redirect_url_on_failure`     | No  | The return URL when the customer failed to complete the checkout process

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
`data.order.wallet_address`  | Cryptocurrency wallet address | string
`data.order.created_at`      | Timestamp when order was created in UTC time | string
`data.order.checkout_url`    | The url redirect for the customer to complete the checkout process | string

# Callbacks

## Customer Return URL

The redirect URLs are used to navigate the customers back to the partner page when they complete the checkout process.
The redirect URL must be specified in **Create Order** API parameters. 

When the customer completes the checkout page the customer will be redirected to redirect_url_on_success.

If there are any issues completing the order then they will be redirected to redirect_url_on_failure.

If the customer cancels the flow at any point, they will be redirected to the redirect_url_on_cancelled.
