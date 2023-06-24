# E-Commerce payment

![path](../images/e-commerce-payment.svg)

The diagram above describes both server-side and the client-side implementation that you have to build.

## Step 1: Get available payment method(s)

When a user is ready to pay or enters your payment page, get a list of the available payment method(s) with the corresponding information that needs to be filled in by the user.

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
        <td>cardBIN</td>
        <td>O</td>
        <td>The specific card BIN which the user intends to use to pay. With this parameter, you will get the available payment method(s) and payment information for this specific card BIN in response.</td>
    </tr>
    <tr>
        <td>currency</td>
        <td>O</td>
        <td>The specific currency which the user intends to use to pay. With this parameter, you will get the available payment method(s) and payment information for this specific currency in response.</td>
    </tr>
    <tr>
        <td>tokenize</td>
        <td>O</td>
        <td>The Boolean filed. Used to indicate Inquiry the payment method which support tokenization. With the value true, you will get the payment method(s) and payment information which support tokenization.</td>
    </tr>
</table>

Only one parameter above is allowed to be used for each request. If multiple parameters are sent, the system will reply you with a priority of cardBIN, paymentBrand, and then currency.

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

* paymentInfo: The payment information that required by the Card Scheme or E-Wallet to initiate the payment.

* authenticationInfo: The authentication information that required by the Card Scheme to initiate the user authentication.

* promotionInfo: The list of the promotion names supported by the payment method.

See the example of the response [here](./ec-paymentmethod-response-example.md).

If you want to guide your user to pay on your frontend page or client APP, you can use paymentMethod.paymentBrandList.paymentInfo to provide the list of payment methods and the required payment information for each payment method.

If you want to guide your user to conduct a payment authentication, you can use paymentMethod.paymentBrandList.authenticationInfo. See chapter Authentication for more details.

You can add additional tips or format check by your own if needed.

Please note that for the following fields, after you collect it from the user, before you send them to EVO Cloud, you need to ensure your request follows the ISO specification. See EVO Cloud API Specification for more details.

<table>
    <tr>
        <th>Key name in paymentBrandList</th>
        <th>Field name in EVO Cloud API</th>
        <th>ISO specification</th>
    </tr>
    <tr>
        <td>Country (in Delivery Address)</td>
        <td>userInfo.deliveryAddress.country</td>
        <td>ISO-3166-1 alpha-3. For example, SGP.</td>
    </tr>
    <tr>
        <td>State or Province (in Delivery Address)</td>
        <td>userInfo.deliveryAddress.stateOrProvince</td>
        <td>ISO-3166-2. For example, North East.</td>
    </tr>
    <tr>
        <td>Country (in Billing Address)</td>
        <td>userInfo.billingAddress.country</td>
        <td>ISO-3166-1 alpha-3. For example, SGP.</td>
    </tr>
    <tr>
        <td>State or Province (in Billing Address)</td>
        <td>OuserInfo.billingAddress.stateOrProvince</td>
        <td>ISO-3166-2. For example, North East.</td>
    </tr>
</table>

## Step 2: Collect user's payment information

Collect your user's payment information based in the fields required by each payment method, then send them to your host.

Please note that if the user selects to pay with a card, the merchant must obtain the PCI DSS compliance. You have 2 ways to collect card details:

1. Use EVO Cloud client-side solution to securely encrypt your user's card details. This option ensures you meet the PCI DSS compliance, as you're only required to submit PCI DSS Self-Assessment Questionnaire A (SAQ A). See EVO Cloud SDK Integration Guide for more details.

2. Collect and send raw card data. This option requires you to assess your PCI DSS compliance according to PCI DSS Self-Assessment Questionnaire D (SAQ D), the most extensive form of self-certification.

## Step 3: Make a payment

After the user chooses the payment method and submits the required payment information, you need to make a payment request to EVO Cloud.

