---
tags: [Getting Started, Payments, 3-D Secure, Frictionless Flow, Challenge Flow]
---

# 3-D Secure

Al utilizar nuestro Gateway y Fiserv como proveedor de 3-D Secure, la autenticación se realiza en línea con el flujo de transacciones existente. El proceso comienza realizando una autorización típica o una solicitud de venta con el deseo de realizar una autenticación 3-D Secure en la solicitud.

Luego, la autorización se coloca en un estado ```WAITING``` hasta que se complete el proceso de autenticación. Durante la autenticación, se le puede solicitar al comerciante que actualice la solicitud de transacción original una o más veces para avanzar en el flujo del proceso.

Al final del proceso de autenticación, la transacción original se actualiza con los resultados de la autenticación y se completa la autorización.

Los diagramas de secuencia a continuación corresponden a los pasos del texto que sigue. El primer diagrama es para el flujo sin fricción. Esto significa que el emisor no requiere que el titular de la tarjeta se autentique.

![Sequence diagrama for frictionless flow!](/assets/images/3-5-1-3ds-frictionless.png "Frictionless flow")

El siguiente diagrama muestra el flujo cuando su cliente tiene que autenticarse, lo que significa que su emisor ha solicitado que proporcione detalles de autenticación adicionales.

![Sequence diagrama for authenticate!](/assets/images/3-5-2-3ds-auth.png "Authenticate flow")

## Cómo implementar 3-D Secure usando nuestra API

### Step 1: Iniciar un pago

Utilice la tarjeta de pago o el token de pago para iniciar una transacción de pago principal.

Puede indicar al pago que utilice 3-D Secure si desea aplicarlo. Los RequestTypes relevantes para la autenticación 3-D Secure son los siguientes:

- PaymentCardPreAuthTransaction
- PaymentCardSaleTransaction
- PaymentTokenPreAuthTransaction
- PaymentTokenSaleTransaction
- PaymentCardPayerAuthTransaction

Este mensaje debe incluir el objeto ```authenticationRequest``` en el mensaje de solicitud de transacción e incluye los siguientes valores:

| Atributo                   | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```authenticationType```   | El valor Secure3DAuthenticationRequest es un valor predeterminado para la solicitud de autenticación 3DS.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ```termURL```              | Indica la URL de devolución de llamada donde el servidor ACS debe publicar los resultados del proceso de autenticación (este es el servidor de control de acceso que ejecuta la autenticación del titular de la tarjeta).                                                                                                                                                                                                                                                                                                                                                                   |
| ```methodNotifictionURL``` | Para recibir una notificación sobre la finalización de la visualización del formulario 3DSMethod, también debe enviar este elemento en su solicitud de transacción. La URL debe ser identificable de forma única, por lo que cuando se recibe una notificación en esta URL, debe poder asignarla a la transacción correspondiente. Esto elimina cualquier dependencia del Secure3dTransId que recibirá con la respuesta del formulario 3DSMethod. Una forma fácil de garantizar la asignación correcta de transacciones es pasar una referencia de transacción como una cadena de consulta. |
| ```challengeIndicator```   | En caso de que desee influir en qué flujo de autenticación debe usarse, puede enviar este elemento opcional con uno de los valores que se enumeran a continuación. En caso de que el Indicador de desafío no se envíe dentro de su solicitud de transacción, la puerta de enlace completará el valor predeterminado "01": sin preferencia.                                                                                                                                                                                                                                                  |
| ```challengeWindowSize```  | Si desea definir el tamaño de la ventana de desafío que se muestra a sus clientes durante el proceso de autenticación, puede enviar este elemento opcional con uno de los valores que se enumeran a continuación.                                                                                                                                                                                                                                                                                                                                                                           |

Los valores disponibles para ```challengeIndicator``` son:
01 = Sin preferencia (No tiene preferencia sobre si se debe realizar un desafío. Este es el valor predeterminado)
02 = No se solicitó ningún desafío (Prefiere que no se realice ningún desafío).
03 = Desafío solicitado: Preferencia del Solicitante 3DS (Prefiere que se realice un desafío)
04 = Desafío solicitado: Mandato (Existen mandatos locales o regionales que significan que se debe realizar un desafío)
05 = No se solicitó desafío (Análisis de riesgo de transacción ya se realizó)
06 = No se solicitó desafío (Solo uso compartido de datos)
07 = No se solicitó desafío (SCA ya se realizó)
08 = No se solicitó desafío (utilice la exención de la lista blanca si no se requiere desafío)
09 = Desafío solicitado (solicitud de lista blanca solicitada si se requiere desafío)

