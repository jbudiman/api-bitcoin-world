# Countries

## Get Countries

Retrieve all available countries


### Request

> Example Request:

```shell
curl -H "Authorization: Bearer xxxxxxxx:xxxx-xxxx-xxxx:xxxxxxxx" \
  -H "Accept: application/json" \
  "https://[partner].banxa.com/api/countries"
```

> Example Response:

```json
{
  "data" : {
    "countries": [
      {
        "country_code": "AU",
        "country_name": "Australia"
      }
    ]  
  }
}
```

`GET https://[partner].banxa.com/api/countries`

<aside class="notice">
Requires <strong>Authentication</strong>
</aside>      

### Response

Field | Description | Format
--------- | -------- | -----------
`data.countries.country_code`    | The ISO 2 country code | string
`data.countries.country_name`    | The country full name | string

## Get US States

Retrieve all available US States

> Example Request:

```shell
curl -H "Authorization: Bearer xxxxxxxx:xxxx-xxxx-xxxx:xxxxxxxx" \
  -H "Accept: application/json" \
  "https://[partner].banxa.com/api/countries/us/states"
```

> Example Response:

```json
{
  "data": {  
    "states": [
      {
        "state_code": "AL",
        "state_name": "Alabama"
      },
      {
        "state_code": "AK",
        "state_name": "Alaska"
      }
    ]
  }
}
```

### Request

`GET https://[partner].banxa.com/api/countries/us/states`

<aside class="notice">
Requires <strong>Authentication</strong>
</aside>      

### Response

Field | Description | Format
--------- | -------- | -----------
`data.states.state_code`    | 2 letter state code | string
`data.states.state_name`    | The state name | string
