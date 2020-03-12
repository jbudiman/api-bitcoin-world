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
`wallet_address`              | Yes+ | Wallet address of type *string*. We would prefer you to do the validation on your side.
`return_url_on_success`     | Yes | The return URL when the customer completed the checkout process
`return_url_on_cancelled`   | No  | The return URL when the customer cancelled the checkout process
`return_url_on_failure`     | No  | The return URL when the customer failed to complete the checkout process

<aside class="notice">
+ Wallet address is only required for a buy. For a sell we will send back the wallet to send to in the response
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
    "given_name": "Joe",
    "middle_name": "",
    "surname": "Bloggs",
    "dob": "01-01-1990",
    "mobile_number": "61431000001",
    "email": "test@bitcoin.com.au",
    "nationality": "AU"
    "residential_address": {
    	"street_number": "44",
    	"street_name": "Gwynne",
    	"street_type": "STREET",
    	"suburb": "Cremorne",
    	"post_code": "3121",
    	"state": "VIC",
    	"country": "AU"
    },
    "identity_documents": [
    	{
    	"type": "Passport",
    	"data": {
    		"given_name": "Joe",
	    	"middle_name": "",
	    	"surname": "Bloggs",
	    	"dob": "01-01-1990",
	    	"gender": "M",
	    	"country": "AU",
	    	"number": "11111111",
	    	"expiry_date": "01-01-2030"
    	},
    	"link": "http://www.orimi.com/pdf-test.pdf",
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

### Request
`POST https://[partner].banxa.com/api/identities`

<aside class="notice">
Requires <strong>Authentication</strong>
</aside>      

### Body Parameters

Parameter | Required | Description
--------- | -------- | -----------
`account_reference`           | Yes | Partner's account reference for the customer / user string.
`given_name`                  | Yes | The customers first name
`middle_name`                 | No  | The customers middle name
`surname`                     | Yes | The customers last name 
`dob`                         | Yes | The customers date of birth (D-M-Y format)
`mobile_number`               | No | The customers mobile phone number
`email`                       | Yes | The customers email 
`nationality`                 | Yes | The nationality of the customer based on their documents
`residential_address`         | No  | The customers address
`residential_address.street_number` | Yes | The number of the house on the street 
`residential_address.street_name`   | Yes | The name of the street without the number or type
`residential_address.street_type`   | Yes | The street type, e.g. Road, Street, etc
`residential_address.suburb`        | Yes | The suburb, town or city of the address
`residential_address.state`         | Yes | The state, county or principality of the address
`residential_address.post_code`     | Yes | The postal code or zip code of the address
`residential_address.country`       | Yes | The country of the address 
`identity_documents`          | No | A list of identity documents for the customer
`identity_documents.type`     | Yes | The type of the document (PASSPORT, DRIVING_LICENSE, IDENTIFICATION)
`identity_documents.link`     | Yes | The link to download the document of this type
`identity_documents.data`     | No | The data extracted from this document
`identity_documents.data.number`          | Yes | The number on the document
`identity_documents.data.country`         | Yes | The country on the document
`identity_documents.data.given_name`      | Yes | The first name on the document 
`identity_documents.data.middle_name`     | No  | The middle name on the document
`identity_documents.data.surname`         | Yes | The last name on the document
`identity_documents.data.dob`             | Yes | The date of birth on the document (D-M-Y format)
`identity_documents.data.gender`          | No | The gender on the document
`identity_documents.data.state`           | No | The state on the document
`identity_documents.data.expiry_date`     | No | The expiry date on the document (D-M-Y format)
`identity_documents.verification` | No | The verification proof of the document
`identity_documents.verification.verified` | Yes | Whether of not the partner verification was successful 
`identity_documents.verification.verified_at` | Yes | The date the document was last verified (D-M-Y format) 
`identity_documents.verification.authorizer` | Yes | The name of the 3rd party or manual if manually verified 
`identity_documents.verification.link` | No | Link to the verification document provided by identity 3rd party 
`identity_documents.verification.comment` | No | Any additional comments that were added to this verification 

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