Los valores disponibles para ```challengeWindowSize``` son:

01 = 250 x 400
02 = 390 x 400
03 = 500 x 600
04 = 600 x 400
05 = Full screen

El siguiente documento JSON representa un ejemplo de una solicitud de transacción de venta con un conjunto mínimo de elementos:

```json
{
  "requestType": "PaymentCardSaleTransaction",
    "transactionAmount": {
      "total": "122.04",
      "currency": "USD"
      },
    "paymentMethod": {
      "paymentCard": {
        "number": "403587XXXXXX4977",
        "securityCode": "977",
        "expiryDate": {
        "month": "12",
        "year": "24"
      }   
    }
  },
  "authenticationRequest": {
    "authenticationType": "Secure3D21AuthenticationRequest",
    "termURL": "https://www.mywebshop.com/process3dSecure",
    "methodNotificationURL": "https://www.mywebshop.com/process3dSecureMethodNotification?transactionReferenceNumber=ffffffff-ba0b-539f-8000-016b2343ad7e",
    "challengeIndicator": "01",
    "challengeWindowSize": "01"
  }
}
```

No todos los emisores admiten la recopilación de datos del navegador mediante el formulario 3DSMethod. En esos casos, no se publicarán datos en methodNotificationURL, y el flujo debe continuar publicando un estado de ```EXPECTED_PERO_NO_RECIBIDO```; consulte a continuación.

### Paso 2: Respuesta de autenticación segura 3-D

Nuestra respuesta incluirá un elemento ```3DSMethod```, que genera un iframe oculto que ayuda a recopilar los datos del navegador para los emisores. Esta información se suma al perfil general del consumidor y ayuda a identificar transacciones potencialmente fraudulentas. También aumenta la probabilidad de una transacción exitosa y sin fricciones.

Deberá incluir ```3DSMethod``` en su sitio web como iframe oculto. No se presenta ninguna pantalla de interfaz de usuario al titular de la tarjeta.

En este punto, se realiza una solicitud de verificación para determinar si el sistema 3-D Secure funciona y si el titular de la tarjeta está inscrito en 3-D Secure. Si el sistema 3-D Secure no funciona o si el titular de la tarjeta no está inscrito, la transacción se procesará normalmente y será aprobada o rechazada por la red de procesamiento.

En el caso anterior, el estado de la transacción aparecerá así:

```transactionStatus = `APPROVED` || `DECLINED` ```

Si se verifica que el titular de la tarjeta está inscrito en el programa 3-D Secure, se incluirá un objeto 'authenticationResponse' en la respuesta de la transacción.

Mientras espera la respuesta, la transacción tendrá el siguiente estado de transacción:

```transactionStatus = WAITING```

El objeto ```authenticationResponse``` contendrá los siguientes valores:

| Atributo                             | Valor                                                                                                           |
|--------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| ```type```                           | 3D_SECURE                                                                                                       |
| ```version```                        | 2.1 o 2.2                                                                                                       |
| ```secure3DMethod/methodForm```      | Datos de formulario HTML con iFrame oculto utilizado para recopilar los datos del navegador web para el Emisor. |
| ```secure3DMethod/secure3dTransId``` | Un identificador único para la transacción proporcionado por el servidor ACS del emisor.                        |

The following JSON document represents an example of a response:

