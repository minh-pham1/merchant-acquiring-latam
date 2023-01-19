---
tags: [Getting Started, Payments, Request Methods, SEPA Transactions]
---

# Payment Methods

To create a customer payment using a Primary Transaction, you must include the paymentMethod object in the JSON call. Dependent on the requestType, you’ll make some changes to the paymentMethod object to reflect different payment instruments and transaction types.

To take a Card Payment, use the PaymentCardPaymentMethod:

```json
{
  "paymentCard": {
    "number": "5424180279791732",
    "expiryDate": {
      "month": "03",
      "year": "21"
    },
    "securityCode": "977"
  }
}
```

To take a Token Payment, use the PaymentTokenPaymentMethod:

```json
{
  "paymentToken": {
    "value": "1235325235236",
    "function": "DEBIT",
    "securityCode": "977"
  }
}
```

Within the paymentMethod object, you’ll provide different values dependent on the payment method type being used for payment. This defines the payment instrument the customer is using to make the transaction. The available options available are defined below. These will be regularly updated as more payment methods become available.

Further information on the payload changes associated with each of these methods is accessed via clicking the paymentMethodType.

|paymentMethodType	|Description|
| ------------------| --------- |
|PAYMENT_CARD	|The Payment Card payment method covers all Credit and Debit Card types and networks. Your merchant acquiring contract will tell you which of the payment networks you can accept payments for.|
|PAYMENT_TOKEN	|The Payment Token payment method enables you to use a token created by us, instead of the actual payment method details. More information is provided on using Tokens here.|
|SEPA	A SEPA |transaction enables the customer to pay you via a bank-to-bank request over the European SEPA network.|
|WALLET	|Enables payment via a Wallet transaction. Wallet transactions require a specific paymentMethod object. More information on this object is provided in the Wallet Payments section.|

## SEPA Transactions

To use the ```SepaSaleTransaction``` request type, the paymentMethod object is replaced by the sepa object. The sepa object includes all data points required to execute a sepa transaction. Sepa stands for the “Single European Payments Area”, and enables payments to be executed immediately between bank accounts at low cost. This capability is only enabled for domestic payments in Germany at present. Additional countries will be added in the near future.

You must be signed up for SEPA payments and have a mandate reference ID to supply in order to populate the mandate object.

The SEPA PaymentMethod Object is shown below:

```json
{
  "sepa": {
    "iban": "DE34500100600032121604",
    "name": "Alan Turner",
    "country": "DEU",
    "email": "AlanTurner@Bonn.com",
    "mandate": {
      "reference": "3JLCSTIG",
      "url": "https://www.fiserv.com",
      "signatureDate": "2020-10-15",
      "type": "SINGLE"
    }
  }
}
```

---

## See Also

- [Request Types](?path=docs/3-1-request-types.md)
- [Payment Types](?path=docs/3-3-payment-types.md)
- [Post-authorisation & Returns](?path=docs/3-4-post-auth.md)
- [3-D Secure](?path=docs/3-5-3d-secure.md)
- [Card Verification](?path=docs/3-6-card-verification.md)
- [Currency Conversion](?path=docs/3-7-currency-conversion.md)
- [Managed Redirect](?path=docs/3-8-managed-redirect.md)
- [Orders](?path=docs/3-9-orders.md)
- [Payment URL](?path=docs/3-10-payment-url.md)
- [Recurring Payments](?path=docs/3-11-recurring-payments.md)
- [Tokenisation](?path=docs/3-12-tokenisation.md)
- [Error codes](?path=docs/3-13-error-codes.md)

---
