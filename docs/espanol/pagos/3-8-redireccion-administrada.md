---
tags: [Getting Started, API Reference, Managed Redirect, Credit Card, 3-D Secure]
---

# Redirección administrada

La función de redirección administrada reduce significativamente el esfuerzo de desarrollo necesario para la integración de métodos de pago que requieren la redirección del consumidor. Una simple solicitud de API a la puerta de enlace genera una URL, que se utiliza en el siguiente paso para redirigir a sus clientes y permitir que nuestra página de pago alojada (Conectar) maneje el proceso de pago en su nombre.

## Tarjeta de crédito

El siguiente documento JSON representa un ejemplo de una solicitud para generar una URL de redirección para un pago con tarjeta de crédito:

```json
{
    "requestType": "PaymentCardManagedRedirectRequest",
    "storeId": "1109950006",
    "transactionAmount": {
        "total": "14",
        "currency": "EUR"
    },
    "transactionType": "SALE",
    "paymentMethod": {
        "paymentCard": {
            "number": "403587******4977",
            "securityCode": "***",
            "cardFunction": "CREDIT",
            "expiryDate": {
                "month": "12",
                "year": "24"
           }
        }
      }
    }
```

En la respuesta de Gateway, recibe el parámetro 'redirectURL', que puede pasar a sus consumidores para completar su pago.

El siguiente documento JSON representa un ejemplo de una respuesta:

```json
{
   "clientRequestId": "a35e3995-e74a-4e25-ad49-849de6bbabca",
   "apiTraceId": "Ymf1-1ohKkLOqSdsIKi3AQAAA6c",
   "requestStatus": "SUCCESS",
   "orderId": "R-0229343b-aeed-411e-9496-c1fd73dafae5",
   "redirectURL": "https://test3.ipg-online.com/connect/gateway/processing?storename=1109950006&oid=R-0229343b-aeed-411e-9496-c1fd73dafae5&managedRedirectUrlId=84af185c-5ec9-45ea-952a-ee4497726178",
   "transactionId": "84400086815"
}
```

> 🚧 Nota. Tenga en cuenta que después de que su cliente complete su pago, recibirá una respuesta con formato HTML en lugar de una respuesta con formato JSON.
<!-- theme: warning -->

## 3-D Secure

Para los casos en los que requiera que se incluya la autenticación 3-D Secure, debe incluir el objeto 'redirectAttributes' adicional en su solicitud inicial.

|          Nombre redirectAttribute         |  Obligatorio/Opcional |                                                                                                                                                                                Descripción                                                                                                                                                                               |
| ----------------------------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| ```authenticateTransaction```             | obligatorio           | Indica si se solicita una autenticación 3-D Secure para esta transacción. Valores aceptados: • verdadero                                                                                                                                                                                                                                                                 |
| ```challengeIndicator```                  | obligatorio           | Indica si se solicita un desafío para esta transacción. Si no se envía en la solicitud, se completa automáticamente un valor predeterminado '01'. Valores aceptados: • 01 = Sin preferencia • 02 = Sin desafío solicitado • 03 = Desafío solicitado (preferencia del Solicitante 3DS) • 04 = Desafío solicitado (Mandato)                                                |
| ```language```                            | obligatorio           | Valor que representa el idioma del navegador según se define en IETF BCP47, ejemplo de idiomas de uso frecuente: Inglés: en Español: es Francés: fr Alemán: de Portugués: pt Hindi: hi                                                                                                                                                                                   |
| ```tresDSEmvCoMessageCategory```          | opcional              | Identifica la categoría del mensaje para un caso de uso específico. Si no se envía en la solicitud, se completa automáticamente un valor predeterminado '01'. Valores aceptados: • 01 = Autenticación de pago (valor predeterminado) • 02 = Autenticación de no pago                                                                                                     |
| ```mobileMode```                          | opcional              | Booleano que representa la solicitud para utilizar el modo móvil de páginas de pago alojadas. Valores aceptados: • verdadero • falso                                                                                                                                                                                                                                     |
| ```browserJavaScriptEnabled```            | opcional              | Booleano que representa la capacidad del navegador del titular de la tarjeta para ejecutar JavaScript. Valores aceptados: • verdadero • falso                                                                                                                                                                                                                            |
| ```tresDSTransactionType```               | opcional              | Identifica el tipo de transacción que se está autenticando. Si no se envía en la solicitud, se completa automáticamente un valor predeterminado '01'. Valores aceptados: • 01 = Compra de bienes/servicios (valor predeterminado) • 03 = Aceptación de cheques • 10 = Financiamiento de cuenta • 11 = Transacción de cuasi-efectivo • 28 = Activación y carga de prepago |
| ```skipTRA```                             | opcional              | Booleano que representa la capacidad de omitir la recomendación de resultados del análisis de riesgos de transacciones en un nivel de transacción. Disponible solo para tiendas habilitadas con servicio TRA. Valores aceptados: • verdadero • falso                                                                                                                     |
| ```override3dsCountryExclusion```         | opcional              | Booleano que anula la configuración de exclusión de país de Dynamic 3-D Secure. Valores aceptados: • verdadero • falso                                                                                                                                                                                                                                                   |

