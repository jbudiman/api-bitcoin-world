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

