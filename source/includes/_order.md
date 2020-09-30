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

