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

### Paso 1: Iniciar un pago

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

- 01 = Sin preferencia (No tiene preferencia sobre si se debe realizar un desafío. Este es el valor predeterminado)
- 02 = No se solicitó ningún desafío (Prefiere que no se realice ningún desafío).
- 03 = Desafío solicitado: Preferencia del Solicitante 3DS (Prefiere que se realice un desafío)
- 04 = Desafío solicitado: Mandato (Existen mandatos locales o regionales que significan que se debe realizar un desafío)
- 05 = No se solicitó desafío (Análisis de riesgo de transacción ya se realizó)
- 06 = No se solicitó desafío (Solo uso compartido de datos)
- 07 = No se solicitó desafío (SCA ya se realizó)
- 08 = No se solicitó desafío (utilice la exención de la lista blanca si no se requiere desafío)
- 09 = Desafío solicitado (solicitud de lista blanca solicitada si se requiere desafío)

Los valores disponibles para ```challengeWindowSize``` son:

- 01 = 250 x 400
- 02 = 390 x 400
- 03 = 500 x 600
- 04 = 600 x 400
- 05 = Full screen

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

El siguiente documento JSON representa un ejemplo de una respuesta:

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

### Paso 3: 3DSMethod Solicitud de notificación y respuesta

El 'methodForm' de 3-D Secure se utiliza para proporcionar detalles del entorno del titular de la tarjeta al servidor de control de acceso del emisor (ACS). El ```methodForm``` contiene el HTML para un iFrame oculto que se incluirá en su página web. Esto obligará a que la información se publique automáticamente en el servidor ACS a través de Fiserv. La información HTML es un bloque HTML autónomo que no necesita modificarse ni publicarse, ya que se cuidará automáticamente cuando se represente la página en la que se inserta. Alternativamente, esto se puede crear en una página que nunca sea visible para el titular de la tarjeta.

Si se reciben correctamente, los datos de respuesta se publicarán en la URL proporcionada en el campo ```methodNotificationURL``` original y el mensaje publicado contendrá un campo ```threeDSServerTransID``` que contiene el ID de transacción ACS único asociado con el original solicitud. Tenga en cuenta que la carga útil para esta respuesta contendrá un solo elemento llamado ```tresDSMethodData```. Ese elemento contendrá una respuesta JSON codificada en base64 que contiene el campo ```threeDSServerTransID```.

Ejemplo:

```xml
<form name="frm" method="POST" action="{value from methodNotificationURL}">
  <input type="hidden" name="threeDSMethodData" value="eyJ0aHJlZURTU2VydmVyVHJhbnNJRCI6IjNhYzdjYWE3LWFhNDItMjY2My03OTFiLTJhYzA1YTU0MmM0YSJ9">
</form>
```

Descifrado threeDSMethodData:

```json
{"threeDSServerTransID":"3ac7caa7-aa42-2663-791b-2ac05a542c4a"}
```

El ```threeDSServerTransID``` no es necesario para ningún otro procesamiento de 3DS. Sin embargo, se recomienda guardar este valor como referencia para el servidor 3DS en el futuro si es necesario.

Debe esperar un mínimo de ** 10 segundos ** para que se complete la operación POST anterior y luego determinar el estado de notificación del método de la siguiente manera:

| Estatus                   | Descripción                                                                                                                                                                                                                                                                                      |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RECEIVED                  | Ha enviado el elemento ```methodNotificationURL``` en la solicitud de transacción de Venta inicial y ha recibido la notificación de ACS en 10 segundos, recibirá un mensaje HTTP POST de ACS, que contendrá un identificador de transacción único representado por secure3dTransId. |
| EXPECTED_BUT_NOT_RECEIVED | Ha enviado el elemento ```methodNotificationURL``` en la solicitud de transacción de Venta inicial y no ha recibido la notificación de ACS en 10 segundos.                                                                                                                          |
| NOT_EXPECTED              | NO ha enviado el elemento ```methodNotificationURL``` en la solicitud de transacción de venta inicial.                                                                                                                                                                              |


> 🚧 Puede haber ocasiones en las que observará respuestas duplicadas a su 'URL de notificación de 3DSMethod' o 'URL de término', esto podría deberse a solicitudes duplicadas enviadas desde el ACS de un emisor o quizás al comportamiento del usuario dentro del navegador. Se recomienda que incorpore el manejo en su sistema, de modo que, en caso de que reciba una respuesta duplicada a su 'URL de notificación de 3DSMethod' o 'URL de término', no envíe una solicitud adicional/duplicada a la puerta de enlace.<!-- theme: warning -->

## Flujo sin fricción

### Paso 4: Solicitud para continuar con la autenticación 3DS

Una vez que se haya completado la llamada al Método 3DS, debe notificar a la puerta de enlace que el proceso de autenticación puede continuar enviando el elemento 'methodNotificationStatus' con los valores basados en las condiciones correspondientes del formulario 3DSMethod anterior. Esto se hace realizando una operación **PATCH** en la transacción original.

