---
tags: [Getting Started, Payments, Card Verification]
---

# Card Verification

## /card-verification 

Use the Card Verification service to check a card is available for charging before a customer uses it to pay. Submit the payload below using a POST method to the /card-verification API and we'll perform a zero value authorisation against the card to ensure it isn't fraudulent, blacklisted, expired or blocked. Essentially it lets us check whether the card and account are in good standing and available to make payment.

```json
{ 
  method: "POST",
  url: "https://prod.api.firstdata.com/ipp/payments-gateway/v2/card-verification",
  headers: {
    "content-type": "application/json",
    "client-request-id": "",
    "api-key": "",
    "timestamp": "",
    "message-signature": ""
  }
  "body": {
    "paymentCard": {
      "number": "4035874000424977",
      "expiryDate": {
        "month": "12", 
        "year": "20"
      },
      "securityCode": "977"
    },
    "billingAddress": {
      "address1": "5565 Glenridge Conn",
      "city": "Atlanta",
      "postalCode": "30342",
      "country": "USA"
    }
  }
}
```

Below is an example of a card verification payload (body only).

```json
{
  "paymentCard": {
    "number": "4035874000424977",
    "expiryDate": {
      "month": "12",
      "year": "20"
    },
    "securityCode": "977"
  },
  "billingAddress": {
    "address1": "5565 Glenridge Conn",
    "city": "Atlanta",
    "postalCode": "30342",
    "country": "USA"
  }
}
```

---

## See Also

- [Request Types](?path=docs/english/payments/3-1-request-types.md)
- [Payment Methods](?path=docs/english/payments/3-2-payment-methods.md)
- [Payment Types](?path=docs/english/payments/3-3-payment-types.md)
- [Post-authorisation & Returns](?path=docs/english/payments/3-4-post-auth.md)
- [3-D Secure](?path=docs/english/payments/3-5-3d-secure.md)
- [Currency Conversion](?path=docs/english/payments/3-7-currency-conversion.md)
- [Managed Redirect](?path=docs/english/payments/3-8-managed-redirect.md)
- [Orders](?path=docs/english/payments/3-9-orders.md)
- [Payment URL](?path=docs/english/payments/3-10-payment-url.md)
- [Recurring Payments](?path=docs/english/payments/3-11-recurring-payments.md)
- [Tokenisation](?path=docs/english/payments/3-12-tokenisation.md)
- [Error codes](?path=docs/english/payments/3-13-error-codes.md)

---