El siguiente documento JSON representa un ejemplo de una transacción que incluye un conjunto mínimo de 'redirectAttributes' obligatorios:

```json
{
    "requestType": "PaymentCardManagedRedirectRequest",
    "storeId": "1109950006",
    "transactionAmount": {
        "total": "14",
        "currency": "EUR"
    },
    "transactionType": "SALE",
    "paymentMethod": {
        "paymentCard": {
            "number": "520474******2745",
            "securityCode": "XXX",
            "cardFunction": "CREDIT",
            "expiryDate": {
                "month": "12",
                "year": "24"
            }
        }
    },
    "redirectAttributes": {
        "language": "en",
        "challengeIndicator": "01",
        "authenticateTransaction": true
    }
}
```

El siguiente documento JSON representa un ejemplo de una respuesta que recibe del Gateway:

```json
{
   "clientRequestId": "9410557e-e451-4ed8-b37e-0f268a63f060",
   "apiTraceId": "YmuZy8URv@sTmvxBgZjBdQAAAr0",
   "requestStatus": "SUCCESS",
   "orderId": "R-432da132-f610-4714-91a3-3500a02c5863",
   "redirectURL": "https://test3.ipg-online.com/connect/gateway/processing?storename=1109950006&oid=R-432da132-f610-4714-91a3-3500a02c5863&managedRedirectUrlId=9c55f38b-bd57-455a-92ab-90b580306e0f",
   "transactionId": "84400249138"
}
```

### Pagos locales

Si su cuenta está inscrita en el modelo de pagos locales colectivos, puede enviar un método de pago individual en su ```GenericManagedRedirectRequest``` a nuestro Gateway.

### POST /ipgrestapi/v2/services/managed-redirect

```json
{
  "requestType": "GenericManagedRedirectRequest",
  "storeId": "120995000",
  "transactionAmount": {
    "total": "15",
    "currency": "EUR"
    },
  "transactionType": "SALE",
  "paymentMethod": {
      "paymentMethodType": "IDEAL_APM"
    }
}
```

En la respuesta, recibirá un ```redirectURLparameter```, que puede pasar a su consumidor para completar el pago.

El siguiente documento JSON representa un ejemplo de la respuesta:

```json
{
   "clientRequestId": "67db6b7e-37d8-4dbd-8b8f-28749a9b0770",
   "apiTraceId": "Yufk72hKKznZUrdK7lqvnwAAA3U",
   "requestStatus": "SUCCESS",
   "orderId": "R-8e6b5d51-842a-4e8d-9231-99450be41f59",
   "redirectURL": "https://test3.ipg-online.com/connect/gateway/processing?storename=120995000&oid=R-8e6b5d51-842a-4e8d-9231-99450be41f59&managedRedirectUrlId=f29084b5-78da-41d8-8ea5-870926f2f516",
   "transactionId": "84405921374"
}
```

Se han habilitado los siguientes métodos de pago para la solución de redirección gestionada:

- ```ALIPAY```
- ```BCMC_APM```
- ```EPS```
- ```GIROPAY_APM```
- ```IDEAL_APM```
- ```MYBANK```
- ```PAYSAFECARD```
- ```PRZELEWY24```
- ```SOFORT_APM```
- ```TRUSTLY```

---

## Ver más

- [Tipos de Solicitud](?path=docs/español/pagos/3-1-tipos-solicitudes.md)
- [Métodos de Pagos](?path=docs/español/pagos/3-2-metodos-pago.md)
- [Formas de Pagos](?path=docs/español/pagos/3-3-formas-pagos.md)
- [Pos-autorización & Devoluciones](?path=docs/español/pagos/3-4-post-aut.md)
- [3-D Secure](?path=docs/español/pagos/3-5-3d-secure.md)
- [Verificación de Tarjeta](?path=docs/español/pagos/3-6-verificacion-tarjeta.md)
- [Conversion de Moneda](?path=docs/español/pagos/3-7-conversion-moneda.md)
- [Pedidos](?path=docs/español/pagos/3-9-pedidos.md)
- [URL Pago](?path=docs/español/pagos/3-10-pago-url.md)
- [Pagos Recurrentes](?path=docs/español/pagos/3-11-pagos-recurrentes.md)
- [Tokenización](?path=docs/español/pagos/3-12-tokenizacion.md)
- [Códigos de Error](?path=docs/español/pagos/3-13-codigos-error.md)

---
