---
tags: [Getting Started, Payments, Tokenisation]
---

# Tokenización

## Tokens

Los tokens son útiles cuando se almacenan los detalles de los instrumentos de pago de un cliente para asegurar y acelerar el pago más adelante. Cuando recopila los métodos de pago de un cliente, puede solicitar un token, que luego puede usar en lugar de los detalles de la tarjeta de un cliente para ejecutar pagos futuros. Deberá almacenar el token en su plataforma, asignado a los detalles de la cuenta del cliente.

Los tokens son útiles ya que reducen el riesgo de que los ataques a sus sistemas de pago y a los nuestros tengan éxito. La tokenización también reduce el requisito de PCI para usted, ya que significa que no necesita almacenar los detalles de la tarjeta dentro de sus sistemas, lo que debería reducir sus costos asociados con el cumplimiento de PCI.

Para generar un Token para uso futuro al mismo tiempo que envía un pago, use el objeto createToken para configurar un Token para uso múltiple (establezca reutilizable en True). Además, puede crear su propio token para el comerciante, enviárnoslo dentro del objeto y establecer reglas sobre si desea rechazar pagos con detalles de pago duplicados.

```json
  "createToken": {
    "value": "optional - define your own token",
    "reusable": true | false,
    "declineDuplicates": true | false
   }
```

Una vez que se crea el token y lo ha almacenado en los detalles de la cuenta del cliente, puede usarlo para ejecutar pagos para el cliente. Para utilizar un instrumento de pago tokenizado, utilice los tipos de solicitud PaymentToken* correspondientes. Si proporciona un valor de token, lo almacenaremos; de lo contrario, generaremos un valor de token y se lo devolveremos en la respuesta.

Cuando un cliente está pagando y usted ha tokenizado previamente sus detalles de pago (y ha almacenado el token con el registro de la cuenta del cliente en sus sistemas), puede solicitar los detalles del token para permitir que el cliente confirme que desea pagar con el instrumento de pago almacenado. Para hacer esto, use el punto final GET, proporcionando el ```tokenid``` para recibir una respuesta de PaymentTokenDetails. Nuestra sugerencia es que use el valor ```last4``` y ```brand``` para permitir que el cliente identifique correctamente su instrumento de pago.

A continuación, puede utilizar el token de pago en el tipo de solicitud de pago principal correspondiente para ejecutar el pago del cliente. Para actualizar o eliminar tokens de pago de clientes, consulte la sección Token.

### Crear un token de pago

Para tokenizar una tarjeta de pago por separado de un pago, POST la carga útil a continuación a /payment-tokens (se proporciona un ejemplo para la tarjeta, pero use el objeto método de pago relevante para el instrumento de su elección).

```json
{
  "requestType": "PaymentCardPaymentTokenizationRequest",
  "paymentCard": {
    "number": "4012000033330026",
    "expiryDate": {
      "month": "12",
      "year": "24"
    }
  },
  "createToken": {
    "value": "123abc456def"
    "reusable": true,
    "declineDuplicates": false
  },
  "accountVerification": false,
  "additionalDetails": {
    "operatorId": "OPERATOR_ID_123XXX",
    "salesSystemId": "W-EU-H3866-FLS2"
  }
}
```

Si desea establecer su propio valor para el token, incluya el atributo ```value``` en el objeto createToken. Si no incluye esto, definiremos el valor del token y lo devolveremos en la respuesta como atributo ```value``` en el objeto paymentToken. El atributo ```reuseable``` es booleano. Si se establece en verdadero, el token se puede reutilizar. Si es falso, tiene un solo uso. El atributo ```declineDuplicates``` también es booleano. Si proporcionó su propio valor de token para el instrumento de pago y se envía un pago con la misma tarjeta de pago, pero sin token, el sistema rechazará el pago (este es un control de fraude que puede configurar a nivel de token).

Utilice el siguiente generador de muestra para ver las cargas útiles y las respuestas:

```json
{
  "method": "post",
  "url": "https://prod.api.firstdata.com/ipp/payments-gateway/v2/payment-tokens",
  "headers": {
    "Content-Type": "application/json",
    "Client-Request-Id": "",
    "Api-Key": "",
    "Timestamp": "",
    "Message-Signature": ""
  }
  "body" {
    "requestType": "PaymentCardPaymentTokenizationRequest",
    "paymentCard": {
      "number": "4012000033330026",
      "expiryDate": {
        "month": "12",
        "year": "24"
      }
    },
    "createToken": {
      "value": "123abc456def"
      "reusable": true,
      "declineDuplicates": false
    },
    "accountVerification": false,
    "additionalDetails": {
      "operatorId": "OPERATOR_ID_123XXX",
      "salesSystemId": "W-EU-H3866-FLS2"
    }
  }
} 
```

## Actualización de tokens de pago

Puede actualizar uno o más tokens de pago a la vez y cambiar la configuración asociada con el token o la tarjeta de pago asociada con el token. Para realizar estas actualizaciones, haga un PARCHE a /pago-tokens usando requestType ```PaymentCardPaymentTokenUpdateRequest```.

El objeto ```PaymentTokens``` es una lista y puede incluir varios tokens de pago. Consulte el siguiente ejemplo para ver cómo construir la carga útil. Cada uno de los objetos de token a continuación incluye las actualizaciones requeridas; estas se escribirán automáticamente en el registro de token en nuestros sistemas si la solicitud se procesa correctamente.

```json
{
  "requestType": "PaymentCardPaymentTokenUpdateRequest",
  "paymentTokens": [
    {
      "value": "1751905117310026",
      "reusable": true,
      "declineDuplicates": false,
      "paymentCard": {
        "number": "5424180279791732",
        "expiryDate": {
          "month": "03",
          "year": "21"
        },
        "securityCode": "977"
      }
    },
    {
      "value": "9877hkhk68688888ffgh",
      "reusable": true,
      "declineDuplicates": false,
      "paymentCard": {
        "number": "4773410012347324",
        "expiryDate": {
          "month": "12",
          "year": "26"
        },
        "securityCode": "123"
      }
    }
  ]
}
```

Si una de las actualizaciones falla, esto se especificará en la respuesta; consulte a continuación un ejemplo de una respuesta a la anterior en la que falló la segunda actualización.

```json
{
  "requestStatus": "PARTIAL_SUCCESS",
  "requestTime": "1554308829345",
  "errors": {
    "details": [
      {
        "message": "HOSTED_DATA_ID4773410012347324. Invalid credit card number: CreditCard [cardNumber=4773410...7324, expirationMonth=12, expirationYear=2026"
      },
    ]
  }
}
```

## Otras funciones de token de pago

Un token de pago se puede eliminar enviando un DELETE a /payment-tokens/{token-id}.

Puede recuperar los datos de la tarjeta de pago asociados con un token y la configuración del token enviando un GET a /payment-tokens/{token-id}.

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
- [Códigos de Error](?path=docs/español/pagos/3-13-codigos-error.md)

---