From your server, you can make an HTTP POST request to EVO Cloud endpoint */g2/v1/payment/mer/{sid}/payment* and specify the parameters below.

<table>
    <tr>
        <th>Body parameter</th>
        <th>Required</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>merchantTransInfo</td>
        <td>M</td>
        <td>The reference for the payment, including a unique merchantTransID and merchantTransTime to specify the time you initiate the request.</td>
    </tr>
    <tr>
        <td>transAmount</td>
        <td>M</td>
        <td>The currency and value of the payment. The value must follow the currency's minor unit.</td>
    </tr>
    <tr>
        <td>paymentMethod</td>
        <td>M</td>
        <td>Use paymentMethod.type to specify different payment method type, such as e-wallet or card payment, with different payment information that you collect in the previous step.</td>
    </tr>
    <tr>
        <td>transInitiator</td>
        <td>M</td>
        <td>The information of device to initiate the transaction.</td>
    </tr>
    <tr>
        <td>storeInfo</td>
        <td>O</td>
        <td>The supplemental store information. When your store is assigned with multiple MCCs in EVO Cloud, this field is required to indicate which MCC you are using for this payment.</td>
    </tr>
    <tr>
        <td>tradeInfo</td>
        <td>O</td>
        <td>This field is required when paymentMethod.e-wallet.paymentBrand is Alipay to indicate the detailed information about this payment.</td>
    </tr>
    <tr>
        <td>userInfo</td>
        <td>O</td>
        <td>Detailed information about the buyer. Specify this parameter if you can obtain the information from the user. This helps EVO Cloud laundering and fraud detection, and increases payment success rates.</td>
    </tr>
    <tr>
        <td>validTime</td>
        <td>O</td>
        <td>This is applied when paymentMethod.type is e-wallet to indicate the effective time (minutes) of the order. The payment can only be executed before the time. If you have any special requirements for this value definition, please contact EVO Cloud account manager for details and suggestion.</td>
    </tr>
    <tr>
        <td>returnURL</td>
        <td>O</td>
        <td>The URL to redirect your customer back to after your customer completes or cancels the payment.</td>
    </tr>
    <tr>
        <td>webhook</td>
        <td>O</td>
        <td>The URL to receive notification. Specify this to get the notification from EVO Cloud after the payment succeeds.</td>
    </tr>
    <tr>
        <td>metadata</td>
        <td>O</td>
        <td>A self-defined reference information that you can specify in the request, and will be echoed back in the response. The metadate message in the response is the same as the metadata in your request message, except of GET request. The metadata in GET response message is the same as the metadata in the POST request message. And the metadata in the notification is the same as the metadata in the POST request message.</td>
    </tr>
</table>

See [key fields in paymentMethod object](./key-fields-in-paymentmethod-object.md) for more details about the paymentMethod object.

You need to include additional parameters in your payment if you request to:

* Tokenize the user's payment information. See chapter Tokenization for more details.

* Authenticate the user with 3D Secure or SecurePlus. See chapter Authentication for more details.

* Place a hold to reserve funds and capture it later. See chapter After Payment for more details.

Here is an example of using Alipay to make a 10 USD payment:

```bash
curl -X POST https://{EVO_Cloud_DOMAIN_NAME.com}/g2/v1/payment/mer/{sid}/payment \  
-H "Content-Type: application/json" \  
-H "DateTime: 2021-12-31T08:30:59+0800" \  
-H "MsgID: 2d21a5715c034efb7e0aa383b885fc7a" \  
-H "SignType: SHA256" \  
-H "Authorization: YOUR_MESSAGE_SIGNATURE" \  
-d '{  
    "merchantTransInfo": {  
        "merchantTransID": "e05b93cc849046a6b570ba144c328c7f",  
        "merchantTransTime": "2021-12-31T08:30:59+08:00"  
    },  
    "storeInfo": {  
        "mcc": "5411"  
    },  
    "transAmount": {  
        "currency": "USD",  
        "value": "10.00"  
    },  
    "validTime": "120",  
    "returnURL": "https://YOUR_COMPANY.com/RETURNURL",  
    "paymentMethod": {  
        "type": "e-wallet",  
        "e-wallet": {  
            "paymentBrand": "Alipay"  
        }  
    },  
    "transInitiator": {  
        "platform": "WEB"  
    },  
    "tradeInfo": {  
        "tradeType": "Sale of goods",  
        "goodsName": "iPhone 13",  
        "goodsDescription": "This is an iPhone 13",  
        "totalQuantity": "2"  
    },  
    "webhook": "https://YOUR_COMPANY.com/WEBHOOK",  
    "metadata": "This is a metadata"  
}' 
```

