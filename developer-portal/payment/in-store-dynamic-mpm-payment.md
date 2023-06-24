# In-store dynamic MPM payment

![path](../images/in-store-mpm-payment.svg)

The diagram above describes both server-side and the client-side implementation that you have to build.

<embed src="./_in-store-cpm-mpm-step1.md"/>

## Step 2: Make a payment

After the user chooses the payment method, you need to make a payment request to EVO Cloud.

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

:::info
More details about the paymentMethod object:

When you get the payment code of E-Wallet,

* paymentMethod.type: Set as e-wallet in your request.

* paymentMethod.e-wallet.paymentBrand: The Payment Brand of the e-wallet that the user chooses to pay with, required. See the full list of all the payment brands in EVO Cloud API Specification.
:::

Here is an example of using WeChat Pay to make a 10 USD payment:

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
    "paymentMethod": {    
        "type": "e-wallet",    
        "e-wallet": {    
            "paymentBrand": "WeChat_Pay"    
        }    
    },    
    "transInitiator": {    
        "platform": "POS",  
        "paymentScenario": "inStore",  
        "inStorePaymentScenario": "MPM"  
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

If you don't receive an action object in the response, you can go to Step 4 to use the result.code and payment.status to present the payment result to your user.

Otherwise, additional steps are required to complete the payment. You need to send the action object to your terminal, then proceed to the next step.

Here is an example of receiving a response with an action object requiring to present a QR code:

```json
{  
    "result": {  
        "code": "S0000",  
        "message": "Success",  
        "pspMessage": "Success",  
        "pspResponseCode": "SUCCESS"  
    }  
    "action": {  
        "qrData": {  
            "qrCode": "weixin://wxpay/bizpayurl?pr=YZSLeOAzz"  
        },  
        "type": "presentQRCode"  
    },  
    "payment": {  
        "evoTransInfo": {  
            "evoTransID": "f70cf59bfa9a4a87aca67e9bd15e3d3f",  
            "evoTransTime": "2023-04-20T01:51:09Z"  
        },  
        "merchantTransInfo": {  
            "merchantTransID": "e05b93cc849046a6b570ba144c328c7f",  
            "merchantTransTime": "2021-12-31T08:30:59+08:00"  
        },  
        "pspTransInfo": {  
            "pspTransTime": "2021-12-31T00:30:59Z"  
        },  
        "status": "Pending",  
        "transAmount": {  
            "currency": "USD",  
            "value": "10.00"  
        }  
    },  
    "paymentMethod": {  
        "e-wallet": {  
            "paymentBrand": "WeChat_Pay"  
        }  
    },  
    "pspData": {  
        "name": "WeChat_Pay"  
    },  
}  
```

## Step 3: Perform additional actions

If your server receives an action object in the payment response (*/g2/v1/payment/mer/{sid}/payment*), you need to follow the action.type to conduct the next step.

<table>
    <tr>
        <th>Type of the action</th>
        <th>Description</th>
        <th>Next steps</th>
    </tr>
    <tr>
        <td>presentQRCode</td>
        <td>This is applied to MPM scenario of In-Store payment.</td>
        <td>
          Render the QR code with the data specified by action.qrData object. The user is then prompted with a QR code which the user needs to scan and pay with the E-Wallet APP.
          1.You can optionally make an HTTP GET request from your server to EVO Cloud endpoint /g2/v1/payment/mer/{sid}/payment to get the payment result. See Step 4 for more details.
          2.Or you can wait for the notification webhook to know the payment result. See chapter After Payment for more details.
        </td>
    </tr>
</table>

## Step 4: Retrieve the payment result

When the paymentScenario is instore and inStorePaymentScenario is MPM, you need to present the QR code for the user to scan and pay, and then you can make a request to EVO Cloud to retrieve the payment result if you don't use the Accept Notification webhook.

From your server, you can make an HTTP GET request to EVO Cloud endpoint */g2/v1/payment/mer/{sid}/payment* and specify the parameter below.

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

If you receive an action object in the response, similar with Step 2, additional steps are required to complete the payment. You need to send the action object to your terminal, then proceed to the next step. Back to Step 3 to perform the required action.

If not, you can go to Step 5 to use the result.code and payment.status to present the payment result to your user.

If the payment is completed, you will get a payment and paymentMethod object in the response to show the details of this payment:

* payment.status: The status of this capture, required, can be Pending, Cancelling, Cancelled, Captured, Refunding, Refunded, Partial Refunded or Failed. See EVO Cloud API Specification for more details.

* payment.failureCode: The error code for the payment, which will be returned when the payment.status is Failed.

* payment.failureReason: The error message for the payment, which will be returned when the payment.status is Failed.

* payment.transAmount: The currency and value of the payment, required. It is echoed back from your request.

* payment.merchantTransInfo: The object that is a reference for the payment, required and generated by your host. It is echoed back from your request.

* payment.evoTransInfo: The object that is a reference for the payment, required and generated by EVO Cloud. It contains evoTransID, evoTransTime and traceNum (optional) and retrievalReferenceNum for some PSPs such as Visa or Mastercard.

* payment.pspTransInfo: The object that is a reference for the capture, optional and generated by the PSP. It contains pspTransID, pspTransTime and authorizationCode. EVO Cloud forwards this information from PSP to your host if PSP returns.

* payment.billingAmount and payment.billingFXRate: The user's billing currency and value of the payment, and the FX rate between payment.transAmount.currency and payment.billingAmount.currency, if has. EVO Cloud forwards this information from PSP to your host if PSP returns. This is applied to some E-Wallet payment scenarios when PSP conducts the currency conversion, or to some card payment scenarios when you enable the DCC feature. You can contact EVO Cloud account manager for more details.

* payment.convertTransAmount and payment.convertTransFXRate: The currency and value calculated by EVO Cloud based on transAmount and the FX rate from transAmount.currency to a different currency when the transaction is sent out to PSP, and the FX rate between payment.transAmount.currency and payment.convertTransAmount.currency. The FX rate will be provided if you enable currency conversion feature in EVO Cloud and this capture applies currency conversion. You can contact EVO Cloud account manager for more details.

* paymentMethod.e-wallet.walletName: The child wallet name when payment brand is Alipayplus.

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
            "paymentBrand": "WeChat_Pay"  
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
        "name": "WeChat_Pay"  
    },  
    "metadata": "This is a metadata"  
}   
```

## Step 5: Present the payment result

After the user confirms the payment, you can use the result.code and payment.status to inform the user of the payment status.

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
        <td>B0005</td>
        <td>Unsupported function {XXX}, please check your profile setup in EVO Cloud</td>
    </tr>
    <tr>
        <td>B0006</td>
        <td>Unsupported payment brand {XXX}, please check your profile setup in EVO Cloud</td>
    </tr>
    <tr>
        <td>B0033</td>
        <td>The transaction was rejected for security violation</td>
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