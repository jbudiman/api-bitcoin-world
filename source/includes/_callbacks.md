# Callbacks

## Customer Return URL

The redirect URLs are used to navigate the customers back to the partner page when they complete the checkout process.
The redirect URL must be specified in **Create Order** API parameters. 

When the customer completes the checkout page the customer will be redirected to return_url_on_success.

If there are any issues completing the order then they will be redirected to return_url_on_failure.

If the customer cancels the flow at any point, they will be redirected to the return_url_on_cancelled.