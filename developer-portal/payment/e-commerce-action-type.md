# Action types and how to proceed to the next steps

<table>
    <tr>
        <th>Type of the action</th>
        <th>Description</th>
        <th>Next steps</th>
    </tr>
    <tr>
        <td>threeDSRedirect</td>
        <td>This is applied to 3DS 1 or 2 when processing authentication in which paymentMethod.type is card and authentication.type is threeDSPage or threeDSIntegrator.</td>
        <td>Redirect the user to another website to complete the 3DS 1 or 2 authentication with action.threeDSData in the same response. See chapter Authentication for more details.</td>
    </tr>
    <tr>
        <td>threeDSIdentify</td>
        <td>This is applied to 3DS 2 when processing authentication in which paymentMethod.type is card with authentication.type is threeDSIntegrator.</td>
        <td>Redirect the user to another website to complete the 3DS 2 frictionless authentication with action.threeDSData in the same response. See chapter Authentication for more details.</td>
    </tr>
    <tr>
        <td>threeDSChallenge</td>
        <td>This is applied to 3DS 2 when processing authentication in which paymentMethod.type is card with authentication.type is threeDSIntegrator.</td>
        <td>Redirect the user to another website to complete the 3DS 2 challenge authentication with action.threeDSData in the same response. See chapter Authentication for more details.</td>
    </tr>
    <tr>
        <td>inputMobilePhone</td>
        <td>This is applied to SecurePlus authentication for paymentMethod.type is card and authentication.type is SecurePlus.</td>
        <td>Present an input box on your payment page to ask the user to input the mobile phone number and submit it to EVO Cloud. See chapter Authentication for more details.</td>
    </tr>
    <tr>
        <td>inputSMSCode</td>
        <td>This is applied to SecurePlus authentication for paymentMethod.type is card and authentication.type is SecurePlus.</td>
        <td>Present an input box on your payment page to ask the user to input the SMS code received on the mobile phone and submit it to EVO Cloud. See chapter Authentication for more details.</td>
    </tr>
    <tr>
        <td>redirectUser</td>
        <td>
          This is applied to payment and authentication when redirection is required. The following payment methods and Payment Brands are applied: 
          <br><li>Alipay, Alipay+ and JkoPay on PC WEB.
          <br><li>Alipay, Alipay+, Alipay HK, WeChat Pay and Jkopay on Mobile WAP.
          <br><li>WeChat Pay in WeChat official account.
          <br><li>UnionPay SecurePay on PC WEB and Mobile WAP.
        </td>
        <td>
          Redirect the user to the action.redirectData.url with HTTP method specified in action.redirectData.method. If action.redirectData.method is POST and action.redirectData.parameter exists, specify the sub field(s) in the parameter object as the form data of the POST request. The user is then redirected to a page where they need to take additional action, depending on the PSP.
          <br>After the user completes the payment, the user is redirected back to your returnURL with an HTTP GET or POST. You can ignore this redirection, or optionally display a loading page to the user before you get the payment result.
          <br>1.You can optionally make an HTTP GET request from your server to EVO Cloud endpoint /g2/v1/payment/mer/{sid}/payment to get the payment result. See Step 6 result for more details.
          <br>2.Or you can wait for the notification webhook to know the payment result. See chapter After Payment for more details.
        </td>
    </tr>
    <tr>
        <td>redirectInIframe</td>
        <td>
          This is applied to E-Wallet payment on PC WEB with AlipayHK merchant account, including user paying with AlipayHK wallet APP and Alipay wallet APP.</td>
          <td>
            Render an iframe on your payment page and perform a redirection in the iframe to the action.redirectData.url with HTTP method specified in action.redirectData.method. If action.redirectData.method is POST and action.redirectData.parameter exists, specify the sub field(s) within the parameter object as the form data of the POST request. The user is then prompted with a QR code in the iframe which the user needs to scan and pay with the E-Wallet APP.
            <br>1.You can optionally make an HTTP GET request from your server to EVO Cloud endpoint /g2/v1/payment/mer/{sid}/payment to get the payment result. See Step 6 for more details.
            <br>2.Or you can wait for the notification webhook to know the payment result. See chapter After Payment for more details.
        </td>
    </tr>
    <tr>
        <td>presentQRCode</td>
        <td>This is applied to E-Wallet payment on PC WEB using WeChat Pay. </td>
        <td>
          Render the QR code with the data specified by action.qrData object. The user is then prompted with a QR code which the user needs to scan and pay with the E-Wallet APP.
          <br>1.You can optionally make an HTTP GET request from your server to EVO Cloud endpoint /g2/v1/payment/mer/{sid}/payment to get the payment result. See Step 6 for more details.
          <br>2.Or you can wait for the notification webhook to know the payment result. See chapter After Payment for more details.</td>
    </tr>
    <tr>
        <td>invokeWallet</td>
        <td>
          This is applied to:
          <br><li>E-Wallet payment in wallet APP and mini-program using WeChat Pay or Alipay.
          <br><li>SecurePay of UnionPay in wallet APP.
        </td>
        <td>
          <br>1.Invoke the wallet SDK with action.walletData.paymentString to trigger the switch to the wallet APP. See WeChat Pay In-APP pay integration guide or Alipay APP payment integration guide or UnionPay In-APP integration guide. Or you can choose EVO Cloud SDK to realize the same function. See EVO Cloud SDK Integration Guide for more details. The user is then switched to wallet APP where the user needs to take additional action to complete the payment.
          <br>2.After the user completes the payment, the user will be redirected back by the callback function you specify when invoking the SDK. You can optionally display a loading page to the user before you get the payment result.
          <br>3.You can optionally make an HTTP GET request from your server to EVO Cloud endpoint /g2/v1/payment/mer/{sid}/payment to get the payment result. See Step 6 for more details.
          <br>4.Or you can wait for the notification webhook to know the payment result. See chapter After Payment for more details.
        </td>
    </tr>
    <tr>
        <td>presentEcontext</td>
        <td>This is applied to cash payment and bank transfer</td>
        <td>Present the texts in the object action.econtext on your payment page to ask the user to complete the follow-up payment process. If the field action.econtext.instructionURL is returned you can also present it on your payment page to guide user.</td>
    </tr>
