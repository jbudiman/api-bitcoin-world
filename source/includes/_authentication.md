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
