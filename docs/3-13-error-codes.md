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
