---
title: Start Integrating
# description: GitHub-flavored markdown example
toc:
  enable: true
---
# Start Integrating

First, build an integration that can make a payment following the introduction in chapter Payment, the primary function that enables you to accept online payment.

Once you build the primary functions, complete the integration by adding more features:

1. Tokenization: save a user's payment method and make payment later. See chapter Tokenization for more details.

2. Authentication: authenticate a user before making a payment with a card by 3D Secure or SecurePlus. See chapter Authentication for more details.

3. Account Status Inquiry (ASI): instead of authenticating the user, authenticate the user's card before making a payment. See chapter Account Status Inquiry (ASI) for more details.

4. Capture, Cancel, Refund, Customs Clearance, and Accept Notification: the actions you can take after a payment. See chapter After Payment for more details.

Different businesses may need different features, for example:

<table>
    <tr>
        <th rowspan="2">Merchant</th>
        <th rowspan="2">Selling</th>
        <th colspan="5">Features you may need</th>
    </tr>
    <tr>
        <th>Payment</th>
        <th>Tokenization</th>
        <th>ASI</th>
        <th>Capture, cancel, refund</th>
        <th>Customs clearance</th>
    </tr>
    <tr>
        <td>Online store</td>
        <td>Common online goods selling</td>
        <td>Yes</td>
        <td>Suggest to have</td>
        <td>No</td>
        <td>Yes</td>
        <td>Suggest to have if you serve for Chinese users</td>
    </tr>
    <tr>
        <td rowspan="2">Hotel</td>
        <td>Online booking</td>
        <td>Yes</td>
        <td>Suggest to have</td>
        <td>Based on your payment flow</td>
        <td>Yes</td>
        <td>No</td>
    </tr>
    <tr>
        <td>Check-in</td>
        <td>Yes</td>
        <td>No</td>
        <td>No</td>
        <td>Yes</td>
        <td>No</td>
    </tr>
    <tr>
        <td>Rental Company</td>
        <td>Car rental service</td>
        <td>Yes</td>
        <td>Suggest to have</td>
        <td>Based on your payment flow</td>
        <td>Yes</td>
        <td>No</td>
    </tr>
    <tr>
        <td>Others</td>
        <td>Depend on your business</td>
        <td>Yes</td>
        <td>Depend on your business</td>
        <td>Depend on your business</td>
        <td>Depend on your business</td>
        <td>Depend on your business</td>
    </tr>
</table>
