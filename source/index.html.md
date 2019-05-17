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

> Sandbox Base URL

```bash
https://sandbox-api.bitcoin.world
```

> Live Base URL

```bash
https://api.bitcoin.world
```


Welcome to the Bitcoin World partner integration API. 

This API documentation will guide partners through the integration process with Bitcoin World payment APIs and processes.

All API responses are in JSON format

<aside class="notice">
The API docs is a work in progress. The content is not finalized yet. 
</aside>

# Authentication

Bitcoin.World uses HMAC Authentication to secure the API which utilises an API Key and Secret to hash the message payload.  You will be provided with credentials as part of the on-boarding process.

A full explanation of the HMAC protocol will be provided.



# Accounts

## Get Account

> Example Request:

```shell
curl "https://sandbox-api.bitcoin.world/accounts/098f6bcd4621d373cade4e832627b4f6"
  -H "Authorization: Bearer xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
```

> Example Response:

```json
{
  "status": "OK",
  "id": "098f6bcd4621d373cade4e832627b4f6",
  "account_ref":"unique-partner-reference",
  "kyc": {
    "status": "incomplete",
    "reason": "missing document"
  }
}
```

This endpoint retrieves an account's details.

### Request

`GET https://sandbox-api.bitcoin.world/accounts/{ACCOUNT_ID}` 

### Parameters

Parameter | Required | Description
--------- | -------- | -----------
`account_id`    | **Yes**  | The customer's account number.  

### Response

Field | Description | Format
--------- | -------- | -----------
`status`       | The result status message | string
`id`           | Account ID | string
`customer_ref` | Partner's unique customer reference | string
`kyc[status]`   | Status of the account KYC | string
`kyc[reason]`   | Reason of KYC status | string

<aside class="notice">
Must be registered with <strong>Create Account</strong> API beforehand.
</aside>
<aside class="warning">
Requires <strong>Authentication</strong>
</aside>

## Create Account
> Example Request *without* KYC details:

```shell
curl -X POST "https://sandbox-api.bitcoin.world/accounts" \
  -H "Authorization: Bearer xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx" \
  -H "Content-Type: application/json" \
  -d \
  '{"account_ref":"unique-partner-reference"
    "mobile_phone":"61400000000",
    "email":"email@example.com"}'  
```
> Example Request *with* KYC details:

```shell
curl -X POST "https://sandbox-api.bitcoin.world/accounts" \
  -H "Authorization: Bearer xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx" \
  -H "Content-Type: application/json" \
  -d \
  '{"account_ref":"unique-partner-reference",
    "mobile_phone":"61400000000",
    "email":"email@example.com",
    "given_name":"John",
    "middle_name":"",
    "surname":"Doe",
    "flat_number":"1",
    "street_number":"11",
    "street_name":"Somer",
    "street_type":"Street",
    "suburb":"City",
    "state":"NSW",
    "post_code":"2000",
    "country":"Australia",
    "date_of_birth":"1980-12-31"}'  
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

On successful registration, the api will return an *account id*.

To create an account, the following parameters *a customer_ref, a mobile number* and *an email address* are required.
If no KYC details are provided, the customer will be prompted with a form to enter the KYC details when redirected
to our checkout cart.
 
To improve user experience, we recommend our partners to provide the customer's KYC details during 
account creation. The customer will not be prompted to enter the KYC details in our checkout cart. 

### Request

`POST https://sandbox-api.bitcoin.world/accounts/` 

### Parameters   

Parameter | Required | Description
--------- | -------- | -----------
`customer_ref`    | **Yes**  | Partner's account reference for the customer / user *string*.
`mobile_phone`    | **Yes**  | mobile numbers of type *string*. International format `61412708135`
`email`           | **Yes**  | email address of type *string*.
`given_name`      | No   | The first name of the customer
`middle_name`     | No   | The middle name of the customer, if any.
`surname`         | No   | The family name of the customer
`flat_number`     | No   | The address flat number
`street_number`   | No   | The address street number
`street_name`     | No   | The address street name
`street_type`     | No   | The address street type
`suburb`          | No   | The address suburb
`state`           | No   | The address state
`post_code`       | No   | The address post code
`country`         | No   | The country of residence
`date_of_birth`   | No   | The date of birth in *YYYY-MM-DD* format of type *string*

