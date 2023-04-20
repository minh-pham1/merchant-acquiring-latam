---
tags: [Getting Started, Payments, Post-authorisation & Returns]
---

# Post-autorización y Devoluciones

## /payments/{transaction-id}

Use transacciones secundarias para anular una transacción original, devolver contra una transacción original o para completar una transacción posterior a la autenticación. El parámetro ```transactionId```, completado para la transacción original que requiere una acción secundaria, debe completarse para cada uno de estos tipos de solicitudes.

Para cancelar la transacción original (el mismo día que la transacción original), use el tipo de solicitud ```voidTransaction```. Para devolver (revertir el día siguiente) una transacción original, use ```returnTransaction``` como tipo de solicitud. Para completar una transacción preautorizada usando una transacción posterior a la autorización, haga referencia a la transacción original en los datos del parámetro, luego coloque un POST usando el esquema ```PostAuth```.

A continuación se muestra una versión actualizada del diagrama de matriz de decisiones que se proporcionó anteriormente, con los tipos de solicitud de transacciones secundarias ahora incluidos.

![Decision Matrix!](/assets/images/3-4-decision-matrix.png "Decision Matrix")

Las transacciones secundarias también se basan en tipos de solicitud. La siguiente tabla proporciona enlaces a los esquemas de tipo de solicitud y proporciona el método a utilizar. En todas estas transacciones, el atributo de ID de transacción debe completarse con el valor devuelto en el mensaje de respuesta 200 en el campo ```ipgTransactionId``` para la transacción principal relevante.

## API Endpoint

Puede encontrar el endpoint aquí /payments/{transaction-id}

Para recuperar el estado de una transacción que ya envió, realice una llamada GET al punto final ```/payments/{transaction-id}```. La puerta de enlace devolverá los detalles y el estado de la transacción que envió.

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

Los tipos de solicitud disponibles se enumeran a continuación, con una explicación de para qué se utiliza cada uno de ellos.

| requestType            | Método | Descripción                                                                                                                      |
|------------------------|--------|----------------------------------------------------------------------------------------------------------------------------------|
| VoidTransaction        | POST   | El tipo de solicitud VoidTransaction le permite cancelar una transacción que envió anteriormente el mismo día                    |
| VoidPreAuthTransaction | POST   | El tipo de solicitud VoidTransaction le permite cancelar una transacción de autorización previa                                  |
| PostAuthTransaction    | POST   | El tipo de solicitud PostAuthTransaction le permite completar una transacción de autorización previa contra el mismo             |
| ReturnTransaction      | POST   | El tipo de solicitud ReturnTransaction le permite completar una devolución contra una transacción realizada antes del día actual |
| Transaction Inquiry    | GET    | Ejecute una simple llamada GET contra el punto final con el valor ipgtransactionid de la transacción que desea consultar         |

## Escenarios de pago adicionales

Hay una serie de escenarios comerciales que requieren combinaciones de diferentes llamadas al mismo o diferentes puntos finales. Los ejemplos a continuación demuestran la forma en que se pueden realizar diferentes tipos de solicitudes y llamadas a la API /payments para generar diferentes resultados de pagos.

### Incrementamndo o disminuyendo una autenticación previa

Para aumentar o disminuir el valor de una transacción de autorización previa, envíe otra autorización previa para el mismo titular de la tarjeta que haga referencia al valor del campo ```orderId``` de la respuesta asociada con la transacción de autorización previa original:

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

La transacción de autorización previa original se incrementará/reducirá según lo establecido en su solicitud.

## Completar y anular transacciones de autorización previa

Para completar una autorización previa, POST una transacción posterior a la autenticación para completar la autorización previa, publique una transacción secundaria en /payments/{transaction-id} indicando el valor del campo ```orderId``` de la respuesta asociada con la transacción pre-autorización original en {transaction-id}. El objeto splitShipment habilita varias autorizaciones posteriores parciales en escenarios en los que hay varios envíos contra una única autorización previa original.

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

Para anular la autenticación posterior y, por lo tanto, volver a abrir la autenticación previa, POST un tipo de solicitud voidTransaction como una transacción secundaria configurando el valor ```orderId``` de la respuesta posterior a la autenticación como {transaction-id}. Para anular la transacción de autorización previa, POST un tipo de solicitud voidPreAuthTransaction como transacción secundaria configurando el valor ```orderId``` de la respuesta de autorización previa como {transaction-id}.

---

## Ver más

- [Tipos de Solicitud](?path=docs/español/pagos/3-1-tipos-solicitudes.md)
- [Métodos de Pagos](?path=docs/español/pagos/3-2-metodos-pago.md)
- [Formas de Pagos](?path=docs/español/pagos/3-3-formas-pagos.md)
- [3-D Secure](?path=docs/español/pagos/3-5-3d-secure.md)
- [Verificación de Tarjeta](?path=docs/español/pagos/3-6-verificacion-tarjeta.md)
- [Conversion de Moneda](?path=docs/español/pagos/3-7-conversion-moneda.md)
- [Redirección Administrada](?path=docs/español/pagos/3-8-redireccion-administrada.md)
- [Pedidos](?path=docs/español/pagos/3-9-pedidos.md)
- [URL Pago](?path=docs/español/pagos/3-10-pago-url.md)
- [Pagos Recurrentes](?path=docs/español/pagos/3-11-pagos-recurrentes.md)
- [Tokenización](?path=docs/español/pagos/3-12-tokenizacion.md)
- [Códigos de Error](?path=docs/español/pagos/3-13-codigos-error.md)

---
