# Currencies

## Get Fiat Currencies

Retrieve all available fiat currencies


### Request

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

Retrieve all available crypto currencies

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
