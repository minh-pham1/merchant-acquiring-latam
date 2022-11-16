# 3DSecure Flow

3D Secure es la nueva forma de pago desarrollada por Visa y Mastercard que posibilita la realización de compras seguras en Internet y autentifica al comprador como legítimo titular de la tarjeta que está utilizando.

A partir de ahora solo podrás comprar si se pasa por un flujo de autenticación, el cual autorizará la compra en ese preciso momento. Si no es completado este flujo,  el cliente no podrá terminar la compra con la tarjeta.

De esta manera se hace imposible el fraude en la red y garantiza la total seguridad en las transacciones.

LA MAYORÍA las transacciones que realicemos por default necesitan pasar por el flujo de 3DSecure en nuestras tiendas: ventas directas, ventas a msi, ventas con token, recurrencias calendarizas, pre-auth, con la única excepción de las transacciones recurrentes MIT (Merchant Initiated Transactions), generación de tokens, generación de paymentURL y postauth.

Es decir todas las transacciones con los siguientes requestType, pasan por 3DS.

- PaymentCardPreAuthTransaction (preauthorization with card)
- PaymentCardSaleTransaction (sales with card)
- PaymentTokenPreAuthTransaction (preauthorization with token)
- PaymentTokenSaleTransaction (sales with token)

Por ello es importante que comprendamos el flujo de las transacciones con 3DSecure, a continuación el diagram de un flujo de autenticación 3DS V2, para mayor información ver la descripción de cada uno de los procesos según el número debajo.

IMAGE

1. Primary Transaction se refiere a la transacción inicial donde dentro del payload podremos encontrar el tipo de operativa que se quiere realizar (venta directa, venta msi, etc.) y la información del tarjetahabiente. Esta petición inicial siempre deberá incluir el objeto authenticationRequest y deberá contener los siguientes parámetros:


|**Parameters**|**Description**|
|----------|-----------|
|```authenticationType```|Protocolo solicitado para la autenticación. Deberá ser establecido con el valor Secure3DAuthenticationRequest|
|```termURL```|Es la url de callback en donde se reciben los resultados del proceso de autenticación desde el servidor ACS (Access Control Server) quien se encarga de ejecutar la autenticación del tarjetahabiente.|
|```methodNotificationURL```|Se utiliza para recibir una notificación cuando el formulario 3DSMethod sea completado, por medio de un POST http a una url definida. La URL deberá ser única y capaz de identificar la transacción a la que pertenece. Adicionalmente es posible enviar la referencia de la transacción dentro de la URL como un parámetro GET (queryString). Ejemplo: https://www.mywebshop.com/process3dSecureMethod?reference=12345612352|
|```challengeIndicator```|En caso de tener alguna preferencia sobre el flujo de autenticación a seguir, puedes anexar este parámetro opcional con alguno de los valores enlistados a continuación. En caso de no enviarlo, el valor predeterminado será seteado en “01” – No preference.“01” = No preference (No tienes preferencia sobre el flujo)“02” = No challenge requested (Prefieres no solicitar challenge)“03” = Challenge requested: 3DS Requestor Preference (Prefieres que se solicite challenge) “04” = Challenge requested: Mandate (Existen algunas regiones en donde es obligatorio el uso de challenge)|
|```challengeWindowSize```|Puedes anexar este parametro si deseas establecer el tamaño de la ventana de autenticación mostrada al tarjetahabiente durante la autenticación. Los valores posibles a enviar son. 01 = 250 x 400 02 = 390 x 400 03 = 500 x 600 04 = 600 x 400 05 = Full screen|

Es recomendable proporcionar los datos de facturación y envío dentro de la transacción para reducir el riesgo de rechazos por autenticación. Para realizar esto, asegurate de proporcionar los datos dentro de tu petición de venta.

El siquiente JSON representa una transacción de venta con los requerimientos mínimos:

```json
{
  "requestType": "PaymentCardSaleTransaction",
  "transactionAmount": {
    "total": "122.04",
    "currency": "MXN"
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
    "authenticationType": "Secure3DAuthenticationRequest",
    "termURL": "https://www.mywebshop.com/process3dSecure",
    "methodNotificationURL": "https://www.mywebshop.com/process3dSecureMethodNotification?transactionReferenceNumber=ffffffff-ba0b-539f-8000-016b2343ad7e",
    "challengeIndicator": "01",
    "challengeWindowSize": "01"
  }
}
```

2. Si la respuesta del Gateway contiene el elemento ‘3DSMethod’ , se deberá incrustar este elemento dentro de tu sitio como un iframe oculto; no se presenta ninguna interfaz gráfica y su unica funcionalidad es recoletar información del usuario, ayudando a identificar potenciales transacciones fraudulentas.

3. A continuación se muestra un JSON de respuesta con este formato:

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
      value="eyAidGhyZWVEU1NlcnZlclRyYW5zSUQiIDogIjAwMDAwMDAwLTU2NzYtNTY2My04MDAwLTAwMDAw    &amp;#10;MDAwNDFhOSIsICJ0aHJlZURTTWV0aG9kTm90aWZpY2F0aW9uVVJMIiA6ICJodHRwczovL2xvY2Fs&amp;#10;aG9zdC5tb2RpcnVtLmNvbTo4NTQzL21kcGF5bXBpL01lcmNoYW50U2VydmVyP21uPVkmdHhpZD0x&amp;#10;NjgwOSZkaWdlc3Q9aSUyQnhhUEF5NWFOcVJRbllqNmozbWFDZlFJbTdFdjJYTmkwNnh6YmZNJTJG&amp;#10;R3MlM0QiIH0"/&gt; &lt;input type="hidden"
      name="threeDSMethodData"            
      value="eyAidGhyZWVEU1NlcnZlclRyYW5zSUQiIDogIjAwMDAwMDAwLTU2NzYtNTY2My04MDAwLTAwMDAw&amp;#10;MDAwNDFhOSIsICJ0aHJlZURTTWV0aG9kTm90aWZpY2F0aW9uVVJMIiA6ICJodHRwczovL2xvY 2Fs&amp;#10;aG9zdC5tb2RpcnVtLmNvbTo4NTQzL21kcGF5bXBpL01lcmNoYW50U2VydmVyP21uPVkmdHhpZD0x&amp;#10;NjgwOSZkaWdlc3Q9aSUyQnhhUEF5NWFOcV JRbllqNmozbWFDZlFJbTdFdjJYTmkwNnh6YmZNJTJG&amp;#10;R3MlM0QiIH0"/&gt;
      &lt;/form&gt;&lt;script type="text/javascript"
      xmlns="http://www.w3.org/1999/xhtml"&gt;
      document.getElementById("tdsMmethodForm").submit(); &lt;/script&gt;",
      "secure3dTransId": "3ac7caa7-aa42-2663-791b-2ac05a542c4a"
    }
  }
}
```

4. No todos los emisores soportan la recolección de datos usando el formulario de 3DSMethod. Si no se obtiene un iframe de respuesta en un lapso de 10 segundos, en estos escenarios no se enviará ninguna información a methodNotificationURL y el flujo deberá continuar enviando el estatus.
EXPECTED_BUT_NOT_RECEIVED 

Esto se realiza enviando una petición PATCH con la siguiente información: method HTTP PATCH apuntar hacia el URL https://cert.api.firstdata.com/gateway/v2/payments/{ipgTransactionId}. Donde el ipgTransactionId lo obtendremos de la respuesta previa.

```json
{
  "authenticationType": "Secure3D21AuthenticationUpdateRequest",
  "storeId": "12345500000",
  "methodNotificationStatus": "EXPECTED_BUT_NOT_RECEIVED",
  "securityCode": "XXX"
}
```

NOTA: El campo storeId no es mandatorio.
