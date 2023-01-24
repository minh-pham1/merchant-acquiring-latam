---
tags: [Getting Started, Payments, Card Verification]
---

# Verificación de Tarjeta

## /card-verification 

Utilice el servicio Verificación de tarjeta para verificar que una tarjeta esté disponible para cargar antes de que un cliente la use para pagar. Envíe la carga útil a continuación utilizando un método POST a la API /card-verification y realizaremos una autorización de valor cero contra la tarjeta para asegurarnos de que no sea fraudulenta, esté en la lista negra, caduque o esté bloqueada. Esencialmente, nos permite verificar si la tarjeta y la cuenta están al día y disponibles para realizar el pago.

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

A continuación se muestra un ejemplo de una carga útil de verificación de tarjeta (solo cuerpo).

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

## Ver más

- [Tipos de Solicitud](?path=docs/español/pagos/3-1-tipos-solicitudes.md)
- [Métodos de Pagos](?path=docs/español/pagos/3-2-metodos-pago.md)
- [Formas de Pagos](?path=docs/español/pagos/3-3-formas-pagos.md)
- [Pos-autorización & Devoluciones](?path=docs/español/pagos/3-4-post-aut.md)
- [3-D Secure](?path=docs/español/pagos/3-5-3d-secure.md)
- [Conversion de Moneda](?path=docs/español/pagos/3-7-conversion-moneda.md)
- [Redirección Administrada](?path=docs/español/pagos/3-8-redireccion-administrada.md)
- [Pedidos](?path=docs/español/pagos/3-9-pedidos.md)
- [URL Pago](?path=docs/español/pagos/3-10-pago-url.md)
- [Pagos Recurrentes](?path=docs/español/pagos/3-11-pagos-recurrentes.md)
- [Tokenización](?path=docs/español/pagos/3-12-tokenizacion.md)
- [Códigos de Error](?path=docs/español/pagos/3-13-codigos-error.md)

---
