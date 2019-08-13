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

This API documentation will guide partners through the integration with Banxa payment APIs and processes.

<aside class="success">
All API responses are in <strong>JSON</strong> format
</aside>

Please contact your account representative or <a href="mailto:support@banxa.com">support@banxa.com</a> to receive your production and testing URLs and your API credentials.

The URLs will be of the format https://partner.banxa.com and https://partner.banxa-sandbox.com 

# Authentication

> Example authentication header:

```shell
"Authentication: HMAC " + "PARTNER-API-KEY" + ":"
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
"Authentication: HMAC <strong>API_Key</strong>:<strong>Signature</strong>:<strong>Nonce</strong>"
</p>
</aside>

### API Key
The API public key credential. This is the one provided by us during on-boarding.
### Signature
Message authentication signature. The request message is hashed with the API secret key using SHA256 algorithm.
The structure of the message:
<ul>
 <li>Request method. (e.g. "GET or "POST")</li>
 <li>Request URI.</li>
 <li>The <em>nonce</em> value (e.g. using unix timestamp value)</li>
 <li>The payload in JSON format.</li>
</ul>

<aside class="notice">
To ensure the signature is successfully validated, please be aware of the following:
<ul>
    <li>Each piece of information in the signature message should be separated by a new line. (see the example)</li>
    <li>The payload should not contain any whitespace between the names and values</li>
    <li>The Request URI should not contain the host name. e.g. `/api/orders` instead of `https://partner.banxa.com/api/orders`</li>
</ul>
</aside>      

### Nonce
A numeric value with value greater than the one sent with the previous successful request. (e.g. unix timestamp)

# Order

## Create Order

> Example Request:

```shell
curl -X POST "https://partner.banxa-sandbox.com/api/orders" \
  -H "Authentication: HMAC xxxxxxxx:xxxx-xxxx-xxxx:xxxxxxxx" \
  -d \
  '{"account_reference": "partner_ref",
    "coin_code": "BTC",
    "wallet_address": "1BvBMSEYstWetqTFn5Au4m4GFg7xJaNVN2",
    "redirect_url_on_success": "https:\/\/partner-site.com\/callback\/success"}'
```

> Example Response:

```json
{
  	"status": "OK",
	"order": {
		"id": "0164d962448fbd34f644ffd65624d8ef",
		"account_id": "098f6bcd4621d373cade4e832627b4f6",
		"account_reference": "098f6bcd4621d373cade4e832627b4f6",
		"order_type": "CRYPTO-BUY",
		"coin_code": "BTC",
		"wallet_address": "39Mn6uYF1C1ZHbi5KgmyAjrTPX5RCWThbp",
        "checkout_url": "https://partner.banxa.com/portal?expires=xxx&oid=xxx&signature=xxx",
        "created_at": "16-May-2019 10:30:43"
	}
}
```

This allows the partner to create an order in Banxa. Upon success of this request, the response will contain a checkout_url which will be unique for this order. The customer should be redirected to this URL to complete the checkout process. The URL will expire after 1 minute if a redirect does not occur.

### Request
`POST https://partner.banxa-sandbox.com/api/orders`

<aside class="notice">
Requires <strong>Authentication</strong>
</aside>      

### Parameters

Parameter | Required | Description
--------- | -------- | -----------
`account_reference`           | Yes | Partner's account reference for the customer / user string.
`account_id`                  | No  | The account_id the order belongs to
`fiat_amount`                 | No  | The fiat currency amount of type *float* up to *2* decimal points.
`fiat_code`                   | No  | The fiat currency code of type *string* e.g. 'AUD'. Code must be offered in the rates API
`coin_amount`                 | No  | The cryptocurrency amount of type *float* up to *8* decimal points. e.g 0.12345678 
`coin_code`                   | Yes | The cryptocurrency code of type *string*. e.g. 'BTC'. Code must be offered in the rates API
`wallet_address`              | Yes  | Wallet address of type *string*. We would prefer you to do the validation on your side.
`redirect_url_on_success`     | Yes | The return URL when the customer completed the checkout process
`redirect_url_on_cancelled`   | No  | The return URL when the customer cancelled the checkout process
`redirect_url_on_failure`     | No  | The return URL when the customer failed to complete the checkout process

<aside class="notice">
    The redirect URLs should be JSON encoded, which will escape the forward slashes e.g. https://partner.com becomes https:\/\/partner.com
</aside>      

### Response

Field | Description | Format
--------- | -------- | -----------
`status`    | The result status message| string
`id`        | Unique ID of the order| string
`account_id`| The account ID for the order | string
`account_reference`| Partner's account reference for the customer / user string. | string
`order_type`      | Payment type | string
`fiat_code` | Fiat currency code | string
`fiat_amount`     | Fiat currency value for the order | decimal
`coin_code`       | Cryptocurrency code | string
`coin_amount`     | Cryptocurrency value for the order | decimal
`wallet_address`  | Cryptocurrency wallet address | string
`created_at`      | Timestamp when order was created in UTC time | string
`checkout_url`    | The url redirect for the customer to complete the checkout process | string

# Callbacks

## Customer Return URL

The redirect URLs are used to navigate the customers back to the partner page when they complete the checkout process.
The redirect URL must be specified in **Create Order** API parameters. 

When the customer completes the checkout page the customer will be redirected to redirect_url_on_success.

If there are any issues completing the order then they will be redirected to redirect_url_on_failure.

If the customer cancels the flow at any point, they will be redirected to the redirect_url_on_cancelled.
