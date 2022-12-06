# 3DSecure Flow

3D Secure is the new form of payment developed by Visa and Mastercard that makes possible to perform secure purchases on the Internet and authenticates the buyer as the legitimate holder of the card they are using.

From now on you can only buy if you go through an authentication flow, which will authorize the purchase at that precise moment. If this flow is not completed, the customer will not be able to complete the purchase with the card.

In this way fraud in the network is impossible and guarantees total security in transactions.

**MOST of the transactions that we carry out by default need to go through the 3DSecure flow in our stores:** direct sales, msi sales, sales with token, scheduled recurrences, pre-auth, **with the sole exception of MIT recurring transactions** (Merchant Initiated Transactions), **token generation, paymentURL generation, and postauth.**

In other words, all transactions with the following requestTypes go through 3DS.

- PaymentCardPreAuthTransaction (preauthorization with card)
- PaymentCardSaleTransaction (sales with card)
- PaymentTokenPreAuthTransaction (preauthorization with token)
- PaymentTokenSaleTransaction (sales with token)

For this reason it is important that we understand the flow of transactions with 3DSecure, below is the diagram of a 3DS V2 authentication flow, for more information see the description of each of the processes according to the number below.
for this reason it is important that we understand the flow of transactions with 3DSecure, below is the diagram of a 3DS V2 authentication flow, for more information see the description of each of the processes according to the number below.

IMAGEN AQUI

## 1

Primary Transaction refers to the initial transaction where within the payload we can find the type of operation to be carried out (direct sale, msi sale, etc.) and the cardholder's information. This initial request must always include the **authenticationRequest** object and must contain the following parameters:

|**Parameters**|**Description**|
|----------|-----------|
|```authenticationType```|Protocolo solicitado para la autenticación. Deberá ser establecido con el valor Secure3DAuthenticationRequest|
|```termURL```|Es la url de callback en donde se reciben los resultados del proceso de autenticación desde el servidor ACS (Access Control Server) quien se encarga de ejecutar la autenticación del tarjetahabiente.|
|```methodNotificationURL```|Se utiliza para recibir una notificación cuando el formulario 3DSMethod sea completado, por medio de un POST http a una url definida. La URL deberá ser única y capaz de identificar la transacción a la que pertenece. Adicionalmente es posible enviar la referencia de la transacción dentro de la URL como un parámetro GET (queryString). Ejemplo: <https://www.mywebshop.com/process3dSecureMethod?reference=12345612352/>|
|```challengeIndicator```|En caso de tener alguna preferencia sobre el flujo de autenticación a seguir, puedes anexar este parámetro opcional con alguno de los valores enlistados a continuación. En caso de no enviarlo, el valor predeterminado será seteado en “01” – No preference.“01” = No preference (No tienes preferencia sobre el flujo)“02” = No challenge requested (Prefieres no solicitar challenge)“03” = Challenge requested: 3DS Requestor Preference (Prefieres que se solicite challenge) “04” = Challenge requested: Mandate (Existen algunas regiones en donde es obligatorio el uso de challenge)|
|```challengeWindowSize```|Puedes anexar este parametro si deseas establecer el tamaño de la ventana de autenticación mostrada al tarjetahabiente durante la autenticación. Los valores posibles a enviar son. 01 = 250 x 400 02 = 390 x 400 03 = 500 x 600 04 = 600 x 400 05 = Full screen|

It is recommended provide billing and shipping information within the transaction to reduce the risk of authentication rejections. To do this, make sure to provide the data within your sales request.

The following JSON represents a sales transaction with the minimum requirements:

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

## 2

If the response from the Gateway contains the '3DSMethod' element, this element should be embed within your site as a hidden iframe; No graphical interface is presented and its only functionality is to collect user information, helping to identify potential fraudulent transactions.

## 3

Here is a JSON response in this format:

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

## 4

Not all issuers support data collection using the 3DSMethod form. If no response iframe is returned within 10 seconds, in these scenarios no information will be returned to methodNotificationURL and the flow should continue to send status.
**EXPECTED_BUT_NOT_RECEIVED**

This is done by sending a PATCH request with the following information: **method HTTP PATCH** point to the URL <https://cert.api.firstdata.com/gateway/v2/payments/{ipgTransactionId}/>. Where the ipgTransactionId will be obtained from the previous response.

```json
{
  "authenticationType": "Secure3D21AuthenticationUpdateRequest",
  "storeId": "12345500000",
  "methodNotificationStatus": "EXPECTED_BUT_NOT_RECEIVED",
  "securityCode": "XXX"
}
```

NOTE: The **storeId** field is not mandatory.

## 5

**RECEIVED** = If you received the notification within the first 10 seconds to the url defined in your **methodNotificationURL**.
This is done by sending a PATCH request with the following information: **method HTTP PATCH** point to the URL <https://cert.api.firstdata.com/gateway/v2/payments/{ipgTransactionId}/>. Where the ipgTransactionId will be obtained from the previous response.

```json
{
  "authenticationType": "Secure3D21AuthenticationUpdateRequest",
  "storeId": "12345500000",
  "methodNotificationStatus": "RECEIVED",
  "securityCode": "XXX"
}
```

NOTE: The **storeId** field is not mandatory.

## 6

Once this flow has been completed, the system will determine by which type of 3DS flow the transaction can be taken. When a transaction is considered low risk, the **Frictionless** flow is applied. In this case, the Gateway will proceed to authorize the transaction without any additional input from the cardholder.

## 7

When the API call is completed and the system detects that it is a flow with Challenge, the transaction is not authorized immediately. Instead, you will get the **WAITING** status and the parameters to redirect the cardholder to the issuer's Directory Server in order to authenticate the operation:

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

The **authenticationResponse** field will contain the following information:

|**Parameter**|**Description**|
|----------|-----------|
|```type```|3D_SECURE|
|```version``` |2.1.0 (3DS VERSION)|
|```acsURL|URL to issuer site to redirect cardholder and post cReq and sessionData|
|```termURL```|URL of the merchant where the authentication results will be received|
|```cReq```|Encrypted message returned from the ACS|
|```sessionData```|Session parameters used for authentication. This field is not always returned|

You should implement a form that is automatically submitted within your website.

```xml
<form name="frm" method="POST" action="{value obtained on acsURL}">
  <input type="hidden" name="creq" value="ewogICAiYWNzVHJhbCIgOi...wMDAtMDAwMDAwMDA0MWE5Igp9">
  <input type="hidden" name="threeDSSessionData" value="50F2156E03083CA665BCB4..">
</form>
```

## 8

Once the POST is sent, the buyer will be redirected to the 3DS authentication screen where they will be asked to enter their token/key (this key depends on the issuing bank and the mechanism used by the bank).

![Token request!](/assets/images/3-5-esp-3ds-screen.jpg "Token request")

## 9

When the correct key is entered, the buyer will be redirected to the URL we received in **"termURL"** along with the following key-values as **POST** parameters.

|**Key**|**Value**|
|-------|---------|
|```cRes```|	String containing encrypted information, result of authentication|

## 10

Complete the transaction

Within your termURL you will need to build a PATCH request to confirm the authentication results to the Gateway. The components to send within the request are the following: **method HTTP PATCH** point to the URL <https://cert.api.firstdata.com/gateway/v2/payments/{ipgTransactionId}/>. Where the ipgTransactionId will be obtained from the previous response.

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
  "acsResponse": {
    "cRes": "ewogICAiYWNzUmVmZX…Fuc1N0YXR…IKfQ=="
  }
}
```

The response to this request must contain the final result of the authentication:

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

## 11

We correctly mapped our response according to the Response Management section of this manual.