```json
{
  "clientRequestId": "30dd879c-ee2f-11db-8314-0800200c9a66",
  "apiTraceId": "rrt-0c80a3403e2c2def0-d-ea-28805-6810951-2",
  "ipgTransactionId": "838916029301",
  "transactionType": "SALE",
  "transactionTime": 1518811817,
  "approvedAmount": {
  "total": 122.04,
  "currency": "USD"
},
  "transactionStatus": "WAITING",
  "authenticationResponse": {
    "type": "3D_SECURE",
    "version": "2.1",
    "secure3dMethod": {
      "methodForm": "&lt;!DOCTYPE iframe SYSTEM "about:legacy-compat"&gt;
      &lt;iframe id="tdsMmethodTgtFrame" name="tdsMmethodTgtFrame"
      style="width: 1px; height: 1px; display: none;" src="javascript:false;"
      xmlns="http://www.w3.org/1999/xhtml"&gt;
      &lt;!--.--&gt; &lt;/iframe&gt;&lt;form id="tdsMmethodForm"
      name="tdsMmethodForm"
      action=https://localhost.modirum.com:8543/dstests/ACSEmu2
      method="post"
      target="tdsMmethodTgtFrame" xmlns="http://www.w3.org/1999/xhtml"&gt;
      &lt;input type="hidden" name="3DSMethodData"
      value="eyAidGhyZWVEU1NlcnZlclRyYW5zSUQiIDogIjAwMDAwMDAwLTU2NzYtNTY2My
      04MDAwLTAwMDAw    &amp;#10;MDAwNDFhOSIsICJ0aHJlZURTTWV0aG9kTm90aWZpY2F0aW9
      uVVJMIiA6ICJodHRwczovL2xvY2Fs&amp;#10;aG9zdC5tb2RpcnVtLmNvbTo4NTQzL21kcGF5bXBpL
      01lcmNoYW50U2VydmVyP21uPVkmdHhpZD0x
      &amp;#10;NjgwOSZkaWdlc3Q9aSUyQnhhUEF5NWFOcVJRbllqNmozbWFDZlFJbTdFdjJYTm
      kwNnh6YmZNJTJG&amp;#10;R3MlM0QiIH0"/&gt; &lt;input type="hidden"
      name="threeDSMethodData"            
      value="eyAidGhyZWVEU1NlcnZlclRyYW5zSUQiIDogIjAwMDAwMDAwLTU2NzYtNTY2
      My04MDAwLTAwMDA
      w&amp;#10;MDAwNDFhOSIsICJ0aHJlZURTTWV0aG9kTm90aWZpY2F0aW9uVVJMIiA
      6ICJodHRwczovL2xvY 2Fs&amp;#10;aG9zdC5tb2RpcnVtLmNvbTo4NTQzL21kcGF5bXBpL01lcm
      NoYW50U2VydmVyP21uPVkmdHhpZD0x&amp;#10;NjgwOSZkaWdlc3Q9aSUyQnhhUEF5NWFOcV
      JRbllqNmozbWFDZlFJbTdFdjJYTmkwNnh6YmZNJTJG&amp;#10;R3MlM0QiIH0"/&gt;
      &lt;/form&gt;&lt;script type="text/javascript" 
      xmlns="http://www.w3.org/1999/xhtml"&gt;
      document.getElementById("tdsMmethodForm").submit(); &lt;/script&gt;",
      "secure3dTransId": "3ac7caa7-aa42-2663-791b-2ac05a542c4a"
    }
  }
}
```

### Step 3: 3DSMethod Notification Request & Response

The 3-D Secure 'methodForm' is used to provide details of the cardholder environment to the Issuer Access Control Server (ACS). The ```methodForm``` contains the HTML for a hidden iFrame which is to be included in your web page. This will force the information to be automatically posted to the ACS server via Fiserv. The HTML information is a self-contained HTML block that does not need to be modified or posted, as it will be taken care of automatically when the page in which it is inserted is rendered. Alternatively, this can be created on a page which never becomes visible to the cardholder.

If received properly, the response data will be posted to the URL provided in the original ```methodNotificationURL``` field and the posted message will contain a ```threeDSServerTransID``` field containing the unique ACS transaction id associated with the original request. Note, that the payload for this response will contain a single element called ```threeDSMethodData```. That element will contain a base64 encoded JSON response that contains the ```threeDSServerTransID``` field.

Example:

```xml
<form name="frm" method="POST" action="{value from methodNotificationURL}">
  <input type="hidden" name="threeDSMethodData" value="eyJ0aHJlZURTU2VydmVyVHJhbnNJRCI6IjNhYzdjYWE3LWFhNDItMjY2My03OTFiLTJhYzA1YTU0MmM0YSJ9">
</form>
```

Decoded threeDSMethodData:

