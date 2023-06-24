## Step 1: Get available payment method(s)

When a cashier is ready to collect payment on terminal, get a list of the available payment method(s).

Although we highly recommend using the below endpoint to ensure you always offer the most up- to-date payment method(s) list to your customer, the step is optional. You can also cache the API response and update it once a week.

From your server, you can make an HTTP GET request to EVO Cloud endpoint */g2/v1/payment/mer/{sid}/paymentMethod* and specify either of the parameters below.

<table>
    <tr>
        <th>Query parameter</th>
        <th>Required</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>paymentBrand</td>
        <td>O</td>
        <td>The specific Payment Brand which you intend to provide to user. With this parameter, you will get the available payment method(s) and payment information for this specific Payment Brand in response.</td>
    </tr>
    <tr>
        <td>currency</td>
        <td>O</td>
        <td>The specific currency which the user intends to use to pay. With this parameter, you will get the available payment method(s) and payment information for this specific currency in response.</td>
    </tr>
</table>

Only one parameter above is allowed to be used for each request. If multiple parameters are sent,
the system will reply you with a priority of paymentBrand, and then currency.

If no query parameter is specified, you will get all the payment method(s) and payment information required to be collected based on your integration with EVO Cloud.

Here is an example of requesting the API without any query parameter:

```bash
curl https://{EVO_Cloud_DOMAIN_NAME.com}/g2/v1/payment/mer/{sid}/paymentMethod \
-H "Content-Type: application/json" \
-H "DateTime: 2021-12-31T08:30:59+0800" \
-H "MsgID: 2d21a5715c034efb7e0aa383b885fc7a" \
-H "SignType: SHA256" \
-H "Authorization: YOUR_MESSAGE_SIGNATURE"
```

The response includes the list of available payment method(s) in a containing below fields in each payment method.

* brandName: The brand name of the payment method.

* brandImage: The logo image of the payment method, a URL for you to get the logo to paymentBrandList array, display on your payment page.

* paymentScenario: The supported scenario of the payment method.

* promotionInfo: The list of the promotion names supported by the payment method.

See the example of the response [here](./in-store-paymentmethod-response-example.md).

If you want to display the payment methods' logo on your terminal, you can use paymentMethod.paymentBrandList.brandImage.