---
tags: [Getting Started, Payments, Payment URL]
---

# Pago URL

## /payment-url

La URL de pago es una funcionalidad que le permite proporcionar un enlace a sus clientes (por ejemplo, en una factura por correo electrónico, mensaje de WhatsApp, SMS, código QR, etc.) que luego lleva al cliente a una página alojada en Fiserv para realizar el pago de forma segura con su método de pago preferido, siempre que sea conveniente para ellos.

Esto es especialmente útil en escenarios en los que los bienes se pagan después de la entrega, donde no se envía ningún producto (por ejemplo, el pago final de viajes que se reservaron hace meses) o para el pago de facturas mensuales.

También puede implementar esta funcionalidad para compras fallidas en las que se rechazó la transacción de pago original, de modo que pueda brindar de manera proactiva a su cliente una segunda oportunidad para realizar su compra.

La puerta de enlace proporciona:

- La capacidad de solicitar una URL de pago (enlace) por un monto específico a través de la API

- Una página de pago alojada donde el cliente puede seleccionar el método de pago preferido (según los métodos de pago que están activados para su cuenta) y realizar el pago

- Una página de resultados alojada que le dice al cliente si el pago fue exitoso o no, incluido un botón Reintentar donde el cliente puede elegir un método de pago diferente en caso de que la transacción no se haya realizado correctamente.

## Creación de una URL de pago

Use POST para realizar una llamada a /payment-url y especifique los valores para la carga útil como se define en la siguiente tabla.

| **Atributo**                     | **Explicación**                                                                                                                                                                                                                                                      |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```transactionType```            | Este atributo le dice a nuestra plataforma qué tipo de transacción ejecutar cuando el cliente completa el pago a través de la URL. Para aceptar un pago, use VENTA. Para crear una autorización previa, utilice PREAUTH. Para acreditar al cliente, utilice CRÉDITO. |
| ```transactionNotificationURL``` | Establezca la URL en la que desea que se publique la notificación cuando el cliente haya completado el pago                                                                                                                                                          |
| ```expiration```                 | Esta es la fecha en la que la URL caducará y el cliente ya no podrá utilizarla.                                                                                                                                                                                      |
| ```authenticateTransaction```    | Establece en VERDADERO para que la transacción sea autenticada por 3-D Secure (esto sucederá dentro de nuestra página alojada, no a través del flujo de 3-D Secure impulsado por la API REST)                                                                        |
| ```dynamicMerchantName```        | Esto establece el nombre del comerciante que aparecerá en el estado de cuenta de la tarjeta del cliente, por lo que puede configurarlo como desee que aparezca allí.                                                                                                 |
| ```invoiceNumber```              | Ponga su número de factura aquí.                                                                                                                                                                                                                                     |
| ```purchaseOrderNumber```        | Ponga aquí el número de orden de compra del cliente.                                                                                                                                                                                                                 |
| ```hostedPaymentPageText```      | Esto establece el texto que aparecerá en la página de pago alojada que el cliente utiliza para realizar el pago.                                                                                                                                                     |

El siguiente ejemplo crea una URL para un pago de venta para enviar a un cliente.

```json
{
  "transactionAmount": {
    "total": "42.42",
    "currency": "EUR"
  },
  "transactionType": "SALE",
  "transactionNotificationURL": "https://mywebshop/Customer",
  "expiration": "4102358400",
  "authenticateTransaction": true,
  "dynamicMerchantName": "MyWebsite",
  "invoiceNumber": "96126098",
  "purchaseOrderNumber": "123055342",
  "hostedPaymentPageText": "Dear Customer, please pay our invoice 123456. "
}
```

El generador de muestra a continuación muestra los detalles asociados para la API /payments-url.

```json
{
  "method": "post",
  "url": "https://prod.api.firstdata.com/ipp/payments-gateway/v2/payment-url",
  "headers": {
    "Content-Type": "application/json",
    "Client-Request-Id": "",
    "Api-Key": "",
    "Timestamp": "",
    "Message-Signature": ""
  },
  "body": {
    "transactionAmount": {
      "total": "42.42",
      "currency": "USD"
    },
    "transactionType": "SALE",
    "transactionNotificationURL": "https://mywebshop/notification",
    "expiration": "4102358400",
    "authenticateTransaction": true,
    "dynamicMerchantName": "MyWebsite",
    "invoiceNumber": "96126098",
    "purchaseOrderNumber": "123055342",
    "hostedPaymentPageText": "Dear Customer, please pay our invoice 123456."
  }
}
```

## Otras funciones de URL de pago

Para eliminar una URL de pago, agregue su ```storeId``` y ```transactionId``` de la respuesta de solicitud de creación de URL de pago (```ipgTransactionId```) al encabezado /payment-url, y use el método DELETE para llamar a la API /payment-url.

Puede recuperar los datos asociados con una URL de pago enviando un GET a /paymentURL. Agregue ```storeId``` y ```transactionId``` de la respuesta de solicitud de creación de URL de pago (```ipgTransactionId```) al encabezado /payment-url para recuperar los detalles de una URL de pago específica, o establezca ```storeId```, ```fromDate``` y ```toDate``` para llamar a una respuesta con todas las URL de pago y sus detalles creados dentro de un rango de tiempo específico.

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
- [Pagos Recurrentes](?path=docs/3-11-recurring-payments.md)
- [Tokenización](?path=docs/3-12-tokenisation.md)
- [Códigos de Error](?path=docs/3-13-error-codes.md)

---