### Response

Field | Description | Format
--------- | -------- | -----------
`status`  | The result status message| string
`id`      | Account ID | string

<aside class="warning">
Requires <strong>Authentication</strong>
</aside>      

## Create KYC Details

> Example Request:

```shell
curl -X POST "https://sandbox-api.bitcoin.world/accounts/098f6bcd4621d373cade4e832627b4f6/details" \
  -H "Authorization: Bearer xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx" \
  -H "Content-Type: application/json" \
  -d \
  '{"given_name":"John",
    "middle_name":"",
    "surname":"Doe",
    "flat_number":"1",
    "street_number":"11",
    "street_name":"Somer",
    "street_type":"Street",
    "suburb":"City",
    "state":"NSW",
    "post_code":"2000",
    "country":"Australia",
    "date_of_birth":"1980-12-31"}'  
```

> Example Response:

```json
{
  "id": "098f6bcd4621d373cade4e832627b4f6",
  "status": "OK"
}
```

This endpoint allows kyc details to be added and/or modified to the account. 

### Request

`POST http://sandbox-api.bitcoin.world/accounts/{ACCOUNT_ID}/details` 

### Parameters
    
Parameter | Required | Description
--------- | -------- | -----------
`mobile_phone`    | No | mobile numbers of type *string*. International format `61412708135`
`email`           | No | email address of type *string*.
`given_name`      | **Yes**  | The first name of the customer
`middle_name`     | No | The middle name of the customer, if any.
`surname`         | **Yes**  | The family name of the customer
`flat_number`     | No | The address flat number
`street_number`   | **Yes**  | The address street number
`street_name`     | **Yes**  | The address street name
`street_type`     | **Yes**  | The address street type
`suburb`          | **Yes**  | The address suburb
`state`           | **Yes**  | The address state
`post_code`       | **Yes**  | The address post code
`country`         | **Yes**  | The country of residence
`date_of_birth`   | **Yes**  | The date of birth in *YYYY-MM-DD* format of type *string*

### Response

Field | Description | Format
--------- | -------- | -----------
`status`  | The result status message| string

<aside class="warning">
Requires <strong>Authentication</strong>
</aside>      

# Documents
## Create Document

> Example Request:

```shell
curl -X POST "https://sandbox-api.bitcoin.world/documents" \
  -H "Authorization: Bearer xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx" \
  -H "Content-Type: application/json" \
  -d ' \
    {"account_id":"098f6bcd4621d373cade4e832627b4f6", \
    "document_number":"A 123456", \
    "document_filename":"passport_file_front.jpeg", \
    "document_type":"passport"}'  
```

> Example Response:

```json
{
  "id": "098f6bcd4621d373cade4e832627b4f6",
  "status": "OK",
  "upload_uri": "https://sandbox-api.bitcoin.world/accounts/098f6bcd4621d373cade4e832627b4f6/documents"
}
```

This endpoint accepts KYC document file.

### Request

`POST http://sandbox-api.bitcoin.world/documents/` 

### Parameters

Parameter | Required | Description
--------- | -------- | -----------
account_id      | **Yes**  | 
document_number | **Yes**  | The document identification number 
document_file   | **Yes**  | The document filename 
document_type   | No  | The identification document 

### Response

Field | Description | Format
--------- | -------- | -----------
`status`  | The result status message| string
`document_ref` | ID Reference for upload | string
`upload_uri` | API endpoint for document uploads | string

<aside class="warning">
Requires <strong>Authentication</strong>
</aside>      

## Upload Documents

> Example Request:

```shell
curl -F 'document_file[]=@/path/to/file/passport_id.png' \
    https://sandbox-api.bitcoin.world/v1/documents/21d373cad/upload
```

> Example Request uploading multiple files:

```shell
curl -X POST https://sandbox-api.bitcoin.world/v1/documents/21d373cad/upload 
    -F 'document_file[]=@/path/to/file/passport_id_front.png' \
    -F 'document_file[]=@/path/to/file/passport_id_back.png'
```

