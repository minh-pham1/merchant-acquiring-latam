---
tags: [Getting Started, Payments, Error codes]
---

# Error codes

## AppKey missing in request, 400 BAD REQUEST

```json
{
    "clientRequestId": "12345",
    "responseType": "BadRequest",
    "error": {
        "code": "400",
        "message": "The request is invalid or not properly formed"
    }
}
```

## Invalid Api Key, 401 Unauthorized

```json
{
    "clientRequestId": "12345",
    "responseType": "Unauthorized",
    "error": {
        "code": "401",
        "message": "Invalid API key"
    }
} 
```

## Invalid HMAC, 403 Forbidden

```json
{
    "clientRequestId": "4835065",
    "responseType": "Forbidden",
    "error": {
        "code": "403",
        "message": "Message Signature validation error"
    }
} 
```

## Expired HMAC, 403 Forbidden

```json
{
    "clientRequestId": "551228",
    "responseType": "Forbidden",
    "error": {
        "code": "403",
        "message": "Message Signature has expired"
    }
} 
```

## Invalid URL or invalid query parameter for /payment-url, 404 NotFound

```json
{
    "clientRequestId": "2952405",
    "responseType": "NotFound",
    "error": {
        "code": "404",
        "message": "The requested operation failed because a resource associated with the request could not be found"
    }
}
```

## OAuth Token Invalid or expired, 401 Unauthorized

```json
{
    "clientRequestId": "3091653",
    "responseType": "Unauthorized",
    "error": {
        "code": "401",
        "message": "The token is invalid. It may have expired, or is maybe incorrect"
    }
} 
```

## Message-Signature or Authorization Header missing, 401 Unauthorized

```json
{
    "responseType": "Unauthorized",
    "error": {
        "code": "401",
        "message": "Not authorized to access the resource"
    }
} 
```

## Default Error for unhandled exceptions, 500 ServerError

```json
{
    "clientRequestId": "3091653",
    "responseType": "ServerError",
    "error": {
        "code": "500",
        "message": "The request failed due to an internal error"
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
- [Card Verification](?path=docs/english/payments/3-6-card-verification.md)
- [Currency Conversion](?path=docs/english/payments/3-7-currency-conversion.md)
- [Managed Redirect](?path=docs/english/payments/3-8-managed-redirect.md)
- [Orders](?path=docs/english/payments/3-9-orders.md)
- [Payment URL](?path=docs/english/payments/3-10-payment-url.md)
- [Recurring Payments](?path=docs/english/payments/3-11-recurring-payments.md)
- [Tokenisation](?path=docs/english/payments/3-12-tokenisation.md)

---
