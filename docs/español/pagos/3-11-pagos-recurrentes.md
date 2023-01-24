---
tags: [Getting Started, Payments, Recurring Payments]
---

# Pagos recurrentes

## /payment-schedule

La API de programación de pago le permite crear y administrar un pago recurrente programado para un consumidor individual. Esta API cubre capacidades de creación, actualización, cancelación y generación de informes. Como ejemplo, usaría esta API para configurar pagos de suscripción mensuales contra un método de pago si está proporcionando productos o servicios continuos a un cliente.

La API de programación de pagos usa el mismo encabezado y la mayoría de los mismos objetos que la API de pagos, por lo que si ya ha implementado esa API, debería ser fácil de ampliar.

## Creación de un pago programado

```json
{ 
  method: "POST",
  url: 'https://prod.api.firstdata.com/ipp/payments-gateway/v2/payment-schedules',
  headers: {
    "content-type": 'application/json',
    "client-request-id": '',
    "api-key": '',
    "timestamp": '',
    "message-signature": ''
  }
  "body"{
    "requestType": "PaymentMethodPaymentSchedulesRequest",
    "startDate": "2020-11-16",
    "numberOfPayments": 10,
    "maximumFailures": 1,
    "invoiceNumber": "96126098",
    "purchaseOrderNumber": "123055342",
    "transactionOrigin": "ECOM",
    "dynamicMerchantName": "MyWebsite",
    "frequency": {
      "every": 1,
      "unit": "MONTH"
    },
    "paymentMethod": {
      "paymentCard": {
        "number": "5426064000424979",
        "expiryDate": {
          "month": "12",
          "year": "20"
        },
        "securityCode": "147",
        "cardFunction": "CREDIT"
      }
    },
    "transactionAmount": {
      "total": "25.24",
      "currency": "GBP"
    },
  }
}
```

Para crear un pago programado cuando el cliente está pagando, capture los detalles de pago de los clientes para cualquier método de pago que estén usando como lo haría al procesar un pago único normal. Además de la información de pagos estándar, debe agregar más atributos para configurar correctamente el pago programado:

| Atributo               | Explacación                                                                                                                                                                                                                                                                                                                                               |
|------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```startDate```        | La fecha de inicio indica la fecha del primer pago. Esto puede ser fechado en el futuro. Este valor establece la línea base para el cálculo del próximo pago, dependiendo de los valores ```every``` y ```unit``` definidos en el objeto ```frequency```.                                                                                                 |
| ```numberofPayments``` | Esto define el número total de instancias de aceptación de un pago programado.                                                                                                                                                                                                                                                                            |
| ```maximumFailures```  | Esto establece el límite de reintentos para el pago. Si falla una vez, puede configurar nuestra plataforma para que vuelva a intentarlo automáticamente hasta 999 veces. El período entre reintentos siempre es de 1 día, por lo tanto, debe asegurarse de que el máximo de reintentos sea menor que la cantidad de días entre pagos según lo programado. |
| ```every```            | Establece el incremento para la ejecución de los pagos recurrentes, en asociación con el valor en el campo de la unidad                                                                                                                                                                                                                                   |
| ```unit```             | Establece la frecuencia, ya sea ```DAY```, ```WEEK```, ```MONTH``` o ```YEAR```                                                                                                                                                                                                                                                                           |

Entonces, si configura ```startDate``` como el 1 de diciembre de 2020, ```numberofPayments``` a 12, ```every``` a 1 y ```unit``` a MONTH, el último pago se realizará el 1 de noviembre de 2021, con un pago realizado el 1 de cada mes intermedio.

```json
{
  "requestType": "PaymentMethodPaymentSchedulesRequest",
  "startDate": "2020-11-16",
  "numberOfPayments": 10,
  "maximumFailures": 1,
  "invoiceNumber": "96126098",
  "purchaseOrderNumber": "123055342",
  "transactionOrigin": "ECOM",
  "dynamicMerchantName": "MyWebsite",
  "frequency": {
    "every": 1,
    "unit": "MONTH"
  },
  "paymentMethod": {
    "paymentCard": {
      "number": "5426064000424979",
      "expiryDate": {
        "month": "12",
        "year": "20"
      },
      "securityCode": "147",
      "cardFunction": "CREDIT"
    }
  },
  "transactionAmount": {
    "total": "25.24",
    "currency": "GBP"
  },
  "clientLocale": {
    "language": "EN",
    "country": "UK"
  }
}
```

También puede usar programas de pago para establecer pagos únicos con fecha futura, configurando el número de pagos en 1 y la fecha de inicio en una fecha futura. Siempre que el valor ```every``` se establezca en 1, esto es lo mismo que establecer un único pago con fecha futura.

## Uso de una orden de referencia para crear un pago programado

Para crear un pago programado utilizando un pago único ya enviado, envíe la carga útil según se indica a continuación a la API de /payment-schedule, pero incluya el atributo ```referenceOrderId``` como ID de pedido de la respuesta del pago original. Esto es útil cuando un cliente compra algo como una sola compra, pero luego elige continuar su acceso como una suscripción a su producto o servicio.

```json
{
  "requestType": "ReferencedOrderPaymentSchedulesRequest",
  "referencedOrderId": "R-f9c2c198-b7cc-491a-a711-93d22fd0e589",
  "startDate": "2020-11-16",
  "numberOfPayments": 10,
  "maximumFailures": 1,
  "invoiceNumber": "96126098",
  "purchaseOrderNumber": "123055342",
  "transactionOrigin": "ECOM",
  "dynamicMerchantName": "MyWebsite",
  "frequency": {
    "every": 1,
    "unit": "MONTH"
  },
  "transactionAmount": {
    "total": "25.24",
    "currency": "GBP"
  },
  "clientLocale": {
    "language": "EN",
    "country": "USA"
  }
}
```

## Actualización de un pago programado

Para actualizar un pago programado, use PATCH para hacer una llamada a /pago-programas/{order-id} donde el valor de {order-id} es el valor ```orderId``` de la respuesta del ``` original PaymentSchedulesRequest``` POST. Use la misma estructura de carga útil que para configurar el cronograma de pago, pero identifique el cronograma de pago para la actualización especificando ```orderId``` en la respuesta de ```PaymentSchedulesRequest``` original en /payment-schedules/{ Solicitar ID}. Realice actualizaciones en el programa de pago, el método de pago o el monto de la transacción según sea necesario en la carga útil de la llamada PATCH.

## Cancelación de un pago programado

Cancele un pago programado haciendo una llamada DELETE a /payment-schedules/{order-id} donde el valor {order-id} es el valor ```orderId``` de la respuesta de la ```PaymentSchedulesRequest`` original ` POST.

## Recuperación de datos del calendario de pagos

Recupere los datos del calendario de pagos haciendo una llamada GET a /payment-schedules/{order-id} donde el valor de {order-id} es el valor ```orderId``` de la respuesta de la ```PaymentSchedulesRequest`` original ` POST.

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
- [Tokenización](?path=docs/3-12-tokenisation.md)
- [Códigos de Error](?path=docs/3-13-error-codes.md)

---
