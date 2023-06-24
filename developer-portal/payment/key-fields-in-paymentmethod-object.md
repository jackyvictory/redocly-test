# Key fields in paymentMethod object

## When the user chooses to pay with E-Wallet

* paymentMethod.type: Set as e-wallet in your request.

* paymentMethod.e-wallet.paymentBrand: The Payment Brand of the e-wallet that the user chooses to pay with, required. See the full list of all the payment brands in EVO Cloud API Specification.

* paymentMethod.e-wallet.subOpenID: The user ID for the user in your WeChat mini-program, required when the user pays with WeChat Pay (paymentMethod.e-wallet.paymentBrand is WeChat_Pay) in your WeChat mini-program (transInitiator.platform is MINI). Get this ID when the user logins your WeChat mini-program.

* paymentMethod.e-wallet.subAppID: The APP ID assigned by WeChat, required. when the user pays with WeChat Pay (paymentMethod.e-wallet.paymentBrand is WeChat_Pay) in your WeChat mini-program (transInitiator.platform is MINI) or your client APP (transInitiator.platform is APP). Get this ID when you register your mini-program or APP to WeChat.

* paymentMethod.e-wallet.mobilePhone: The user's mobile number registered in E-wallet, required when the user pays with TrueMoney wallet.

## When the user chooses to pay with card

* paymentMethod.type: Set as card in your request.

* paymentMethod.card.encryptedCardInfo: The encrypted card information. If you use EVO Cloud client-side solution to securely encrypt your user's card details, your frontend will get the value from EVO Cloud SDK, and you need to send it to your host, then your host needs to forward the original data to EVO Cloud.

* paymentMethod.card.cardInfo: The raw data of the card information. If you collect and send raw card data to EVO Cloud, you need to specify the cardNumber, expiryDate and cvc (optional) and holderName in this object. For some of the UnionPay debit cards, the expiryDate is also optional.

(If both paymentMethod.card.encryptedCardInfo and paymentMethod.card.cardInfo present, paymentMethod.card.cardInfo will be applied.)

## When the user chooses to pay with UnionPay SecurePay

* paymentMethod.type: Set as onlineBanking in your request.

* paymentMethod.onlineBanking.paymentBrand: The Payment Brand of the payment method, required. Set as UnionPay in your request.

* paymentMethod.onlineBanking.encryptedCardInfo: The encrypted card information. If you use EVO Cloud client-side solution to securely encrypt your user's card details, your frontend will get the value from EVO Cloud SDK, and you need to send it to your host, then your host needs to forward the original data to EVO Cloud.

* paymentMethod.onlineBanking.cardInfo: The raw data of the card information. If you collect and send raw card data to EVO Cloud, it is optional to specify the cardNumber in this object.

(If both paymentMethod.onlineBanking.encryptedCardInfo and paymentMethod.onlineBanking.cardInfo present, paymentMethod.onlineBanking.cardInfo will be applied.)

## When the user chooses to pay with Online Banking

* paymentMethod.type: Set as onlineBanking in your request.

* paymentMethod.onlineBanking.paymentBrand: The Payment Brand of the online banking that the user chooses to pay with, required. See the full list of all the payment brands in EVO Cloud API Specification.

## When the user chooses to pay with Points

* paymentMethod.type: Set as points in your request.

* paymentMethod.points.paymentBrand: The Payment Brand of the points payment that the user chooses to pay with, required. See the full list of all the payment brands in EVO Cloud API Specification.

## When the user chooses to pay with Bank Transfer

* paymentMethod.type: Set as bankTransfer in your request.

* paymentMethod.bankTransfer.paymentBrand: The Payment Brand of the bank transfer payment that the user chooses to pay with, required. See the full list of all the payment brands in EVO Cloud API Specification.

* paymentMethod.bankTransfer.name: This field is to indicate the given name and family name of user. You need to specify the givenName and familyName in this object when the paymentBrand is Bank_Transfer_JPN or PayEasy. See the details of this object in EVO Cloud API Specification.

* paymentMethod.bankTransfer.mobilePhone: The user's mobile number. This field must be sent when the paymentBrand is Bank_Transfer_JPN or PayEasy.

## When the user chooses to pay with Buy Now Pay Later

* paymentMethod.type: Set as buyNowPayLater in your request.

* paymentMethod.buyNowPayLater.paymentBrand: The Payment Brand of the buy now pay later payment that the user chooses to pay with, required. See the full list of all the payment brands in EVO Cloud API Specification.

* userInfo.name: The user’s name. This field must be sent when the paymentBrand is Paidy.

* userInfo.shippingAddressCity: The name of city that users live in. This field must be sent when the paymentBrand is Paidy.

* userInfo.shippingAddressLine1: The building name and apartment number that uses live in. This field must be sent when the paymentBrand is Paidy.

* userInfo.shippingAddressLine2: The district, land number and land number extension that users live in. This field must be sent when the paymentBrand is Paidy.

* userInfo.shippingAddressState: The name of state or prefecture that users live in. This field must be sent when the paymentBrand is Paidy.

* userInfo.shippingAddressPostalCode: The postal code that user's address belongs to. This field must be sent when the paymentBrand is Paidy.

* userInfo.shippingAddressCountry: The name of country that users live in. This field must be sent when the paymentBrand is Paidy.

## When the user chooses to pay with Carrier Billing:

* paymentMethod.type: Set as carrierBilling in your request.

* paymentMethod.carrierBilling.paymentBrand: The Payment Brand of the carrier billing payment that the user chooses to pay with, required. See the full list of all the payment brands in EVO Cloud API Specification.

## When the user chooses to pay with Prepaid Card:

* paymentMethod.type: Set as prepaidCard in your request.

* paymentMethod.prepaidCard.paymentBrand: The Payment Brand of the prepaid card payment that the user chooses to pay with, required. See the full list of all the payment brands in EVO Cloud API Specification.

* paymentMethod.prepaidCard.prepaidNumber: Prepaid card number. This field must be sent when prepaid card payment.

## When the user chooses to pay with Cash:

* paymentMethod.type: Set as cash in your request.

* paymentMethod.cash.paymentBrand: The Payment Brand of the cash payment that the user chooses to pay with, required. See the full list of all the payment brands in EVO Cloud API Specification.
