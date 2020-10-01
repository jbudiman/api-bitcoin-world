# Errors

If any request returns an error it will be in the following format:

> Example Response:

```json
{
  "errors": [
    {
      "status": "401",
      "code": "40103",
      "title": "You are not authorized to access this resource."
    }
  ]
}
```

### Error Responses

Field | Description | Format
--------- | -------- | -----------
`errors.status`    | The HTTP Status code of the error | string
`errors.code`    | (optional) The internal code of the error. Use this to communicate with Banxa Support | string
`errors.title`    | The title for the error | string
`errors.description`    | (optional) A more detailed description of the error | string

The API uses the following HTTP Statuses:

Status | Meaning
---------- | -------
200 | Successful -- Your request was successful
400 | Bad Request -- Your request is invalid.
401 | Unauthorized -- Your API key is incorrect or the packet was malformed.
403 | Forbidden -- The request is not allowed.
404 | Not Found -- The resource could not be found.
405 | Method Not Allowed -- You tried to access an invalid method.
406 | Not Acceptable -- You requested a format that isn't json.
410 | Gone -- The requested resource has been removed from our servers.
422 | Invalid request 
429 | Too Many Requests 
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarily offline for maintenance. Please try again later.

The API uses the following Error Codes:

Code | Meaning
---------- | -------
1    | Order not found
227  | Customer's account has temporarily been placed on hold
228  | Customer has reached their payment limit
305  | We are waiting for funds to arrive for a customer from a previous order
999  | System error
4002 | Validation error
4007 | The selected payment is not available
40001 | Hmac Error - Nonce must be a valid unix timestamp
40002 | Hmac Error - Nonce time is too different from our system. Please regenerate nonce using current UTC
40003 | Hmac Error - Nonce has been used before. Please regenerate the nonce and try again
40100 | Hmac Error - Merchant not found
40101 | Hmac Error - Auth Digest is malformed
40102 | Hmac Error - Authentication header is missing
40103 | Hmac Error - Payload signature can not be authenticated
40104 | Hmac Error - Invalid API key

