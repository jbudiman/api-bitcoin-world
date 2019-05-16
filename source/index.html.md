---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: Yes
---

# Introduction

Welcome to the Bitcoin World partner integration API. 

This API documentation will guide partners through the integration process with Bitcoin World payment APIs and processes.


# Authentication

Bitcoin.World uses HMAC Authentication to secure the API which utilises an API Key and Secret to hash the message payload.  You will be provided with credentials as part of the on-boarding process.

A full explanation of the HMAC protocol will be provided.

# Accounts
The Api endpoints to manage accounts. 
## Get Account

```shell
curl "https://api.bitcoin.world/accounts"
  -H "Authorization: foobar"
```

> The above command returns JSON structured like this:
`GET https://api.bitcoin.world/accounts/id/<ACCOUNT_ID>`

```json
{
  "id": 1001,
  "status": "OK",
  "kyc": {
    "status": "incomplete",
    "reason": "missing document"
  }
}
```

This endpoint retrieves a customer's account details.

### HTTP Request
`GET http://api.bitcoin.world/accounts/<ACCOUNT_ID>` 
### Parameters
    _TBD_
## Create Account
This endpoint registers an account for a customer. The account registration requires a bare minimum of an email address 
and a mobile phone to create. 

On successful registration, an account id will be provided for the customer.

You maybe able to update customer details by adding KYC details. Adding KYC detail for the customer will allow them
to access greater tier benefits such as being able to purchase with more payments and with higher daily limit.
### HTTP Request
    POST http://api.bitcoin.world/accounts/
### Query Parameters
Parameter | Required | Description
--------- | -------- | -----------
mobile_phone    | Yes  | mobile numbers of type *string*. International format `61412708135`
email           | Yes  | email address of type *string*. 

# Rates
## Get 

> Example Request:

```shell
curl "https://api.bitcoin.world/rates"
  -H "Authorization: TBA"
```

> Example Response:

```json
{
	"code": 0,
	"message": "Success",
	"buyRates": {
		"AUD": {
			"BTC": {
				"commission": 0.0500,
				"value": 12613.00,
				"valueExcFee": 11955.49,
				"fee": 657.51,
				"feeExcTax": 597.74,
				"feeTax": 59.77,
				"flatFee": 0.00
			},
			"ETH": {
				"commission": 0.0500,
				"value": 381.00,
				"valueExcFee": 361.18,
				"fee": 19.82,
				"feeExcTax": 18.02,
				"feeTax": 1.80,
				"flatFee": 0.00
			}
		}
	},
	"sellRates": {
		"AUD": {
			"BTC": {
				"commission": 0.0000,
				"value": 11539.00,
				"valueExcFee": 11539.00,
				"fee": 0.00,
				"feeExcTax": 0.00,
				"feeTax": 0.00,
				"flatFee": 0.00
			},
			"ETH": {
				"commission": 0.0000,
				"value": 350.00,
				"valueExcFee": 350.00,
				"fee": 0.00,
				"feeExcTax": 0.00,
				"feeTax": 0.00,
				"flatFee": 0.00
			}
		}
	}
}
```

Gets the rate information for each crypto-currency and fiat combination, only the relevant currencies will be returned for each of the Buy and Sell functions.

### Request
`GET http://api.bitcoin.world/rates/`

### Response

Field | Description | Format
--------- | -------- | -----------
code  | The result code | integer
message  | The result status message| string
commission  | The commission percentage applied to the rate, expressed as a decimal value| decimal
value  | The value of 1 unit of crypto-currency (inclusive of commission fee) in fiat currency| decimal
valueExcFee  | The value of 1 unit of crypto-currency (exclusive of commission fee) in fiat currency| decimal
fee  | The fee component in fiat currency| decimal
feeExcTax  | The fee component (exclusive of taxes) in fiat currency | decimal
feeTax  | The fee tax component in fiat currency | decimal
flatFee  | The flat fee component in fiat currency| decimal

# Orders
The Api endpoints to manage order creation.
## Get Orders
Returns the order status by `order_id`. 
The `order_id` can be obtained after order has been created with the *Create Orders* API endpoint below.
### HTTP Request
`GET http://api.bitcoin.world/orders/<ORDER_ID>`
### Query Parameters
Parameter | Required | Description
--------- | -------- | -----------
order_id  | Yes     | The order id returned by `create orders` endpoint
## Create Coin Orders
This endpoint creates an order that includes bitcoins. 
When order is created, customer should be redirected to bitcoin.world url checkout page the url is indicated in the response.
The response will also contain an `order_id`, that can be used to check the order status.
### HTTP Request
`POST https://api.bitcoin.world/orders`
### Query Parameters
Parameter | Required | Description
--------- | -------- | -----------
account_id                  | Yes  | The account_id the order belongs to
fiat_amount                 | Yes  | the fiat currency amount of type *float* up to *2* decimal points.
fiat_code                   | Yes  | the fiat currency code of type *string* e.g. 'AUD'. Code must be offered in the rates API
coin_amount                 | No   | the cryptocurrency amount of type *float* up to *8* decimal points. e.g 0.12345678 
coin_code                   | No   | the cryptocurrency code of type *string*. e.g. 'BTC'. Code must be offered in the rates API
wallet_address              | No   | Wallet address of type *string*. We would prefer you to do the validation on your side.
*rate_id                    | No   | @internal Discuss with the team if this is necessary
callback_url_on_success     | Yes  |
callback_url_on_cancelled   | Yes  |
callback_url_on_failure     | Yes  |

<aside class="notice">
@todo Need example of the callbacks.
 
@todo GET/POST method
 
@todo add payload stucture

@todo add hash signature with API keys
</aside>

# Customers
The Api endpoints registers the customers KYC _(This can probably be combined with Accounts api)_
## Register Customer KYC
```shell
curl "http://api.bitcoinworld.com/customers"
  -H "Authorization: meowmeowmeow"
```

> The above command returns JSON structured like this:

```json
[
  {}
]
```
### HTTP Request
`POST http://example.com/api/customers`
### Query Parameters
Parameter | Required | Description
--------- | ------- | -----------
account_id          | Yes  | The account ID
given_name          | Yes  | The first name of the customer
middle_name         | No | The middle name of the customer, if any.
surname             | Yes  | The family name of the customer
flat_number         | No | The address flat number
street_number       | Yes  | The address street number
street_name         | Yes  | The address street name
street_type         | Yes  | The address street type
suburb              | Yes  | The address suburb
state               | Yes  | The address state
post_code           | Yes  | The address post code
country             | Yes  | The country of residence
dob                 | Yes  | The date of birth in *YYYY-MM-DD* format of type *string*
document_ids        | Yes  | The document identification number of type *string*
document_type       | No | The identification document of type *string*. e.g. *passport* 
document_file_urls  | Yes  | The document link image or pdf file(s) that is accessible by us.

You can add multiple file urls `document_file_urls` by adding JSON collection like below:

```json
  { 
    "files": [ 
      "https://example.com/id_front.jpg",
      "https://example.com/id_back.jpg"
      ]
  }
```

<aside class="success">
Must attach authentication header
</aside>




