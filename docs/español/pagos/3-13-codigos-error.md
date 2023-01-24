---
tags: [Getting Started, Payments, Error codes]
---

# Códigos de Error

## Falta AppKey en la solicitud, 400 BAD REQUEST

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

## Api Key Inválido, 401 Unauthorized

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

## HMAC Inválido, 403 Forbidden

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

## HMAC Caducado, 403 Forbidden

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

## URL no válida o parámetro de consulta no válido para /payment-url, 404 NotFound

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

## Token de OAuth no válido o caducado, 401 Unauthorized

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

## Falta la firma del mensaje o el encabezado de autorización, 401 Unauthorized

```json
{
    "responseType": "Unauthorized",
    "error": {
        "code": "401",
        "message": "Not authorized to access the resource"
    }
} 
```

## Error predeterminado para excepciones no controladas, 500 ServerError

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

## Ver más

- [Tipos de Solicitud](?path=docs/español/pagos/3-1-tipos-solicitudes.md)
- [Métodos de Pagos](?path=docs/español/pagos/3-2-metodos-pago.md)
- [Formas de Pagos](?path=docs/español/pagos/3-3-formas-pagos.md)
- [Pos-autorización & Devoluciones](?path=docs/español/pagos/3-4-post-aut.md)
- [3-D Secure](?path=docs/español/pagos/3-5-3d-secure.md)
- [Verificación de Tarjeta](?path=docs/español/pagos/3-6-verificacion-tarjeta.md)
- [Conversion de Moneda](?path=docs/español/pagos/3-7-conversion-moneda.md)
- [Redirección Administrada](?path=docs/español/pagos/3-8-redireccion-administrada.md)
- [Pedidos](?path=docs/español/pagos/3-9-pedidos.md)
- [URL Pago](?path=docs/español/pagos/3-10-pago-url.md)
- [Pagos Recurrentes](?path=docs/español/pagos/3-11-pagos-recurrentes.md)
- [Tokenización](?path=docs/español/pagos/3-12-tokenizacion.md)

---
