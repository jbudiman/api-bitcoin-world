# Identity

## Create Identity
> Example Request:

```shell
curl -X POST "https://[partner].banxa.com/api/identities" \
  -H "Authorization: Bearer xxxxxxxx:xxxx-xxxx-xxxx:xxxxxxxx" \
  -H "Accept: application/json" \
  -d \
'{
	"account_reference": "test001001",
    "mobile_number": "61431000001",
    "email": "test@bitcoin.com.au",
    "nationality": "AU",
    "customer_identity": {
        "given_name": "Joe",
        "surname": "Bloggs",
        "dob": "01-01-1990",
        "residential_address": {
            "street_number": "44",
            "street_name": "Gwynne",
            "street_type": "Street",
            "suburb": "Cremorne",
            "post_code": "3121",
            "state": "VIC",
            "country": "AU"
        }
    },
    "identity_documents": [
    	{
    	"type": "PASSPORT",
    	"data": {
    		"given_name": "Joe",
	    	"surname": "Bloggs",
	    	"dob": "01-01-1990",
	    	"gender": "M",
	    	"country": "AU",
	    	"number": "11111111",
	    	"expiry_date": "01-01-2030"
    	},
        "images": [{
            "link": "http://www.orimi.com/pdf-test.pdf"
        }],
    	"verification": {
    		"verified": true,
	    	"verified_at": "20-02-2020",
	    	"link": "http://testlink.com.au",
	    	"authorizer": "rapidid",
	    	"comment": "Test comment"
    	}
    }],
}'
```
This allows the partner to create a customer's identity in Banxa. This will allow us to pre-identify a customer based on KYC that the partner has completed already. 
Please speak to your account manager about setting up the processing rules. 

The `account_reference` that is passed in the request should be the same one passed when the order is created. 

Although certain properties are optional, they may still be required for us to complete the checkout process. If this is the 
case we may still ask customers for additional information in order to fulfil our regulatory requirements.

### Request
`POST https://[partner].banxa.com/api/identities`

<aside class="notice">
Requires <strong>Authentication</strong>
</aside>      


### Body Parameters

Parameter | Required | Description
--------- | -------- | -----------
`account_reference`           | Yes | Partner's account reference for the customer / user string.
`mobile_number`               | No | The customers mobile phone number
`email`                       | No | The customers email 
`nationality`                 | No | The nationality of the customer based on their documents
`customer_identity`           | No | The customers personal information
`customer_identity.given_name`                  | No | The customers first name
`customer_identity.middle_name`                 | No  | The customers middle name
`customer_identity.surname`                     | No | The customers last name 
`customer_identity.dob`                         | No | The customers date of birth (D-M-Y format)
`customer_identity.residential_address`         | No  | The customers address
`customer_identity.residential_address.street_number` | No | The number of the house on the street 
`customer_identity.residential_address.street_name`   | No | The name of the street without the number or type
`customer_identity.residential_address.street_type`   | No | The street type, e.g. Road, Street, etc
`customer_identity.residential_address.address_line_1`   | No | Address line 1 or street address. Basically a combination of street number, name and type
`customer_identity.residential_address.address_line_2`   | No | Additional address line
`customer_identity.residential_address.suburb`        | No | The suburb, town or city of the address
`customer_identity.residential_address.state`         | No | The state, county or principality of the address
`customer_identity.residential_address.post_code`     | No | The postal code or zip code of the address
`customer_identity.residential_address.country`       | Yes | The country of the address 
`customer_identity.verification` | No | The verification proof of the customers information
`customer_identity.verification.verified` | No | Whether or not the partner verification was successful (If not given assumed to be false) 
`customer_identity.verification.verified_at` | No | The date the document was last verified (D-M-Y format) 
`customer_identity.verification.authorizer` | No | The name of the 3rd party  (If not given assumed to be manual)
`customer_identity.verification.is_manual` | No | True if the information was manually verified by the partner and not the 3rd party (If not given assumed to be false)
`customer_identity.verification.link` | No | Link to the verification document provided by identity 3rd party 
`customer_identity.verification.comment` | No | Any additional comments that were added to this verification 
`identity_documents`          | No | A list of identity documents for the customer
`identity_documents.type`     | Yes | The type of the document (PASSPORT, DRIVING_LICENSE, IDENTIFICATION, SELFIE, PROOF_OF_ADDRESS, SOURCE_OF_FUNDS)
`identity_documents.images`     | No | The list of images that are of the original document
`identity_documents.images.link`     | No | The link to download the document of this type
`identity_documents.images.tag`     | No | An optional tag to describe this image, e.g. FRONT or BACK for driving license
`identity_documents.data`     | No | The data extracted from this document
`identity_documents.data.number`          | No | The number on the document
`identity_documents.data.country`         | No | The country on the document
`identity_documents.data.given_name`      | No | The first name on the document 
`identity_documents.data.middle_name`     | No  | The middle name on the document
`identity_documents.data.surname`         | No | The last name on the document
`identity_documents.data.dob`             | No | The date of birth on the document (D-M-Y format)
`identity_documents.data.gender`          | No | The gender on the document
`identity_documents.data.state`           | No | The state on the document
`identity_documents.data.expiry_date`     | No | The expiry date on the document (D-M-Y format)
`identity_documents.verification` | No | The verification proof of the document
`identity_documents.verification.verified` | No | Whether or not the partner verification was successful (If not given assumed to be false) 
`identity_documents.verification.verified_at` | No | The date the document was last verified (D-M-Y format) 
`identity_documents.verification.authorizer` | No | The name of the 3rd party  (If not given assumed to be manual) 
`identity_documents.verification.is_manual` | No | True if the information was manually verified by the partner and not the 3rd party (If not given assumed to be false)
`identity_documents.verification.link` | No | Link to the verification document provided by identity 3rd party 
`identity_documents.verification.comment` | No | Any additional comments that were added to this verification 

<strong> Customer Identity </strong>

Note: while all fields are not required, we do recommend you send at least given name, surname, dob and address. 
Otherwise we will request the rest of the information from the customer when they are redirected to our platform

With address we request you send either the street number, name and type or the address line 1 depending on how your addresses are formatted. 
To classify this as a complete address we also request you send the country, and where available the suburb, state and post code (or equivalent).

<strong> Identity Documents </strong>

Note: With the passport, driving license and identification types, we request you send the data field to provide the document information. These are not required with the others.

For the image link, this should ideally be a one time link to download the document that shows the information captured, to ensure it will not be downloaded by any other party.

<strong> Verification </strong>

For both customer and document information we request you identify the steps take to verify the authenticity. 
Preferably this should be a trusted 3rd party who have a good history of accurate KYC verification. We ask you to provide 
a link to the certificate provided by the 3rd party so that we may audit any KYC performed.

If the document was manually verified by your support team then the is_manual flag should be set and the authorizer field can be excluded. 
Please attach any additional information from your system in the comment field.   

> Example Response:

```json
{
    "data": {
        "account": {
            "account_id": "d3be8dfe1eea6f55725d5784a83d4ce9",
            "account_reference": "test001001"
        }
    }
}
```
### Response

Field | Description | Format
--------- | -------- | -----------
`data.account.account_id`| The account ID for the customer | string
`data.account.account_reference`| Partner's account reference for the customer / user string. | string


