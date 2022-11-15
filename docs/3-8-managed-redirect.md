# Managed Redirect

Managed Redirect feature significantly reduces development effort needed for an integration of payment methods requiring consumer redirection. A simple API request to the Gateway generates a URL, which is used in the next step to redirect your customers to and let our Hosted Payment Page (Connect) handle the payment process on your behalf.

## Credit Card

The following JSON document represents an example of a request for generating a redirectURL for a credit card payment:

```json
{
    "requestType": "PaymentCardManagedRedirectRequest",
    "storeId": "1109950006",
    "transactionAmount": {
        "total": "14",
        "currency": "EUR"
    },
    "transactionType": "SALE",
    "paymentMethod": {
        "paymentCard": {
            "number": "403587******4977",
            "securityCode": "***",
            "cardFunction": "CREDIT",
            "expiryDate": {
                "month": "12",
                "year": "24"
           }
        }
      }
    }
```

In the response from the Gateway you receive the 'redirectURL' parameter, which you can pass to your consumers to complete their payment.

The following JSON document represents an example of a response:

```json
{
   "clientRequestId": "a35e3995-e74a-4e25-ad49-849de6bbabca",
   "apiTraceId": "Ymf1-1ohKkLOqSdsIKi3AQAAA6c",
   "requestStatus": "SUCCESS",
   "orderId": "R-0229343b-aeed-411e-9496-c1fd73dafae5",
   "redirectURL": "https://test3.ipg-online.com/connect/gateway/processing?storename=1109950006&oid=R-0229343b-aeed-411e-9496-c1fd73dafae5&managedRedirectUrlId=84af185c-5ec9-45ea-952a-ee4497726178",
   "transactionId": "84400086815"
}
```

> Note. Please note, that after your customer completed their payment, you will receive HTML formatted response instead of a JSON formatted response
<!-- theme: warning -->

> 🚧 Note. Please note, that after
<!-- theme: warning -->

## 3-D Secure

For cases, where you would require 3-D Secure authentication to be included, you must include additional 'redirectAttributes' object in your initial request.

|redirectAttribute Name	|	Mandatory/Optional	|Description|
|----------------------|----------------------|-----------|
|```authenticateTransaction```	|mandatory	|Indicates whether a 3-D Secure authentication is requested for this transaction. Values accepted: • true|
|```challengeIndicator```|	mandatory	|Indicates whether a challenge is requested for this transaction. If not submitted in the request a default value '01' is populated automatically. Values accepted: • 01 = No preference • 02 = No challenge requested • 03 = Challenge requested (3DS Requestor preference) • 04 = Challenge requested (Mandate)|
|```language```	|mandatory	|Value representing the browser language as defined in IETF BCP47, example of frequently used languages: English: en  Spanish: es  French: fr  German: de  Portuguese: pt  Hindi: hi|
|```threeDSEmvCoMessageCategory```|	optional	|Identifies the category of the message for a specific use case. If not submitted in the request a default value '01' is populated automatically.  Values accepted:  • 01 = Payment Authentication (default value)  • 02 = Non-Payment Authentication|
|```mobileMode```	|optional	|Boolean that represents the request for utilizing hosted payment pages mobile mode.  Values accepted:  • true  • false|
|```browserJavaScriptEnabled```	|optional	|Boolean that represents the ability of the cardholder browser to execute JavaScript.  Values accepted:  • true  • false|
|```threeDSTransactionType```	|optional	|Identifies the type of transaction being authenticated. If not submitted in the request a default value '01' is populated automatically.  Values accepted:  • 01 = Goods/ Service Purchase (default value)  • 03 = Check Acceptance  • 10 = Account Funding  • 11 = Quasi-Cash Transaction  • 28 = Prepaid Activation and Load
|```skipTRA```	|optional	|Boolean that represents the ability to skip Transaction Risk Analysis result recommendation on a transaction level. Available only for stores enabled with TRA service.  Values accepted:  • true  • false|
|```override3dsCountryExclusion```	|optional	|Boolean that overrides Dynamic 3-D Secure country exclusion setup.  Values accepted:  • true  • false|

The following JSON document represents an example of a transaction including minimal set of mandatory 'redirectAttributes':

```json
{
    "requestType": "PaymentCardManagedRedirectRequest",
    "storeId": "1109950006",
    "transactionAmount": {
        "total": "14",
        "currency": "EUR"
    },
    "transactionType": "SALE",
    "paymentMethod": {
        "paymentCard": {
            "number": "520474******2745",
            "securityCode": "XXX",
            "cardFunction": "CREDIT",
            "expiryDate": {
                "month": "12",
                "year": "24"
            }
        }
    },
    "redirectAttributes": {
        "language": "en",
        "challengeIndicator": "01",
        "authenticateTransaction": true
    }
}
```

The following JSON document represents an example of a response you receive from the Gateway:

```json
{
   "clientRequestId": "9410557e-e451-4ed8-b37e-0f268a63f060",
   "apiTraceId": "YmuZy8URv@sTmvxBgZjBdQAAAr0",
   "requestStatus": "SUCCESS",
   "orderId": "R-432da132-f610-4714-91a3-3500a02c5863",
   "redirectURL": "https://test3.ipg-online.com/connect/gateway/processing?storename=1109950006&oid=R-432da132-f610-4714-91a3-3500a02c5863&managedRedirectUrlId=9c55f38b-bd57-455a-92ab-90b580306e0f",
   "transactionId": "84400249138"
}
```

### Local Payments

If your account is enrolled with collective Local Payments model, you can submit individual payment method in your ```GenericManagedRedirectRequest``` to our Gateway.

### POST /ipgrestapi/v2/services/managed-redirect

```json
{
  "requestType": "GenericManagedRedirectRequest",
  "storeId": "120995000",
  "transactionAmount": {
    "total": "15",
    "currency": "EUR"
    },
  "transactionType": "SALE",
  "paymentMethod": {
      "paymentMethodType": "IDEAL_APM"
    }
}
```

In the response you will receive a ```redirectURLparameter```, which you can pass to your consumer to complete the payment.

The following JSON document represents an example of the response:

```json
{
   "clientRequestId": "67db6b7e-37d8-4dbd-8b8f-28749a9b0770",
   "apiTraceId": "Yufk72hKKznZUrdK7lqvnwAAA3U",
   "requestStatus": "SUCCESS",
   "orderId": "R-8e6b5d51-842a-4e8d-9231-99450be41f59",
   "redirectURL": "https://test3.ipg-online.com/connect/gateway/processing?storename=120995000&oid=R-8e6b5d51-842a-4e8d-9231-99450be41f59&managedRedirectUrlId=f29084b5-78da-41d8-8ea5-870926f2f516",
   "transactionId": "84405921374"
}
```

The following payment methods has been enabled for Managed Redirect solution:

- ```ALIPAY```
- ```BCMC_APM```
- ```EPS```
- ```GIROPAY_APM```
- ```IDEAL_APM```
- ```MYBANK```
- ```PAYSAFECARD```
- ```PRZELEWY24```
- ```SOFORT_APM```
- ```TRUSTLY```