```json
{"threeDSServerTransID":"3ac7caa7-aa42-2663-791b-2ac05a542c4a"}
```

The ```threeDSServerTransID``` is not required for any further 3DS processing. However, it is recommended to save this value for reference to the 3DS Server in the future if necessary.

You need to wait a minimum of **10 seconds** for the above POST operation to complete and then determine the methodNotificationStatus as follows:

| Status                    | Description                                                                                                                                                                                                                                                                                      |
|---------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RECEIVED                  | You have submitted the element ```methodNotificationURL``` in the initial Sale transaction request and have received the notification from ACS within 10 seconds, you will receive HTTP POST message from ACS, which will contain a unique transaction identifier represented by secure3dTransId |
| EXPECTED_BUT_NOT_RECEIVED | You have submitted the element ```methodNotificationURL``` in the initial Sale transaction request and have not received the notification from ACS within 10 seconds.                                                                                                                            |
| NOT_EXPECTED              | You have NOT submitted the element ```methodNotificationURL``` in the initial Sale transaction request.                                                                                                                                                                                          |

> 🚧 There may be occasions where you will observe duplicate responses to your '3DSMethod Notification URL' or 'Term URL', this could be due to duplicate requests being sent from an issuers ACS or perhaps user behaviour within the browser. It is recommended that you build handling into your system, so in the event you receive a duplicate response to your '3DSMethod Notification URL' or 'Term URL' you do not then send an additional/duplicated request to the Gateway.
<!-- theme: warning -->

## Frictionless Flow

### Step 4: Request to continue with 3DS Authentication

Once the 3DS Method call has been completed, you need to notify the Gateway, that the authentication process can continue by submitting the 'methodNotificationStatus' element with the values based on corresponding conditions from the 3DSMethod form above. This is done by performing a **PATCH** operation on the original transaction.

You may also include the optional cardholder billing address and the security code at this time.

The following JSON document represents an example of a request to be sent after ```3DSMethod``` form display:

```json
{
  "authenticationType": "Secure3D21AuthenticationUpdateRequest",
  "storeId": "12345500000",
  "billingAddress": {
  "company": "Test Company",
  "address1": "5565 Glenridge Conn",
  "address2": "Suite 123",
  "city": "Atlanta",
  "region": "Georgia",
  "postalCode": "30342",
  "country": "USA"
},
  "securityCode": "123",
  "methodNotificationStatus": "RECEIVED"
}
```

### Step 5: Final 3DS Response

When it is determined that a frictionless flow has been performed (i.e. the customer has been fully authenticated by their bank without the need for direct interaction), the 3-D Secure process is completed and transaction authorization is processed.

The transaction response contains a secure3dResponse object and the transaction is either approved or declined.

```transactionStatus = APPROVED or DECLINED```

The 'secure3dResponse' object will contain the following field: 'responseCode3dSecure'

The following JSON document represents an example of a response you receive from the API indicating, that the authorisation has been successful:

```json
{
  "clientRequestId": "30dd879c-ee2f-11db-8314-0800200c9a66",
  "apiTraceId": "rrt-0c80a3403e2c2def0-d-ea-28805-6810951-2",
  "ipgTransactionId": "838916029301",
  "transactionType": "SALE",
  "transactionTime": 1518811817,
  "approvedAmount": {
    "total": 122.04,
    "currency": "USD"
  },
  "transactionStatus": "APPROVED",
  "schemeTransactionId": "019078743804756",
  "processor": {
    "responseCode": "00",
    "responseMessage": "APPROVED",
    "authorizationCode": "OK7118"
  },
  "secure3dResponse": {
    "responseCode3dSecure": "1"
  }
}
```

## Challenge Flow

The challenge flow is triggered, when the transaction is not considered as low risk or when the Issuer requires additional authentication by the cardholder. The whole process starts with an initial Authorisation or Sale transaction request through the step where 3DSMethod is displayed, as described in Steps 1 through 4 above.

### Step 6: Request to continue with 3DS Authentication

Once the 3DS Method call has been completed, you need to notify the Gateway that the authentication process can continue by submitting the 'methodNotificationStatus' element with the values based on corresponding conditions from the 3DS Method Form above. This is done by performing a **PATCH** operation on the original transaction.

