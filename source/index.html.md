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

## Get Account

> Example Request:

```shell
curl "https://api.bitcoin.world/accounts/098f6bcd4621d373cade4e832627b4f6"
  -H "Authorization: TBA"
```

> Example Response:

```json
{
  "id": "098f6bcd4621d373cade4e832627b4f6",
  "status": "OK",
  "kyc": {
    "status": "incomplete",
    "reason": "missing document"
  }
}
```

This endpoint retrieves an account's details.

### Request

`GET https://api.bitcoin.world/accounts/<ACCOUNT_ID>` 

Parameter | Required | Description
--------- | -------- | -----------
account_id    | Yes  | The customer's account number.  

### Response

Field | Description | Format
--------- | -------- | -----------
status  | The result status message| string
id  | Account ID | string
kyc status  | Status of the account kyc| string
kyc reason  | Reason of kyc staus| string

<aside class="notice">
Must be registered first with *Create Account* API or else, the response will be treated as account does not exist.
</aside>



## Create Account

> Example Request:

```shell
curl "https://api.bitcoin.world/accounts"
  -H "Authorization: TBA"
```

> Example Response:

```json
{
  "id": "098f6bcd4621d373cade4e832627b4f6",
  "status": "OK"
}
```

This endpoint registers a new account. The account registration requires a bare minimum of an email address 
and a mobile phone to create. 

On successful registration, an account id will be provided.

You maybe able to update customer details by adding KYC details. Adding KYC detail for the customer will allow them
to access greater tier benefits such as being able to purchase with more payments and with higher daily limit.

### Request

`POST https://api.bitcoin.world/accounts/<ACCOUNT_ID>` 
    
Parameter | Required | Description
--------- | -------- | -----------
account_id      | Yes  | Customer's unique account identifier
mobile_phone    | Yes  | mobile numbers of type *string*. International format `61412708135`
email           | Yes  | email address of type *string*.
account_ref           | Yes  | Partner's account reference for the customer \ user *string*.

### Response

Field | Description | Format
--------- | -------- | -----------
status  | The result status message| string
id  | Account ID | string



## Add Account details

> Example Request:

```shell
curl "https://api.bitcoin.world/accounts/098f6bcd4621d373cade4e832627b4f6/details"
  -H "Authorization: TBA"
```

> Example Response:

```json
{
  "id": "098f6bcd4621d373cade4e832627b4f6",
  "status": "OK"
}
```

This endpoint allows details to be added to the account a new account.

### Request

`POST http://api.bitcoin.world/accounts/<ACCOUNT_ID>/details` 
    
Parameter | Required | Description
--------- | -------- | -----------
given_name      | Yes  | The first name of the customer
middle_name     | No | The middle name of the customer, if any.
surname         | Yes  | The family name of the customer
flat_number     | No | The address flat number
street_number   | Yes  | The address street number
street_name     | Yes  | The address street name
street_type     | Yes  | The address street type
suburb          | Yes  | The address suburb
state           | Yes  | The address state
post_code       | Yes  | The address post code
country         | Yes  | The country of residence
dob             | Yes  | The date of birth in *YYYY-MM-DD* format of type *string*

### Response

Field | Description | Format
--------- | -------- | -----------
status  | The result status message| string



## Add KYC Document 

> Example Request:

```shell
curl "https://api.bitcoin.world/accounts/098f6bcd4621d373cade4e832627b4f6/documents"
  -H "Authorization: TBA"
```

> Example Response:

```json
{
  "id": "098f6bcd4621d373cade4e832627b4f6",
  "status": "OK"
}
```

This endpoint allows KYC document data to be provided for the account

### Request

`POST http://api.bitcoin.world/accounts/<ACCOUNT_ID>/documents` 

Parameter | Required | Description
--------- | -------- | -----------
document_type       | Yes  | The identification document of type *string*. e.g. *passport*
document_number     | Yes  | The document identification number of type *string* 
document_file_name  | Yes  | The document identification number of type *string* 
document_file_urls  | Yes  | The document link image or pdf file(s) that is accessible by us.

### Response

Field | Description | Format
--------- | -------- | -----------
status  | The result status message| string


## Upload KYC Document

> Example Request:

```shell
curl -F 'document_file=@/storage/passport_id_front.png' \
    https://api.bitcoin.world/v1/accounts/098f6bcd4621d373cade4e832627b4f6/documents
```

> Example Response:

```json
{
  "id": "098f6bcd4621d373cade4e832627b4f6",
  "status": "OK"
}
```

This endpoint allows KYC document files to be be upload for the account

### Request

`PUT http://api.bitcoin.world/accounts/{ACCOUNT_ID}/documents`
    
Parameter | Required | Description
--------- | -------- | -----------
document_file     | Yes |

### Response

