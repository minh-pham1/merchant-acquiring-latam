---
tags: [Payments, API, Header, Parameters, Polymorphism, Primary Transaction]
---

# Pagos e-Commerce

Use esta API para aceptar pagos de todo tipo y para procesar acciones secundarias como anulaciones, reembolsos o finalizaciones previas a la autorización.

Puede utilizar nuestra API de pago para ofrecer una variedad de métodos de pago a sus clientes. Le permite recibir pagos utilizando el tipo de transacción más apropiado.

La API de pago de Fiserv admite los siguientes métodos de pago principales; ```Visa```, ```Mastercard```, ```Amex```, ```Apple Pay```, ```Google Pay```, ```Sepa```, así como muchos otros métodos de pago internacionales y locales. Para obtener acceso a métodos de pago más allá de este conjunto principal, comuníquese con su representante de servicios de integración.

Nuestros puntos finales de pagos se pueden usar para crear y completar un pago, crear y administrar un pago recurrente programado, generar un enlace de pago para enviar a un cliente, verificar la tarjeta de un cliente u ofrecerles la opción de pagar en su moneda local.

Una vez que haya ejecutado una transacción utilizando estas APIs, puede recuperar todos los detalles que necesita sobre la transacción utilizando nuestras APIs de Merchant Intelligence.

## Encabezado y Parametros

Nuestra API de pagos tiene una estructura de encabezado coherente basada en un conjunto de parámetros. Para crear el encabezado, proporcione los siguientes valores:

| Parametro de Encabezado | Tipo    | Descripción                                                                                                                                                                                                                        |
|-------------------------|---------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Content-Type            | String  | Defina este atributo como application/json                                                                                                                                                                                        |
| Client-Request-Id       | String  | Este es un ID que genera para que podamos realizar un seguimiento mutuo de sus solicitudes. Debe ser único por solicitud. Recomendamos que esté construido en formato UUID de 128 bits.                                           |
| Api-Key                 | String  | Esta es la clave que utilizará para identificarse con nosotros. Se le dará una clave para el desarrollo y las pruebas, luego una clave diferente cuando esté listo para comenzar.                                                 |
| Timestamp               | Integer | Debe definir esto como una marca de tiempo de época en milisegundos. Esto se utiliza para la generación de firmas de mensajes y para el control de límites de tiempo.                                                             |
| Message-Signature       | String  | Usamos esto por seguridad. La firma del mensaje es el hash HMAC codificado en Base64 (algoritmo SHA256 con el API Secret de la API como clave). Para obtener más información, consulte la documentación de apoyo en Developer Portal. |

El encabezado se construye según el siguiente ejemplo.

```json
{
  "method": "post",
  "url": "https://prod.emea.api.fiservapps.com/ipp/payments-gateway/v2/payments",
  "headers": {
    "Content-Type": "application/json",
    "Client-Request-Id": "",
    "Api-Key": "",
    "Timestamp": "",
    "Message-Signature": ""
  },
}
```

La generación de una firma de mensaje es un componente clave de esta API. Puedes aprender más aqui:

[Generate a message signature](?path=recipes/1-generate-message.md)


## Polimorfismo

Nuestra API de pagos es polimórfica, lo que significa que puede enviar varios tipos de solicitudes con diferentes cargas útiles al mismo recurso de API y generar diferentes tipos de pagos y respuestas.

El término se toma de la química: el polimorfismo es la capacidad de los materiales sólidos para existir en dos o más formas cristalinas con diferentes arreglos.

Cuando aplicamos esto al diseño de API, significa que la misma API se puede usar para múltiples acciones de pago. Habilitamos esto definiendo diferentes esquemas para el cuerpo de la carga útil, cada uno de los cuales se usa para una acción de pago.

El polimorfismo de nuestra API de pagos se basa en los tipos de solicitud (el campo ```requestType```), lo que le permite distinguir entre pagos de clientes según el tipo de transacción (```venta```, ```reembolso` ``, ```cancelación```, etc.) y el método de pago que el cliente desea utilizar (carrito de crédito o débito, monedero digital, SEPA, Paypal, etc.).

Utilizará el mismo endpoint para ejecutar un pago para todas estas variaciones, pero el valor ```requestType``` que envíe y los otros objetos en la carga útil variarán según el tipo de pago del cliente que esté intentando tomar.

Como ejemplo, usará el tipo de solicitud ```PaymentCardSaleTransaction``` para aceptar un pago normal con tarjeta cuando un cliente desea pagar. Puede utilizar un tipo de solicitud de transacción secundaria para reembolsar o anular la transacción.

## Ejemplo

```json
{
  "requestType": "PaymentCardSaleTransaction",
  "transactionAmount": {
    "total": "12.04",
    "currency": "USD"
  },
  "paymentMethod": {
    "paymentCard": {
      "number": "5424180279791732",
      "securityCode": "977",
      "expiryDate": {
        "month": "12",
        "year": "24"
      }
    }
  }
}
```

```json
{
  "requestType": "ReturnTransaction",
  "transactionAmount": {
    "total": 3,
    "currency": "USD"
  }
}
```

Estos ejemplos se muestran a continuación:

[Make a card payment](?path=recipes/2-card-payment.md)

[Refund a transaction](?path=recipes/3-refund-transaction.md)

## Cómo funciona el recurso

El endpoint ```/payments``` le permite ```crear```, ```consultar``` y ```finalizar``` transacciones de pago.

Le permitirá ```anular``` una transacción anterior, ```reembolsar``` una transacción anterior o ejecutar ```reembolsos parciales``` o ```anulaciones```. Finalmente, y cuando sea necesario, le permitirá ```preautorizar``` transacciones que pueden completarse más tarde.

La API le permite realizar pagos utilizando diferentes instrumentos de pago, a través de tarjetas de crédito o débito, tokens o mediante métodos de pago locales como SEPA DD o cuenta de Paypal. Todos estos métodos se explican a continuación.

Puede usar la API para aceptar transacciones de billetera a través de métodos de pago como Apple Pay o Google Pay. Le permitirá utilizar protocolos de autenticación adicionales para garantizar que los pagos de los clientes sean más seguros y para protegerlo contra el fraude.

Nuestra API de pagos tiene dos usos principales: transacciones primarias y secundarias. Las transacciones primarias son transacciones típicas de venta, preautorizaciones o créditos. Las transacciones secundarias le permiten reembolsar una transacción, anular una transacción o completar una preautorización.

## Creando una transacción 'primaria'

Las transacciones primarias se utilizan para ejecutar un pago de un cliente, una autorización previa o una transacción de crédito sin referencia a una transacción anterior. Para este tutorial, usaremos el tipo de solicitud PaymentCardSaleTransaction (todos los tipos de solicitud deben tener enlaces), que puede usar para una transacción de pago con tarjeta de crédito o débito normal sin referencia a una transacción anterior. En esta guía se explican cada uno de los diferentes tipos de solicitudes, los escenarios en los que es posible que desee utilizarlos y las diferencias en el JSON para cada tipo de solicitud.

El tipo de solicitud ```PaymentCardSaleTransaction``` requiere los siguientes campos para publicar una transacción principal.

```json
{
  "method": "post",
  "url": "https://prod.api.firstdata.com/ipp/payments-gateway/v2/payments",
  "headers": {
    "Content-Type": "application/json",
    "Client-Request-Id": "",
    "Api-Key": "",
    "Timestamp": "",
    "Message-Signature": ""
  },
  "body": {
    "requestType": "PaymentCardSaleTransaction",
    "transactionAmount": {
      "total": "12.04",
      "currency": "USD"
    },
    "paymentMethod": {
      "paymentCard": {
        "number": "5424180279791732",
        "securityCode": "977",
        "expiryDate": {
          "month": "12",
          "year": "24"
        }
      }
    }
  }
}
```

Dentro del modelo, los objetos más importantes son ```transactionAmount``` y ```paymentMethod```. Sin estos objetos, no podemos procesar el pago.

También le recomendamos que incluya información de facturación e información de envío, ya que esto permite que la verificación de huellas dactilares de 3DSecure se ejecute en segundo plano (consulte la página [Implementación de 3DSecure](?path=docs/3-5-3d-secure.md) para obtener más información) . Esto hace que el proceso de pago sea sencillo para su cliente y permite que nuestros sistemas antifraude lo protejan más fácilmente.

Tanto los objetos ```billing``` como ```shipping``` siguen la misma estructura:

```json
{
  "name": "Alec Leamas",
  "customerId": "1234567890",
  "contact": {
    "phone": "07777777777",
    "email": "alecleamas@tswciftc.com"
  }
  "address": {
    "address1": "Bernauer Strasse 111",
    "city": "Berlin",
    "postalCode": "13355",
    "country": "Germany"
  }
}
```

El siguiente flujo muestra un proceso de pago estándar mediante una transacción de pago con tarjeta.
![standard payments process!](/assets/images/3-payment-process.png "Standard payment process")

[Verify a card payment](?path=recipes/4-verify-card-payment.md)

[Look up card information](?path=recipes/5-card-information.md)

---

## Ver más

- [Tipos de Solicitud](?path=docs/español/pagos/3-1-tipos-solicitudes.md)
- [Métodos de Pagos](?path=docs/español/pagos/3-2-metodos-pago.md)
- [Formas de Pagos](?path=docs/español/pagos/3-3-formas-pagos.md)
- [Pos-autorización & Devoluciones](?path=docs/español/pagos/3-4-post-aut.md)
- [3-D Secure](?path=docs/español/pagos/3-5-3d-secure.md)
- [Verificación de Tarjeta](?path=docs/3-6-card-verification.md)
- [Conversion de Moneda](?path=docs/3-7-currency-conversion.md)
- [Redirección Administrada](?path=docs/3-8-managed-redirect.md)
- [Órdenes](?path=docs/3-9-orders.md)
- [URL Pago](?path=docs/3-10-payment-url.md)
- [Pagos Recurrentes](?path=docs/3-11-recurring-payments.md)
- [Tokenización](?path=docs/3-12-tokenisation.md)
- [Códigos de Error](?path=docs/3-13-error-codes.md)

---