> Example Response:

```json
{
  "status": "OK"
}
```

This endpoints accepts KYC document of image file types. Must be initialized with **Create Document** API to obtain the 
upload reference `document_ref`

### Request

`POST http://sandbox-api.bitcoin.world/documents/{DOCUMENT_REF}/upload`

### Parameters
    
Parameter | Required | Description
--------- | -------- | -----------
`document_ref`    | **Yes** | ID reference 
`document_files`  | **Yes** | One or more files. Accepted file extensions: `.png`, `.jpg`, `.jpeg`

### Response

Field | Description | Format
--------- | -------- | -----------
`status`  | The result status message| string

<aside class="notice">
Must be initialized with <strong>Create Documents</strong> API beforehand.
</aside>
<aside class="warning">
Requires <strong>Authentication</strong>
</aside>        

# Rates
## Get Rates

> Example Request:

```shell
curl "https://sandbox-api.bitcoin.world/rates"
  -H "Authorization: Bearer xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
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
				"value_exc_fee": 11955.49,
				"fee": 657.51,
				"fee_fxc_tax": 597.74,
				"fee_tax": 59.77,
				"flat_fee": 0.00
			},
			"ETH": {
				"commission": 0.0500,
				"value": 381.00,
				"value_exc_fee": 361.18,
				"fee": 19.82,
				"fee_exc_tax": 18.02,
				"fee_tax": 1.80,
				"flat_fee": 0.00
			}
		}
	},
	"sellRates": {
		"AUD": {
			"BTC": {
				"commission": 0.0000,
				"value": 11539.00,
				"value_fxc_fee": 11539.00,
				"fee": 0.00,
				"fee_exc_tax": 0.00,
				"fee_tax": 0.00,
				"flat_fee": 0.00
			},
			"ETH": {
				"commission": 0.0000,
				"value": 350.00,
				"value_exc_fee": 350.00,
				"fee": 0.00,
				"fee_exc_tax": 0.00,
				"fee_tax": 0.00,
				"flat_fee": 0.00
			}
		}
	}
}
```

Gets the rate information for each crypto-currency and fiat combination, only the relevant currencies will be returned for each of the Buy and Sell functions.

### Request
`GET http://sandbox-api.bitcoin.world/rates/`

### Response

Field | Description | Format
--------- | -------- | -----------
`status`        | The result status message| string
`commission`    | The commission percentage applied to the rate, expressed as a decimal value| decimal
`value`         | The value of 1 unit of crypto-currency (inclusive of commission fee) in fiat currency| decimal
`value_exc_fee` | The value of 1 unit of crypto-currency (exclusive of commission fee) in fiat currency| decimal
`fee`           | The fee component in fiat currency| decimal
`fee_exc_tax`   | The fee component (exclusive of taxes) in fiat currency | decimal
`fee_tax`       | The fee tax component in fiat currency | decimal
`flat_fee`      | The flat fee component in fiat currency| decimal

<aside class="warning">
Requires <strong>Authentication</strong>
</aside>      

# Orders
## Get Order

> Example Request:

```shell
curl "https://sandbox-api.bitcoin.world/orders/0164d962448fbd34f644ffd65624d8ef"
  -H "Authorization: Bearer xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
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
`GET http://sandbox-api.bitcoin.world/orders/<ORDER_ID>`

### Parameters

Parameter | Required | Description
--------- | -------- | -----------
`order_id`  | Yes     | The order id returned by `create order` endpoint

### Response

Field | Description | Format
--------- | -------- | -----------
`status`        | The result status message| string
`id`            | Unique ID of the order| string
`account_id`    | The customer ID for the order | string
`type`          | Payment type | string
`ref`           | The reference number that is comminucated to the end user | string
`fiat_code`     | Fiat currency code | string
`fiat_amount`   | Fiat currency value for the order | decimal
`coin_code`     | Cryptocurrency code | string
`coin_amount`   | Cryptocurrency value for the order | decimal
`wallet_address` | Cryptocurrency wallet address | string
`fee`           | fFe for the order | decimal
`commission`    | Commission rate | decimal
`txn`           | Blockchain transaction reference | string
`created`       | Timestamp whe norder was created in UTC time | string
`status`        | Status of the order | string