If you don't receive an action object in the response, you can go to Step 7 to use the result.code and payment.status to present the payment result to your user.

Otherwise, additional steps are required to complete the payment. You need to send the action object to your frontend page or client APP, then proceed to the next step.

Here is an example of receiving a response with an action object requiring a redirection:

```json
{  
    "result": {  
        "code": "S0000",  
        "message": "Success",  
        "pspResponseCode": "PAYMENT_IN_PROCESS",  
        "pspMessage": "Payment is processing."  
    },  
    "action": {  
        "type": "redirectUser",  
        "redirectData": {  
            "url": "https://ALIPAY_REDIRECT_URL.com",  
            "method": "GET"  
        }  
    },  
    "paymentMethod": {  
        "e-wallet": {  
            "paymentBrand": "Alipay"  
        }  
    },  
    "payment": {  
        "status": "Pending",  
        "merchantTransInfo": {  
            "merchantTransID": "e05b93cc849046a6b570ba144c328c7f",  
            "merchantTransTime": "2021-12-31T08:30:59+08:00"  
        },  
        "evoTransInfo": {  
            "evoTransID": "6a3b2e6b5ab74d6da7202cdf8e97fa6e",  
            "evoTransTime": "2021-12-31T00:30:59Z"  
        },  
        "pspTransInfo": {  
        "pspTransID": "012650163996361073624683217162626594RAUmxGgaUF2012190006141885",  
            "pspTransTime": "2021-12-31T08:30:59+08:00"  
        },  
        "transAmount": {  
            "currency": "USD",  
            "value": "10.00"  
        }  
    },  
    "pspData": {  
        "name": "Alipay"  
    },  
    "metadata": "This is a metadata"  
} 
```

## Step 4: Perform additional actions

If your server receives an action object in the payment response (*/g2/v1/payment/mer/{sid}/payment*), you need to follow the [action.type](./e-commerce-action-type.md) to conduct the next step.


## Step 5: Submit the additional payment information

When the payment method is the card, if the user performs the authentication flow, you need to submit the additional payment details to EVO Cloud to complete the payment. See chapter Authentication for more details.

## Step 6: Retrieve the payment result

When the payment method is the E-Wallet, if you redirect the user to another website or APP, or if you present the QR code for the user to scan and pay, you can make a request to EVO Cloud to retrieve the payment result if you don't use the Accept Notification webhook.

From your server, you can make an HTTP GET request to EVO Cloud endpoint
*/g2/v1/payment/mer/{sid}/payment* and specify the parameter below.

<table>
    <tr>
        <th>Query parameter</th>
        <th>Required</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>merchantTransID</td>
        <td>M</td>
        <td>The merchantTransInfo.merchantTransID of the requesting payment.</td>
    </tr>
</table>

Here is an example of retrieving the payment result:

```bash
curl https://{EVO_Cloud_DOMAIN_NAME.com}/g2/v1/payment/mer/{sid}/payment?merchantTransID={YOUR_TRANS_ID_OF_INITIAL_PAYMENT} \  
-H "Content-Type: application/json" \  
-H "DateTime: 2021-12-31T08:30:59+0800" \  
-H "MsgID: 2d21a5715c034efb7e0aa383b885fc7a" \  
-H "SignType: SHA256" \  
-H "Authorization: YOUR_MESSAGE_SIGNATURE"  
```