You may also include the optional cardholder billing address and the security code at this time.

The following JSON document represents an example of a request to be sent after 3DSMethod form display:

```json
{
  "authenticationType": "Secure3D21AuthenticationUpdateRequest",
  "storeId": "12345500000",
  "methodNotificationStatus": "RECEIVED"
}
```

### Step 7: Gateway respond to continue 3DS Authentication

For the challenge flow, the transaction status will be returned as follows:

```transactionStatus = "WAITING"```

The response will contain an ```authenticationResponse``` object with the following fields:

| **Field**         | Description                                                                                                           |
|-------------------|-----------------------------------------------------------------------------------------------------------------------|
| ```type```        | 3D_SECURE                                                                                                             |
| ```version```     | 2.1 or 2.2                                                                                                            |
| ```acsURL```      | The URL to which the 'cReq and 'sessionData' values should be posted for the cardholder challenge to take place.      |
| ```termURL```     | The URL where the results of the authentication will be posted.                                                       |
| ```cReq```        | An encoded challenge request message returned from the ACS server.                                                    |
| ```sessionData``` | An encoded list of session parameters to be used for authentication. Note that this value may not always be provided. |

The following JSON document represents an example of a response:

```json
{
  "clientRequestId": "30dd879c-ee2f-11db-8314-0800200c9a66",
  "apiTraceId": "rrt-0c80a3403e2c2def0-d-ea-28805-6810951-2",
  "ipgTransactionId": "838916029301",
  "transactionType": "SALE",
  "transactionTime": 1518811817,
  "approvedAmount": {
    "total": 122.04,
    "currency": "USD"
  },
  "transactionStatus": "WAITING",
  "authenticationResponse": {
    "type": "3D_SECURE",
    "version": "2.1",
    "params": {
      "acsURL": "https://3ds-acs.test.modirum.com/mdpayacs/pareq",
      "termURL": "https://www.mywebshop.com/process3dSecure/",
      "cReq": "ewogICAiYWNzVHJhbCIgOiA...wMDAtMDAwMDAwMDA0MWE5Igp9",
      "sessiondata": "50F2156E03083CA665BCB4.."
    }
  }
}
```

### Step 8: Cardholder Challenge

In the next step you need to POST data to the indicated ```acsURL``` usually implemented as an auto-submit form. This needs to be implemented within your website. The cardholder will be redirected to the ACS and presented with the UI to collect the authentication details - for example enter one-time-password or perform authentication using their banking app. After the authentication is completed, the consumer is redirected back to your webpage.

You need to POST the ```cReq``` and the ```sessionData``` values to the URL specified in the ```acsURL``` field.

This information is posted using the following field names:

| **```cReq```**               | The entire base64 encoded cReq message as obtained above.        |
|------------------------------|------------------------------------------------------------------|
| **```threeDSSessionData```** | The entire base64 encoded sessionData message as obtained above. |

Example:

```xml
<form name="frm" method="POST" action="https://3ds-acs.test.modirum.com/mdpayacs/pareq ">
  <input type=”hidden” name=”creq” value=”ewogICAiYWNzVHJhbCIgOiA...wMDAtMDAwMDAwMDA0MWE5Igp9”>
  <input type=”hidden” name=”threeDSSessionData” value=”50F2156E03083CA665BCB4..”>
</form>
```

When the authentication is completed, an authentication response will be posted to the URL specified in the 'termURL' field.

### Step 9: Request to complete transaction

After you received the data from the ACS, you need to submit them to the Gateway in ```cRes``` element together with the reference to the original transaction. This is done by sending **PATCH** request to the original transaction and includes the following values:

| **```authenticationType```** | **Secure3D21AuthenticationUpdateRequest**                          |
|------------------------------|--------------------------------------------------------------------|
| **```acsResponse/cRes```**   | The ```cRes``` data posted to the ```termURL``` by the ACS server. |

It is highly recommended to include the optional cardholder billing address and the security code at this time.

The following JSON document represents an example of a request with 'cRes' element:

```json
{
  "authenticationType": "Secure3D21AuthenticationUpdateRequest",
  "storeId": "12345500000",
  "billingAddress": {
    "company": "Test Company",
    "address1": "5565 Glenridge Conn",
    "address2": "Suite 123"
    "city": "Atlanta",
    "region": "Georgia",
    "postalCode": "30342",
    "country": "USA"
  },
  "securityCode": "123",
  "acsResponse": {
    "cRes": "ewogICAiYWNzUmVmZX…Fuc1N0YXR…IKfQ=="
  }
}
```

### Step 10: Final response

Since this transaction was initiated as a 'Sale', the authorization is performed as part of this final step, if the authentication was successful.

The transaction response contains a ```secure3dResponse``` object and the transaction is either approved or declined.

The ```secure3dResponse``` object will contain the following field: ```responseCode3dSecure```

The following JSON document represents an example of a response you receive indicating that the authorization has been successful:

```json
{
  "clientRequestId": "30dd879c-ee2f-11db-8314-0800200c9a66",
  "apiTraceId": "rrt-0c80a3403e2c2def0-d-ea-28805-6810951-2",
  "ipgTransactionId": "838916029301",
  "transactionType": "SALE",
  "transactionTime": 1518811817,
  "approvedAmount": {
    "total": 122.04,
    "currency": "USD"
  },
  "transactionStatus": "APPROVED",
  "schemeTransactionId": "019078743804756",
  "processor": {
    "responseCode": "00",
    "responseMessage": "APPROVED",
    "authorizationCode": "OK7118"
  },
  "secure3dResponse": {
    "responseCode3dSecure": "1"
  }
}
```

## Fallback to 3DS 1.0

For cases, where the issuers do not support EMV 3DS protocol yet, the Gateway provides an option to "fallback" to 3DS 1.0 authentication instead.

In the first step you need to **POST** an API request as for EMV 3DS protocol:

```json
{
  "requestType": "PaymentCardSaleTransaction",
    "transactionAmount": {
      "total": "12.99",
      "currency": "EUR"
      },
    "paymentMethod": {
      "paymentCard": {
        "number": "403587XXXXXX4977",
        "securityCode": "999",
        "expiryDate": {
        "month": "12",
        "year": "24"
      }  
    }
  },
  "authenticationRequest": {
    "authenticationType": "Secure3D21AuthenticationRequest",
    "termURL": "https://test.ipg-online.com/webshop/simulator/secure3d/return",
    "methodNotificationURL": "https://test.ipg-online.com/ipgconfirmation/services/secure3ds",
    "challengeIndicator": "01",
    "challengeWindowSize": "01"
  }
}
```

You will receive a response from the Gateway indicating, that credit card is enrolled for 3DS version 1 only:

```json
{
   "clientRequestId": "55351544-749a-4c03-9e35-3e240ea5572d",
   "apiTraceId": "YKUh6bqtrBfPwvICng2wBgAAATM",
   "ipgTransactionId": "84563547902",
   "orderId": "R-ff489e2d-b5f3-4757-a91b-0a774681e26a",
   "transactionType": "SALE",
   "transactionOrigin": "ECOM",
   "paymentMethodDetails":    {
      "paymentCard":       {
         "expiryDate":          {
            "month": "12",
            "year": "2024"
         },
         "bin": "403587",
         "last4": "4977",
         "brand": "VISA"
      },
      "paymentMethodType": "PAYMENT_CARD"
   },
   "country": "Germany",
   "transactionTime": 1621434858,
   "transactionStatus": "WAITING",
   "authenticationResponse":    {
      "type": "3D_SECURE",
      "version": "1.0",
      "params":       {
         "payerAuthenticationRequest": "eJxVUdtygjAQ/RXG184QEvASZ80M1V54aAcRP4BCWqgSNAlW/r4JeGn3ac/Jnt3NWUhLyflqw/NWcgZvXKnsiztVsRjFyXo88Yg3xSMGcZjwI4MTl6pqBMOu5xJAV2h0Mi8zoRlk+fExemfjPgBdINRcRitGqWfCBzRAEFnNWRS/OClX2vFXG0A9BXnTCi07NiMTQFcArdyzUuuDmiOkjcL1C+V+VlLpItOZmzc1IFsD6L5P3NpMmZ7nqmBxkW4flj80weK7DE9h130872b7VES7BSBbAaYVZ8Qj2Btj6uBg7gdzPAPU85DVdhn2tE0cTFxKzQ8HBg52UDgATOzLXwaMv5KLvGN0aprdEPDzoRHcVBg7bzmg+9rLV2tqro1f2Pc96uMAW197xsorYw0J8KC3AJDVoMvJ0OWmJvt361+B66YO",
         "termURL": "https://test.ipg-online.com/webshop/simulator/secure3d/return",
         "merchantData": "",
         "acsURL": "https://3ds-acs.test.modirum.com/mdpayacs/pareq"
      }
   }
}
```

