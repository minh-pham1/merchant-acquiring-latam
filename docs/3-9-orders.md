---
tags: [Getting Started, Payments, Orders]
---

# Orders

## /orders/{order-id}

The /orders API enables you to post a secondary transaction using your own order ID. Instead of submitting the POST with a return or postauth requestType and the ```ipgTransactionId```, you can use your ```orderId``` value, the one you provided to us in the Order object in the primary transaction submitted to /payments. In addition, you can use GET to pull details of the primary transaction using the same ```orderId``` reference.

```json
{
  "method": "get",
  "url": "https://prod.api.firstdata.com/ipp/payments-gateway/v2/payments/IPG001001001",
  "query": {},
  "headers": {
    "Content-Type": "application/json",
    "Client-Request-Id": "",
    "Api-Key": "",
    "Timestamp": "",
    "Message-Signature": ""
  }
}
```


---

## See Also

- [Request Types](?path=docs/3-1-request-types.md)
- [Payment Methods](?path=docs/3-2-payment-methods.md)
- [Payment Types](?path=docs/3-3-payment-types.md)
- [Post-authorisation & Returns](?path=docs/3-4-post-auth.md)
- [3-D Secure](?path=docs/3-5-3d-secure.md)
- [Card Verification](?path=docs/3-6-card-verification.md)
- [Currency Conversion](?path=docs/3-7-currency-conversion.md)
- [Managed Redirect](?path=docs/3-8-managed-redirect.md)
- [Payment URL](?path=docs/3-10-payment-url.md)
- [Recurring Payments](?path=docs/3-11-recurring-payments.md)
- [Tokenisation](?path=docs/3-12-tokenisation.md)
- [Error codes](?path=docs/3-13-error-codes.md)

---