</table>

# Frontend action need to perform for some actions

## redirectUser

If action.redirectData.method is GET, redirect the user to the action.redirectData.url.

```bash
curl {action.redirectData.url}
```

If action.redirectData.method is POST and action.redirectData.parameter exists, for example, if the user is performing the payment with SecurePay for UnionPay on PC WEB or Mobile WAP, here is an example of action.redirectData.parameter:

```json
{  
   "accNo": "ZgTg0d4%2BWJeM7GBq9EbxjSu0wvQQYpS8%2F%2B9NJ848bx1tLlqWc%2FbtUSzFiMaKaFHNEJMT3PPvv8RY8C9lcOeoWJpFBUA4en%2FSw2OLqY%2Fmj1zcikLMg5O4XgO8aqELQUDZNin1mtlkDGx1%2B%2FR9NNX4a%2F2E8x2%2Fme4haxUdO%2BVY76v7edaj30pR2n%2BbLfB70pDnc9XIsZrL5cNPE9Wj83Mirh2rLwEtreS%2FKtCYqzpopLEcu115sd%2BJOdj1E0z3Bp2x5EBLrdiz714kOV0FmY7HQz0UFwV3MnY7PofuQ7MwnilYYnTsFBcTKxNpSwAZlZAB0eSijnFpIG3tYjnIephVFg%3D%3D",  
   "accessType": "1",  
   "acqInsCode": "YOUR_ACQUIRER_IIN",  
   "backUrl": "https%3A%2F%2F%7BEVO_Cloud_DOMAIN_NAME.com%7D%2FpspNotify%2FUPI%2F%7BYOUR_ACQUIRER_IIN%7D",  
   "bizType": "000201",  
   "certId": "69798631478",  
   "channelType": "07",  
   "currencyCode": "840",  
    "encoding": "UTF-8",  
    "encryptCertId": "69026275926",  
    "frontUrl": "https%3A%2F%2FYOUR_COMPANY.com%2FRETURNURL",  
    "merAbbr": "AbbrName",  
    "merCatCode": "5411",  
    "merId": "999290058120001",  
    "merName": "This is your merchant name",  
    "orderId": "YOUR_TRANS_ID",  
    "payTimeout": "20211231183059",  
    "signMethod": "01",  
    "signature": "EashQx%2F4eLvGenbKThFNeqssyg6tSXlj5mT3GbF2ll9TV8ObzqYpMd%2Fl2Ymv0UKkWOi05QeRM9c2UajjzAM92CUGp8KhVkfcK4BUH1BXNgAWAEZwB8ZLLuMPvnTQZJ%2F03HQ6hOeyIXOCE1lOm4HlL9n5B5bg2ACRFpxK%2FmGVPZ%2FuGumAMyEWq5%2B8HwRlcTBepzDYNuSqCLegofT7bTAQZIELf%2BjxKSgV3D3cCI9PwOg44uFlRwSdtg%2Fr0FnIh%2BhQTVDgY0ttJm99GWYwJIKzdsEdN9tihpgxJVoxveoJBwll2M6mrv7THXMFvI3p3zFCpLIjU%2FxqvvUQmViYC3lniA%3D%3D",  
    "txnAmt": "1000",  
    "txnSubType": "01",  
    "txnTime": "20211231083059",  
    "txnType": "01",  
    "version": "5.1.0"  
}  
```

In this case, you need to perform the redirectUser action on your frontend with a form POST:

```html
<form id="pay_form" action="{action.redirectData.url} " method="post">  
   <input type="hidden" name="accNo" id="accNo" value="ZgTg0d4%2BWJeM7GBq9EbxjSu0wvQQYpS8%2F%2B9NJ848bx1tLlqWc%2FbtUSzFiMaKaFHNEJMT3PPvv8RY8C9lcOeoWJpFBUA4en%2FSw2OLqY%2Fmj1zcikLMg5O4XgO8aqELQUDZNin1mtlkDGx1%2B%2FR9NNX4a%2F2E8x2%2Fme4haxUdO%2BVY76v7edaj30pR2n%2BbLfB70pDnc9XIsZrL5cNPE9Wj83Mirh2rLwEtreS%2FKtCYqzpopLEcu115sd%2BJOdj1E0z3Bp2x5EBLrdiz714kOV0FmY7HQz0UFwV3MnY7PofuQ7MwnilYYnTsFBcTKxNpSwAZlZAB0eSijnFpIG3tYjnIephVFg%3D%3D" />  
   <input type="hidden" name="accessType" id="accessType" value="1" />  
   <input type="hidden" name="acqInsCode" id="acqInsCode" value="YOUR_ACQUIRER_IIN" />  
   <input type="hidden" name="backUrl" id="backUrl" value="https%3A%2F%2F%7BEVO_Cloud_DOMAIN_NAME.com%7D%2FpspNotify%2FUPI%2F%7BYOUR_ACQUIRER_IIN%7D" />  
   <input type="hidden" name="bizType" id="bizType" value="000201" />  
   <input type="hidden" name="certId" id="certId" value="69798631478" />  
   <input type="hidden" name="channelType" id="channelType" value="07" />  
   <input type="hidden" name="currencyCode" id="currencyCode" value="840" />  
   <input type="hidden" name="encoding" id="encoding" value="UTF-8" />  
   <input type="hidden" name="encryptCertId" id="encryptCertId" value="69026275926" />  
   <input type="hidden" name="frontUrl" id="frontUrl" value="https%3A%2F%2FYOUR_COMPANY.com%2FRETURNURL" />  
   <input type="hidden" name="merAbbr" id="merAbbr" value="AbbrName" />  
   <input type="hidden" name="merCatCode" id="merCatCode" value="5411" />  
   <input type="hidden" name="merId" id="merId" value="999290058120001" />  
   <input type="hidden" name="merName" id="merName" value="This is your merchant name" />  
   <input type="hidden" name="orderId" id="orderId" value="YOUR_TRANS_ID" />  
   <input type="hidden" name="payTimeout" id="payTimeout" value="20211231183059" />  
   <input type="hidden" name="signMethod" id="signMethod" value="01" />  
   <input type="hidden" name="signature" id="signature" value="EashQx%2F4eLvGenbKThFNeqssyg6tSXlj5mT3GbF2ll9TV8ObzqYpMd%2Fl2Ymv0UKkWOi05QeRM9c2UajjzAM92CUGp8KhVkfcK4BUH1BXNgAWAEZwB8ZLLuMPvnTQZJ%2F03HQ6hOeyIXOCE1lOm4HlL9n5B5bg2ACRFpxK%2FmGVPZ%2FuGumAMyEWq5%2B8HwRlcTBepzDYNuSqCLegofT7bTAQZIELf%2BjxKSgV3D3cCI9PwOg44uFlRwSdtg%2Fr0FnIh%2BhQTVDgY0ttJm99GWYwJIKzdsEdN9tihpgxJVoxveoJBwll2M6mrv7THXMFvI3p3zFCpLIjU%2FxqvvUQmViYC3lniA%3D%3D" />  
   <input type="hidden" name="txnAmt" id="txnAmt" value="1000" />  
   <input type="hidden" name="txnSubType" id="txnSubType" value="01" />  
   <input type="hidden" name="txnTime" id="txnTime" value="20211231083059" />  
   <input type="hidden" name="txnType" id="txnType" value="01" />  
   <input type="hidden" name="version" id="version" value="5.1.0" />  
  </form>  
```

Please keep the letters case of the key(s) in your POST request consistent with the parameter(s) name in action.redirectData.parameter returned from EVO Cloud response.

## redirectInIframe

It is similar with processing redirectUser to process redirectInIframe action on your frontend, the only difference is that you need to initiate the HTTP GET or POST request within the iframe you render on your payment page. It is suggested to set the iframe as 200*200 px.

## presentQRCode

Render the QR code with the data specified by action.qrData object, and prompt it to your user. The details of action.qrData includes:

* action.qrData.qrCode: The contents of the QR code, a UTF-8 string. Use this to render the QR code.

* action.qrData.qrDownloadURL: The download URL of the QR code. This field will be returned when action.qrData.qrCode is not returned.

* action.qrData.expiredAt: The date and time when the payment expires. For example: 2017-07-17T13:42:40+01:00.

## invokeWallet

If you choose to use the wallet SDK, see wallet SDK guides for more details on how to invoke the SDKs.

If you use EVO Cloud SDK, see EVO Cloud SDK Integration Guide. You can contact EVO Cloud account manager to get the guides.