If you receive an action object in the response, similar with Step 3, additional steps are required to complete the payment. You need to send the action object to your frontend page or client APP, then proceed to the next step. Back to Step 4 to perform the required action.

If not, you can go to Step 7 to use the result.code and payment.status to present the payment result to your user.

If the payment is completed, you will get a payment object in the response to show the details of this payment. See [key fields in payment object](./key-fields-in-payment-object.md) for more details.

Here is an example of a successful payment response:

```json
{  
   "result": {  
       "code": "S0000",  
       "message": "Success",  
       "pspResponseCode": "SUCCESS",  
       "pspMessage": "success"  
   },  
   "paymentMethod": {  
       "e-wallet": {  
            "paymentBrand": "Alipay"  
        }  
    },  
    "payment": {  
        "status": "Captured",  
        "merchantTransInfo": {  
            "merchantTransID": "e05b93cc849046a6b570ba144c328c7f",  
            "merchantTransTime": "2021-12-31T08:30:59+08:00"  
        },  
        "evoTransInfo": {  
            "evoTransID": "6a3b2e6b5ab74d6da7202cdf8e97fa6e",  
            "evoTransTime": "2021-12-31T00:30:59Z"  
        },  
        "pspTransInfo": {  
            "pspTransID": "012650163996361073624683217162626594RAUmxGgaUF202112190006141885",  
            "pspTransTime": "2021-12-31T08:30:59+08:00"  
        },  
        "transAmount": {  
            "currency": "USD",  
            "value": "10.00"  
        }  
    },  
    "pspData": {  
        "name": "Alipay"  
    },  
    "metadata": "This is a metadata"  
}  
```

## Step 7: Present the payment result

After the user completes the payment and no further actions are required on the frontend or client APP, you can use the result.code and payment.status to inform the user of the payment status.

<table>
    <tr>
        <th>Result code</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>S0000</td>
        <td>Success</td>
    </tr>
    <tr>
        <td>V0008</td>
        <td>Field {} is not applied with {...}</td>
    </tr>
    <tr>
        <td>C0004</td>
        <td>Request resource merchantTransID {XXX} not exist</td>
    </tr>
    <tr>
        <td>C0006</td>
        <td>Request resource vault ID {XXX} not exist</td>
    </tr>
    <tr>
        <td>C0008</td>
        <td>Request resource paymentMethod token {XXX} not exist</td>
    </tr>
    <tr>
        <td>B0005</td>
        <td>Unsupported function {XXX}, please check your profile setup in EVO Cloud</td>
    </tr>
    <tr>
        <td>B0012</td>
        <td>Invalid transaction status {XXX} to complete the request</td>
    </tr>
    <tr>
        <td>B0019</td>
        <td>Authentication required, please ask user to input authentication info</td>
    </tr>
    <tr>
        <td>B0021</td>
        <td>{Payment / Tokenization} failed due to user authentication results with high risk, please ask user to try again</td>
    </tr>
</table>

For other possible result.code values and recommended messages that you can present to your user, see Appendix in EVO Cloud API Specification.

If the result.code is S0000, you need to check the payment.status to judge and inform your user the payment status.

<table>
    <tr>
        <th>Status</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>Authorised</td>
        <td>Used for card transaction, means the transaction authorization is successfully completed and pending for Capture (merchant can submit cancel).</td>
    </tr>
    <tr>
        <td>Captured</td>
        <td>If card transaction, means transaction authorization and capture are successfully completed; If e-wallet transaction, means transaction is successfully completed.</td>
    </tr>
    <tr>
        <td>Failed</td>
        <td>Payment failed</td>
    </tr>
</table>

For other possible payment.status values and description, see payment.status in EVO Cloud API Specification.

<embed src="./_error-handling.md"/>