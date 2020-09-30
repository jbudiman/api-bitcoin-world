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
227  | Customer's account has temporarily been placed on hold
228  | Customer has reached their payment limit
305  | We are waiting for funds to arrive for a customer from a previous order
4007 | The selected payment is not available

