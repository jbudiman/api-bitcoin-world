# Introduction

Welcome to the Banxa partner integration API. 

This API documentation will guide you through the integration with Banxa payment APIs and processes.

<aside class="success">
All API responses are in <strong>JSON</strong> format and follow <a href="https://jsonapi.org">JSON:API</a> format
</aside>

Please contact your account representative or <a href="mailto:support@banxa.com">support@banxa.com</a> to receive your production and testing URLs and your API credentials.

The URLs will be of the format https://[partner].banxa.com and https://[partner].banxa-sandbox.com 

## Checkout Approaches
There are 2 approaches available to our partners for integrating with Banxa to take the customer through the on-ramp experience.

### Redirect
Redirect is when the partner redirects the customer to our domain to complete the checkout process. In this case you will take advantage of the checkout_url that is returned in the response to the Create Order. The pros to this approach are:

* Lowest development effort to setup, as do not need to setup a location for the iframe to be hosted.
* Best approach if you have not yet integrated with our Create Identity endpoint as otherwise it will force the customer's browser to be redirected to our site to complete KYC 

### Widget
Widget is when the partner hosts our site in an iframe to complete the checkout process. In this case you will take advantage of the checkout_iframe that is returned in the response. 
Note this will only be returned if you send the iframe_domain in the request body of the Create Order. The pros to this approach are:

* More integrated experience for your customer. For many payment methods the customer will not leave your site for the whole flow (see below for details).
* If used in collaboration with the checkout look and feel below it will feel as part of your own experience.

The Widget will force a redirect to have the order completed on our site in the following scenarios:

* If the browser does not support 3rd party cookies, e.g. Safari. In some browsers they have strict rules around what iframes of 3rd party sites can do, and one of them is the ability to store cookies. Because we require this to maintain state, if this cannot be saved the customer will be redirected to our site to complete.
* If the customer needs to complete KYC. Due to potential restrictions of accessing the camera from an iframe if the customer requires KYC then we will redirect to our site to complete this. Note that when the customer makes their next order it will all be done from the widget.
* If the customer uses iDEAL payment method. For security reasons, iDEAL does not work within iframes and therefore we need to redirect the customer in order to have them login into their bank. They will then be redirected to the order status page on our site where they can then redirect back to the partner site.
* If the customer uses Credit Card they will be redirected for the order status only. This is a limitation of our provider where they will redirect the customer to our site, where they can then go back to the partner page after viewing their status. 

The iframe should be loaded within a modal div and below is the recommended iframe format:

`
<iframe src="{checkout_iframe}" style="border:0; width: 100%; min-height: 80vh;"></iframe>
`

## Checkout Look and Feel
If you would like the colour scheme of your site to be reflected on Banxa then please get in touch with your account manager and provide the following colours for us to set up this customization:

Colour | Format | Example | Additional Information
---------- | ---- |-----|-----
Primary Colour | HEX | #2D99FF | Used throughout the app mainly for headers/buttons as primary app colour
Background Colour | HEX | #121638 | Main app background colour
Text color on background | HEX | #EDF2F7 | Used on 404/500 error pages
Footer Background Colour | HEX | #FFFFFF | Bottom footer background colour
Footer Font Colour | HEX | #999999 | Text color on the footer
Border colour at the footer | HEX | #EDF2F7 | Thin border used on the footer 