In the next step you need to **POST** the data to the indicated URL usually implemented as auto-submit form. This needs to be implemented within your website. We recommend you to POST the challenge request element without capitals (e.g. ‘pareq’) to avoid any problems in a communication with the ACS.

Example:

```xml
<form name="frm" method="POST" action="https://3ds-acs.test.com/mdpayacs/pareq ">
  <input type=”hidden” name=”pareq” value=”ewogICAiYWNzVHJhbCIgOiA...wMDAtMDAwMDAwMDA0MWE5Igp9”>
  <input type=”hidden” name=”threeDSSessionData” value=”50F2156E03083CA665BCB4..”>
</form>
```

After you received the data from the ACS, you need to submit them to the Gateway in ```payerAuthenticationResponse``` element together with the reference to the original transaction. This is done by sending **PATCH** request to the original transaction.

*Note: The element ```merchantData``` might not be returned by all issuers, in case you have not received it in the previous step, please do not submit in the next PATCH request.*

### PATCH /ipgrestapi/v2/services/payments/{ipgTransactionId}

```json
{
  "authenticationType": "Secure3D10AuthenticationUpdateRequest",
  "billingAddress": {
    "address1": "5565 Glenridge Conn",
    "city": "Atlanta",
    "postalCode": 30342,
    "country": "USA"
  },
  "merchantData": "MD____13992017033012241629.....c-4a40-aeb4-b41a8a34480fa067ac",
  "payerAuthenticationResponse": "eJzlWFeP67iS/………9Xm88X5c/37Pcj6I/v3P8JV86aGw=="
}
```

The following JSON document represents an example of a response you receive from the Gateway indicating, that the authorization has been successful and flagged as authenticated:

```json
{
   "clientRequestId": "4c3aa885-db8e-43fb-b3c4-0e5c6927408c",
   "apiTraceId": "YKUjRJVBbdinNESL8Era8AAAAIs",
   "ipgTransactionId": "84563547902",
   "orderId": "R-ff489e2d-b5f3-4757-a91b-0a774681e26a",
   "transactionType": "SALE",
   "transactionOrigin": "ECOM",
   "paymentMethodDetails":    {
      "paymentCard":       {
         "expiryDate":          {
            "month": "12",
            "year": "2024"
         },
         "bin": "403587",
         "last4": "4977",
         "brand": "VISA"
      },
      "paymentMethodType": "PAYMENT_CARD"
   },
   "country": "Germany",
   "terminalId": "80000860",
   "merchantId": "000102072004393",
   "transactionTime": 1621434858,
   "approvedAmount":    {
      "total": 12.99,
      "currency": "EUR",
      "components": {"subtotal": 12.99}
   },
   "transactionStatus": "APPROVED",
   "secure3dResponse": {"responseCode3dSecure": "1"},
   "schemeTransactionId": "519154004377788",
   "processor":    {
      "referenceNumber": "113914232351",
      "authorizationCode": "907587",
      "responseCode": "00",
      "responseMessage": "Function performed error-free",
      "avsResponse":       {
         "streetMatch": "N",
         "postalCodeMatch": "N"
      }
   }
}
```

## Authentication with external 3DS provider

In case you are using your own / external 3DS service provider and plan to send authorization request to the Gateway, you need to submit the authentication values obtained from your 3DS service provider.

| **Field**                    | **Description**                                                                                                                                                                       |
|------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```authenticationType```     | Used for submitting authentication result performed by an external 3-D Secure service provider                                                                                        |
| ```cavv	```                   | Authentication value obtained in the authentication response from external 3-D Secure service provider                                                                                |
| ```dsTransactionId```        | Authentication transaction reference ID, obtained from external 3-D Secure provider                                                                                                   |
| ```authenticationResponse	``` | Represents the result of the authentication, allowed values are : Y = fully authenticated transaction, A = Successful Authentication Attempt, U = Unable to Authenticate by DS or ACS |