<aside class="warning">
Requires <strong>Authentication</strong>
</aside>      

## Create Order

> Example Request (*Cryptocurrency Order*):

```shell
curl -X POST "https://sandbox-api.bitcoin.world/orders" \
  -H "Authorization: Bearer xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx" \
  -d \
  '{"account_id": "098f6bcd4621d373cade4e832627b4f6",
    "fiat_amount": "10000",
    "fiat_code": "AUD",
    "coin_amount": "1",
    "coin_code": "BTC",
    "wallet_address": "1BvBMSEYstWetqTFn5Au4m4GFg7xJaNVN2",
    "callback_url_on_success": "https://partner-site.com/callback/success",
    "callback_url_on_cancelled": "https://partner-site.com/callback/cancelled",
    "callback_url_on_failure": "https://partner-site.com/callback/failure"}'
```

> Example Request (*Payment Processor Only*):

```shell
curl -X POST "https://sandbox-api.bitcoin.world/orders" \
  -H "Authorization: Bearer xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx" \
  -d \
  '{"account_id": "098f6bcd4621d373cade4e832627b4f6",
    "fiat_amount": "10000",
    "fiat_code": "AUD",
    "callback_url_on_success": "https://partner-site.com/callback/success",
    "callback_url_on_cancelled": "https://partner-site.com/callback/cancelled",
    "callback_url_on_failure": "https://partner-site.com/callback/failure"}'
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
`POST http://sandbox-api.bitcoin.world/orders`

### Parameters

Parameter | Required | Description
--------- | -------- | -----------
`account_id`                  | Yes  | The account_id the order belongs to
`fiat_amount`                 | Yes  | The fiat currency amount of type *float* up to *2* decimal points.
`fiat_code`                   | Yes  | The fiat currency code of type *string* e.g. 'AUD'. Code must be offered in the rates API
`coin_amount`                 | No   | The cryptocurrency amount of type *float* up to *8* decimal points. e.g 0.12345678 
`coin_code`                   | No   | The cryptocurrency code of type *string*. e.g. 'BTC'. Code must be offered in the rates API
`wallet_address`              | No   | Wallet address of type *string*. We would prefer you to do the validation on your side.
`callback_url_on_success`     | Yes  | The return URL when the customer completed the checkout process
`callback_url_on_cancelled`   | Yes  | The return URL when the customer cancelled the checkout process
`callback_url_on_failure`     | Yes  | The return URL when the customer failed to complete the checkout process

### Response

Field | Description | Format
--------- | -------- | -----------
`status`    | The result status message| string
`id`        | Unique ID of the order| string
`account_id`| The customer ID for the order | string
`type`      | Payment type | string
`ref`       | The reference number that is communicated to the end user | string
`fiat_code` | Fiat currency code | string
`fiat_amount` | Fiat currency value for the order | decimal
`coin_code` | Cryptocurrency code | string
`coin_amount` | Cryptocurrency value for the order | decimal
`wallet_address` | Cryptocurrency wallet address | string
`fee`       | fee for the order | decimal
`commission`| Commission rate | decimal
`txn`       | Blockchain transaction reference | string
`created`   | Timestamp when order was created in UTC time | string
`status`    | Status of the order | string

<aside class="warning">
Requires <strong>Authentication</strong>
</aside>      

# Callbacks

## Customer Return URL

Callbacks are used to redirect the customers back to the partner page when they complete the checkout process.
The callback url must be specified in **Create Order** API parameters. 
When the customer completes the checkout page,  callback_url_on_success

## Payment Notification

As soon as a payment has been confirmed and received, we can notify our partners through their webhook.

### Payloads

Field | Description | Format
--------- | -------- | -----------
`order_id`      | The order id of the completed payment
`fiat_amount`   | The received fiat amount
`currency_code` | The currency of the amount
`received_at`   | the timestamp of received payment UTC+0