También puede incluir la dirección de facturación del titular de la tarjeta opcional y el código de seguridad en este momento.

El siguiente documento JSON representa un ejemplo de una solicitud que se enviará después de mostrar el formulario ```3DSMethod```:

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

### Paso 5: Respuesta final de 3DS

Cuando se determina que se ha realizado un flujo sin fricciones (es decir, el cliente ha sido completamente autenticado por su banco sin necesidad de interacción directa), se completa el proceso de 3-D Secure y se procesa la autorización de la transacción.

La respuesta de la transacción contiene un objeto secure3dResponse y la transacción se aprueba o se rechaza.

```transactionStatus = APPROVED or DECLINED```

El objeto 'secure3dResponse' contendrá el siguiente campo: 'responseCode3dSecure'

El siguiente documento JSON representa un ejemplo de una respuesta que recibe de la API que indica que la autorización se ha realizado correctamente:

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

## Flujo de desafío

El flujo de desafío se activa cuando la transacción no se considera de bajo riesgo o cuando el Emisor requiere una autenticación adicional por parte del titular de la tarjeta. Todo el proceso comienza con una solicitud inicial de transacción de Autorización o Venta a través del paso donde se muestra 3DSMethod, como se describe en los Pasos 1 a 4 anteriores.

### Paso 6: Solicitud para continuar con la autenticación 3DS

Una vez que se haya completado la llamada al método 3DS, debe notificar a la puerta de enlace que el proceso de autenticación puede continuar enviando el elemento 'methodNotificationStatus' con los valores basados en las condiciones correspondientes del formulario del método 3DS anterior. Esto se hace realizando una operación **PATCH** en la transacción original.

También puede incluir la dirección de facturación del titular de la tarjeta opcional y el código de seguridad en este momento.

El siguiente documento JSON representa un ejemplo de una solicitud que se enviará después de la visualización del formulario 3DSMethod:

```json
{
  "authenticationType": "Secure3D21AuthenticationUpdateRequest",
  "storeId": "12345500000",
  "methodNotificationStatus": "RECEIVED"
}
```

### Paso 7: Gateway respond to continue 3DS Authentication

Para el flujo de desafío, el estado de la transacción se devolverá de la siguiente manera:

```transactionStatus = "WAITING"```

La respuesta contendrá un objeto ```authenticationResponse``` con los siguientes campos:

| **Campo**         | Descripción                                                                                                                                                   |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```type```        | 3D_SECURE                                                                                                                                                     |
| ```version```     | 2.1 o 2.2                                                                                                                                                     |
| ```acsURL```      | La URL en la que se deben publicar los valores 'cReq' y 'session Data' para que se lleve a cabo el desafío del titular de la tarjeta.                         |
| ```termURL```     | La URL donde se publicarán los resultados de la autenticación.                                                                                                |
| ```cReq```        | Un mensaje de solicitud de desafío codificado devuelto desde el servidor ACS.                                                                                 |
| ```sessionData``` | Una lista codificada de parámetros de sesión que se utilizará para la autenticación. Tenga en cuenta que es posible que no siempre se proporcione este valor. |

El siguiente documento JSON representa un ejemplo de una respuesta:

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

### Paso 8: Desafío del titular de la tarjeta

En el siguiente paso, debe enviar los datos al ```acsURL``` indicado, que generalmente se implementa como un formulario de envío automático. Esto debe implementarse en su sitio web. El titular de la tarjeta será redirigido al ACS y se le presentará la interfaz de usuario para recopilar los detalles de autenticación, por ejemplo, ingresar una contraseña de un solo uso o realizar la autenticación utilizando su aplicación bancaria. Una vez completada la autenticación, se redirige al consumidor a su página web.

Debe publicar los valores ```cReq``` y ```sessionData``` en la URL especificada en el campo ```acsURL```.

Esta información se publica utilizando los siguientes nombres de campo:

| **```cReq```**               | Todo el mensaje cReq codificado en base64 como se obtuvo anteriormente.        |
|------------------------------|--------------------------------------------------------------------------------|
| **```threeDSSessionData```** | Todo el mensaje sessionData codificado en base64 como se obtuvo anteriormente. |

Ejemplo:

```xml
<form name="frm" method="POST" action="https://3ds-acs.test.modirum.com/mdpayacs/pareq ">
  <input type=”hidden” name=”creq” value=”ewogICAiYWNzVHJhbCIgOiA...wMDAtMDAwMDAwMDA0MWE5Igp9”>
  <input type=”hidden” name=”threeDSSessionData” value=”50F2156E03083CA665BCB4..”>
</form>
```

Cuando se complete la autenticación, se publicará una respuesta de autenticación en la URL especificada en el campo 'termURL'.

