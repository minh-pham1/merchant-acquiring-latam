---
tags: [Getting Started, Payments, Post-authorization & Returns]
---

# Post-authorization & Returns

## /payments/{transaction-id}

Use secondary transactions to Void an original transaction, Return against an original transaction or to complete a Post-Auth transaction. The ```transactionId``` Parameter, populated for the original transaction that requires a secondary action, must be populated for each of these request types.

To cancel the original transaction (same day as the original transaction), use the voidTransaction requestType. To return (reverse on subsequent day) an original transaction, use ```returnTransaction``` as the requestType. To complete a Pre-Authorized transaction using a Post-Authorization transaction, reference the original transaction in the parameter data, then place a POST using the ```PostAuth``` schema.

An updated version of the Decision Matrix diagram provided earlier is shown below, with the secondary transaction requestTypes now included.

![Decision Matrix!](/assets/images/3-4-decision-matrix.png "Decision Matrix")


Secondary transactions are also based on requestTypes. The table below provides links to the requestType schemas and provides the method to use. In all of these transactions, the transaction-id attribute must be populated with the value returned in the 200 response message in the ```ipgTransactionId``` field for the relevant primary transaction.

## API Endpoint

You can find the endpoint here /payments/{transaction-id}

To retrieve the status of a transaction you’ve already submitted, place a GET call to the ```/payments/{transaction-id}``` end point. The gateway will return the details and state of the transaction you submitted.

```json
{
  "method": "post",
  "url": "https://prod.api.firstdata.com/ipp/payments-gateway/v2/payments/1001-1001-1001-1001",
  "query": {},
  "headers": {
    "Content-Type": "application/json",
    "Client-Request-Id": "",
    "Api-Key": "",
    "Timestamp": "",
    "Message-Signature": ""
  }
  "body": {
  "requestType": "ReturnTransaction",
    "transactionAmount": {
      "total": 3,
      "currency": "USD"
    }
  }
}
```

The available requestTypes are listed below, with explanation as to what each of them is used for.

| requestType                  | Method | Description                                                                                                                      |
|------------------------------|--------|----------------------------------------------------------------------------------------------------------------------------------|
| ```VoidTransaction```        | POST   | The VoidTransaction requestType enables you to cancel a transaction you submitted earlier the same day                           |
| ```VoidPreAuthTransaction``` | POST   | The VoidTransaction requestType enables you to cancel a PreAuthorization Transaction                                             |
| ```PostAuthTransaction```    | POST   | The PostAuthTransaction requestType enables you to complete a Pre-Authorization Transaction against the same                     |
| ```ReturnTransaction```      | POST   | The ReturnTransaction requestType enables you to complete a return against a transaction taken prior to the current day          |
| ```Transaction Inquiry```    | GET    | execute a simple GET call against the end point with the ipgtransactionid value from the transaction you want to inquire against |

## Additional Payment Scenarios

There are a number of business scenarios that require combinations of different calls to the same, or different end points. The examples below demonstrate the way in which the different requestTypes and calls can be made to the /payments API to generate different payments outcomes.

### Incrementing or decrementing a Pre-Auth

To increase or decrease the value of a pre-authorization transaction, submit another pre-auth for the same cardholder referencing the ```orderId``` field value from the reponse associated with the original Pre-Auth transaction:

```json
{
  "requestType": "PaymentCardPreAuthTransaction",
  "transactionAmount": {
    "total": "17.00",
    "currency": "EUR"
  },
  "paymentMethod": {
    "paymentCard": {
      "number": "4149011500000147",
      "securityCode": "147",
      "expiryDate": {
        "month": "12",
        "year": "20"
      }
    }
  },
  "order": {
    "orderId": "{{lastOrderId}}"
  }
}
```

The original Pre-Authorization transaction will then be incremented/decremented as set in your request.

## Completing and voiding pre-auth transactions

To complete a Pre-Auth, POST a postAuth transaction to complete the Pre-Authorization, post a secondary transaction to /payments/{transaction-id} stating the ```orderId``` field value from the reponse associated with the original Pre-Auth transaction in {transaction-id}. The splitShipment object enables multiple partial Post-Authorizations in scenarios in which there are multiple shipments against a single original Pre-Authorization.

```json
{
  "requestType": "PostAuthTransaction",
  "transactionAmount": {
    "total": "12.04",
    "currency": "USD"
  },
  "splitShipment": {
    "totalCount": 1,
    "finalShipment": true
  }
}
```

To void the Post-Auth, thereby re-opening the Pre-Auth, POST a voidTransaction request type as a secondary transaction setting the ```orderId``` value from the postAuth response as the {transaction-id}. To void the Pre-Authorization transaction, POST a voidPreAuthTransaction request type as a secondary transaction setting the ```orderId``` value from the Pre-Authorization response as the {transaction-id}.

---

## See Also

- [3-D Secure](?path=docs/english/payments/3-5-3d-secure.md)
- [Card Verification](?path=docs/english/payments/3-6-card-verification.md)
- [Currency Conversion](?path=docs/english/payments/3-7-currency-conversion.md)
- [Error codes](?path=docs/english/payments/3-13-error-codes.md)
- [Managed Redirect](?path=docs/english/payments/3-8-managed-redirect.md)
- [Orders](?path=docs/english/payments/3-9-orders.md)
- [Payment Methods](?path=docs/english/payments/3-2-payment-methods.md)
- [Payment Types](?path=docs/english/payments/3-3-payment-types.md)
- [Payment URL](?path=docs/english/payments/3-10-payment-url.md)
- [Recurring Payments](?path=docs/english/payments/3-11-recurring-payments.md)
- [Request Types](?path=docs/english/payments/3-1-request-types.md)
- [Tokenization](?path=docs/english/payments/3-12-tokenisation.md)

---
