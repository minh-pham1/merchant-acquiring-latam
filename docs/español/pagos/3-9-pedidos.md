---
tags: [Getting Started, Payments, Orders]
---

# Pedidos

## /orders/{order-id}

La API /orders le permite publicar una transacción secundaria utilizando su propia ID de pedido. En lugar de enviar el POST con un tipo de solicitud de devolución o postautorización y ```ipgTransactionId```, puede usar su valor de ```orderId```, el que nos proporcionó en el objeto Pedido en la transacción principal enviada a /pagos. Además, puede usar GET para obtener detalles de la transacción principal usando la misma referencia ```orderId```.

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

## Ver más

- [Tipos de Solicitud](?path=docs/español/pagos/3-1-tipos-solicitudes.md)
- [Métodos de Pagos](?path=docs/español/pagos/3-2-metodos-pago.md)
- [Formas de Pagos](?path=docs/español/pagos/3-3-formas-pagos.md)
- [Pos-autorización & Devoluciones](?path=docs/español/pagos/3-4-post-aut.md)
- [3-D Secure](?path=docs/español/pagos/3-5-3d-secure.md)
- [Verificación de Tarjeta](?path=docs/español/pagos/3-6-verificacion-tarjeta.md)
- [Conversion de Moneda](?path=docs/español/pagos/3-7-conversion-moneda.md)
- [Redirección Administrada](?path=docs/español/pagos/3-8-redireccion-administrada.md)
- [URL Pago](?path=docs/3-10-payment-url.md)
- [Pagos Recurrentes](?path=docs/3-11-recurring-payments.md)
- [Tokenización](?path=docs/3-12-tokenisation.md)
- [Códigos de Error](?path=docs/3-13-error-codes.md)

---
