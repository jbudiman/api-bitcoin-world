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
        "payment_method_id": 1,
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
        "payment_method_id": 2,
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
        "payment_method_id": 1,
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
`data.prices.payment_method_id`        | The id of the payment method | number
`data.prices.type`                   | The exchange type e.g. 'FIAT_TO_CRYPTO' | string
`data.prices.spot_price_fee`        | The fee charged on the spot price | string
`data.prices.spot_price_including_fee`        | The adjusted spot price including the fee | string
`data.prices.coin_amount`        | The amount of this crytocurrency to receive based on the fiat amount and fees | string
`data.prices.coin_code`        | The cryptocurrency code | string
`data.prices.fiat_amount`        | The fiat amount the customer is spending | string
`data.prices.fiat_code`        | The fiat currency | string
`data.prices.fee_amount`        | The fees calculated on the fiat amount | string