### Paso 9: Solicitud para completar la transacción

Después de recibir los datos del ACS, debe enviarlos al Gateway en el elemento ```cRes``` junto con la referencia a la transacción original. Esto se hace enviando una solicitud **PATCH** a la transacción original e incluye los siguientes valores:

| **```authenticationType```** | **Secure3D21AuthenticationUpdateRequest**                            |
|------------------------------|----------------------------------------------------------------------|
| **```acsResponse/cRes```**   | Los datos ```cRes``` publicados en ```termURL``` por el servidor ACS.|

Se recomienda incluir la dirección de facturación opcional del titular de la tarjeta y el código de seguridad en este momento.

El siguiente documento JSON representa un ejemplo de una solicitud con el elemento 'cRes':

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

### Paso 10: Última respuesta

Dado que esta transacción se inició como una 'Sale', la autorización se realiza como parte de este paso final, si la autenticación fue exitosa.

La respuesta de la transacción contiene un objeto ```secure3dResponse``` y la transacción se aprueba o rechaza.

El objeto ```secure3dResponse``` contendrá el siguiente campo: ```responseCode3dSecure```

El siguiente documento JSON representa un ejemplo de una respuesta que recibe que indica que la autorización se ha realizado correctamente:

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

## Retroceder a 3DS 1.0

Para los casos en los que los emisores aún no son compatibles con el protocolo EMV 3DS, la puerta de enlace ofrece una opción de "retroceso" a la autenticación 3DS 1.0 en su lugar.

En el primer paso, debe **POST** una solicitud de API como para el protocolo EMV 3DS:

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

Recibirá una respuesta del Gateway que indica que la tarjeta de crédito está registrada solo para la versión 1 de 3DS:

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

En el siguiente paso, debe **POST** los datos a la URL indicada que generalmente se implementa como un formulario de envío automático. Esto debe implementarse en su sitio web. Le recomendamos que PUBLIQUE el elemento de solicitud de desafío sin mayúsculas (por ejemplo, 'pareq') para evitar problemas en una comunicación con el ACS.

Ejemplo:

```xml
<form name="frm" method="POST" action="https://3ds-acs.test.com/mdpayacs/pareq ">
  <input type=”hidden” name=”pareq” value=”ewogICAiYWNzVHJhbCIgOiA...wMDAtMDAwMDAwMDA0MWE5Igp9”>
  <input type=”hidden” name=”threeDSSessionData” value=”50F2156E03083CA665BCB4..”>
</form>
```

Después de recibir los datos del ACS, debe enviarlos al Gateway en el elemento ```payerAuthenticationResponse``` junto con la referencia a la transacción original. Esto se hace enviando una solicitud **PATCH** a la transacción original.

*Nota: Es posible que no todos los emisores devuelvan el elemento ```merchantData```, en caso de que no lo haya recibido en el paso anterior, no lo envíe en la próxima solicitud de PATCH.*

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

El siguiente documento JSON representa un ejemplo de una respuesta que recibe del Gateway que indica que la autorización se realizó correctamente y se marcó como autenticada:

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

## Autenticación con proveedor 3DS externo

En caso de que esté utilizando su propio proveedor de servicios 3DS o externo y planee enviar una solicitud de autorización al Gateway, debe enviar los valores de autenticación obtenidos de su proveedor de servicios 3DS.

| **Field**                    | **Descripción**                                                                                                                                                                       |
|------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```authenticationType```     | Used for submitting authentication result performed by an external 3-D Secure service provider                                                                                        |
| ```cavv	```                | Authentication value obtained in the authentication response from external 3-D Secure service provider                                                                                |
| ```dsTransactionId```        | Authentication transaction reference ID, obtained from external 3-D Secure provider                                                                                                   |
| ```authenticationResponse``` | Represents the result of the authentication, allowed values are : Y = fully authenticated transaction, A = Successful Authentication Attempt, U = Unable to Authenticate by DS or ACS |

Solo los siguientes resultados de autenticación son elegibles para pasar al host de autorización:

| **Caso de uso**                                       | **authenticationResponse** | **cavv**                     | **responseCode3dSecure** |
|-------------------------------------------------------|----------------------------|------------------------------|--------------------------|
| Transacción completamente autenticada (ECI = 02 & 05) | Y                          | valor                        | 1                        |
| Intento de autenticación exitoso (ECI = 01 & 06)      | A                          | valor                        | 4                        |
| No se puede autenticar en el lado DS o ACS (ECI07)    | U                          | el campo no debe ser enviado | 6                        |

El siguiente documento JSON representa un ejemplo de una transacción de venta enviada a nuestro Gateway después de haber sido completamente autenticada por un proveedor de servicios externo:

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

El siguiente documento JSON representa un ejemplo de una respuesta que recibe del Gateway que indica que la autorización se realizó correctamente y se marcó como totalmente autenticada:

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