Only the following authentication results are eligible to be passed to the authorization host:

| **Use case**                                      | **authenticationResponse** | **cavv**                    | **responseCode3dSecure** |
|---------------------------------------------------|----------------------------|-----------------------------|--------------------------|
| Fully Authenticated transaction (ECI = 02 & 05)   | Y                          | value                       | 1                        |
| Successful Authentication Attempt (ECI = 01 & 06) | A                          | value                       | 4                        |
| Unable to authenticate on DS or ACS side (ECI07)  | U                          | field must not be submitted | 6                        |

The following JSON document represents an example of a sale transaction submitted to our Gateway after being fully authenticated by an external service provider:

```json
{
  "requestType": "PaymentCardSaleTransaction",
  "transactionAmount": {
    "total": "12.00",
    "currency": "EUR"
  },
  "paymentMethod": {
    "paymentCard": {
      "number": "401699XXXX0006",
      "securityCode": "999",
      "expiryDate": {
        "month": "12",
        "year": "24"
      }
    }
  },
  "authenticationResult": {
    "authenticationType": "Secure3DAuthenticationResult",
    "cavv": "AAAAAAAAAAAAAAAAAAAAAAAAAAA=",
    "dsTransactionId": "5a56fdc9-6d47-5fee-8000-000000296743",
    "authenticationResponse": "Y"
  }
}
```

The following JSON document represents an example of a response you receive from the Gateway indicating, that the authorization has been successful and flagged as fully authenticated:

```json
{
   "clientRequestId": "97c67e8f-7c2d-421d-9d97-b749206aab06",
   "apiTraceId": "YJPLezoO2XZa9K8QL10bvgAAA98",
   "ipgTransactionId": "84411977859",
   "orderId": "R-941fc643-adae-4468-bc48-26e5099f4367",
   "transactionType": "SALE",
   "transactionOrigin": "ECOM",
   "paymentMethodDetails":    {
      "paymentCard":       {
         "expiryDate":          {
            "month": "12",
            "year": "2024"
         },
         "bin": "401699",
         "last4": "0006",
         "brand": "VISA"
      },
      "paymentMethodType": "PAYMENT_CARD"
   },
   "country": "USA",
   "terminalId": "80000012",
   "merchantId": "520334507229862",
   "transactionTime": 1620298619,
   "approvedAmount":    {
      "total": 12,
      "currency": "EUR",
      "components": {"subtotal": 12}
   },
   "transactionStatus": "APPROVED",
   "secure3dResponse": {"responseCode3dSecure": "1"},
   "schemeTransactionId": "234567891234560",
   "processor":    {
      "referenceNumber": "112610940537",
      "authorizationCode": "005042",
      "responseCode": "00",
      "responseMessage": "Function performed error-free",
      "avsResponse":       {
         "streetMatch": "Y",
         "postalCodeMatch": "Y"
      },
      "securityCodeResponse": "MATCHED"
   }
}
```

---

## Ver más

- [Tipos de Solicitud](?path=docs/español/pagos/3-1-tipos-solicitudes.md)
- [Métodos de Pagos](?path=docs/español/pagos/3-2-metodos-pago.md)
- [Formas de Pagos](?path=docs/español/pagos/3-3-formas-pagos.md)
- [Pos-autorización & Devoluciones](?path=docs/español/pagos/3-4-post-aut.md)
- [Verificación de Tarjeta](?path=docs/español/pagos/3-6-verificacion-tarjeta.md)
- [Conversion de Moneda](?path=docs/español/pagos/3-7-conversion-moneda.md)
- [Redirección Administrada](?path=docs/español/pagos/3-8-redireccion-administrada.md)
- [Pedidos](?path=docs/español/pagos/3-9-pedidos.md)
- [URL Pago](?path=docs/español/pagos/3-10-pago-url.md)
- [Pagos Recurrentes](?path=docs/español/pagos/3-11-pagos-recurrentes.md)
- [Tokenización](?path=docs/español/pagos/3-12-tokenizacion.md)
- [Códigos de Error](?path=docs/español/pagos/3-13-codigos-error.md)

---