Field | Description | Format
--------- | -------- | -----------
status  | The result status message| string

        

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
  	"status": "OK",
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
status  | The result status message| string
commission  | The commission percentage applied to the rate, expressed as a decimal value| decimal
value  | The value of 1 unit of crypto-currency (inclusive of commission fee) in fiat currency| decimal
valueExcFee  | The value of 1 unit of crypto-currency (exclusive of commission fee) in fiat currency| decimal
fee  | The fee component in fiat currency| decimal
feeExcTax  | The fee component (exclusive of taxes) in fiat currency | decimal
feeTax  | The fee tax component in fiat currency | decimal
flatFee  | The flat fee component in fiat currency| decimal



# Orders
## Get

> Example Request:

```shell
curl "https://api.bitcoin.world/orders/0164d962448fbd34f644ffd65624d8ef"
  -H "Authorization: TBA"
```

> Example Response:

```json
{
  	"status": "OK",
	"order": {
		"id": "0164d962448fbd34f644ffd65624d8ef",
		"account_id": "098f6bcd4621d373cade4e832627b4f6",
		"order_type": "CRYPTO-BUY",
		"payment_type": "BPAY",
		"ref": 102412,
		"fiat_code": "AUD",
		"fiat_amount": 173,
		"coin_code": "BTC",
		"coin_amount": 0.01411267,
		"wallet_address": "39Mn6uYF1C1ZHbi5KgmyAjrTPX5RCWThbp",
		"fee": 8.86,
		"commission": 0.05,
		"txn": null,
		"created": "16-May-2019 10:30:43",
		"status": "pendingPayment"
	}
}
```

Get order details for a previously created order.

### Request
`GET http://api.bitcoin.world/orders/<ORDER_ID>`

Parameter | Required | Description
--------- | -------- | -----------
order_id  | Yes     | The order id returned by `create order` endpoint

### Response

Field | Description | Format
--------- | -------- | -----------
status  | The result status message| string
id  | Unique ID of the order| string
account_id  | The customer ID for the order | string
type  | Payment type | string
ref  | The refrence number that is comminucated to the end user | string
fiat_code  | Fiat currency code | string
fiat_amount  | Fiat currency value for the order | decimal
coin_code  | Cryptocurrency code | string
coin_amount | Cryptocurrency value for the order | decimal
wallet_address | Cryptocurrency wallet address | string
fee | fFe for the order | decimal
commission | Commission rate | decimal
txn | Blockchain transaction reference | string
created | Timestamp whe norder was created in UTC time | string
status | Staus of the order | string



## Create

> Example Request:

```shell
curl "https://api.bitcoin.world/orders"
  -H "Authorization: TBA"
```

> Example Response:

```json
{
  	"status": "OK",
	"order": {
		"id": "0164d962448fbd34f644ffd65624d8ef",
		"account_id": "098f6bcd4621d373cade4e832627b4f6",
		"order_type": "CRYPTO-BUY",
		"type": "BPAY",
		"ref": 102412,
		"fiat_code": "AUD",
		"fiat_amount": 173,
		"coin_code": "BTC",
		"coin_amount": 0.01411267,
		"wallet_address": "39Mn6uYF1C1ZHbi5KgmyAjrTPX5RCWThbp",
		"fee": 8.86,
		"commission": 0.05,
		"txn": null,
		"created": "16-May-2019 10:30:43",
		"status": "pendingPayment"
	}
}
```

Get order details for a previously created order.

### Request
`POST http://api.bitcoin.world/orders`

Parameter | Required | Description
--------- | -------- | -----------
account_id                  | Yes  | The account_id the order belongs to
fiat_amount                 | Yes  | The fiat currency amount of type *float* up to *2* decimal points.
fiat_code                   | Yes  | The fiat currency code of type *string* e.g. 'AUD'. Code must be offered in the rates API
coin_amount                 | No   | The cryptocurrency amount of type *float* up to *8* decimal points. e.g 0.12345678 
coin_code                   | No   | The cryptocurrency code of type *string*. e.g. 'BTC'. Code must be offered in the rates API
wallet_address              | No   | Wallet address of type *string*. We would prefer you to do the validation on your side.
callback_url_on_success     | Yes  |
callback_url_on_cancelled   | Yes  |
callback_url_on_failure     | Yes  |

### Response

Field | Description | Format
--------- | -------- | -----------
status  | The result status message| string
id  | Unique ID of the order| string
account_id  | The customer ID for the order | string
type  | Payment type | string
ref  | The refrence number that is comminucated to the end user | string
fiat_code  | Fiat currency code | string
fiat_amount  | Fiat currency value for the order | decimal
coin_code  | Cryptocurrency code | string
coin_amount | Cryptocurrency value for the order | decimal
wallet_address | Cryptocurrency wallet address | string
fee | fFe for the order | decimal
commission | Commission rate | decimal
txn | Blockchain transaction reference | string
created | Timestamp whe norder was created in UTC time | string
status | Staus of the order | string







