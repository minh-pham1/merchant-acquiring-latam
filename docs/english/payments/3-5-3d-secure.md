---
tags: [Getting Started, Payments, 3-D Secure, Frictionless Flow, Challenge Flow]
---

# 3-D Secure

When using our Gateway and Fiserv as the 3-D Secure provider, the authentication is performed in-line with the existing transaction flow. The process starts by performing a typical authorization or sale request with a desire to perform 3-D Secure authentication in the request.

The authorization is then placed into a ```WAITING``` status until the authentication process is completed. During authentication, the merchant may be required to update the original transaction request one or more times in order to move the process flow forward.

At the end of the authentication process, the original transaction is updated with the authentication results and the authorization is completed.

The sequence diagrams below map to the steps in the text that follows. The first diagram is for the frictionless flow. This means the issuer does not require the cardholder to authenticate.

![Sequence diagrama for frictionless flow!](/assets/images/3-5-1-3ds-frictionless.png "Frictionless flow")

The next diagram shows the flow when your customer has to authenticate, which means their issuer has requested they provide additional authentication details.

![Sequence diagrama for authenticate!](/assets/images/3-5-2-3ds-auth.png "Authenticate flow")

## How to implement 3-D Secure using our API

### Step 1: Initiate a payment

Use either the payment card or the payment token to initiate a Primary Payment Transaction.

You can instruct the payment to use 3-D Secure if you want to enforce it. The relevant RequestTypes for 3-D Secure authentication are as follows:

- PaymentCardPreAuthTransaction
- PaymentCardSaleTransaction
- PaymentTokenPreAuthTransaction
- PaymentTokenSaleTransaction
- PaymentCardPayerAuthTransaction

This message needs to include the ```authenticationRequest``` object in the transaction request message and includes the following values:

| Attribute                  | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ```authenticationType```   | The value Secure3DAuthenticationRequest is a default value for 3DS authentication request                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ```termURL```              | Indicates the callback URL where the results of the authentication process should be posted by the ACS server (this is the Access Control Server that executes the cardholder authentication).                                                                                                                                                                                                                                                                                                                            |
| ```methodNotifictionURL``` | In order to be notified about the 3DSMethod form display completion, you must also submit this element in your transaction request. The URL should be uniquely identifiable, so when there is a notification received on this URL, you should be able to map it with the corresponding transaction. This eliminates any dependency on the Secure3dTransId which you will receive with the 3DSMethod form response. An easy way to ensure correct transaction mapping is to pass a transaction reference as a query string |
| ```challengeIndicator```   | In case you would like to influence which authentication flow should be used, you can submit this optional element with one of the values listed below. In case the Challenge Indicator is not sent within your transaction request, the Gateway will populate the default value “01” – No preference.                                                                                                                                                                                                                    |
| ```challengeWindowSize```  | If you want to define the size of the challenge window displayed to your customers during the authentication process, you can submit this optional element with one of the values listed below.                                                                                                                                                                                                                                                                                                                           |

Available values for ```challengeIndicator``` are:
01 = No preference (You have no preference whether a challenge should be performed. This is the default value)
02 = No challenge requested (You prefer that no challenge should be performed.)
03 = Challenge requested: 3DS Requestor Preference (You prefer that a challenge should be performed)
04 = Challenge requested: Mandate (There are local or regional mandates that mean that a challenge must be performed)
05 = No challenge requested (Transaction Risk Analysis is already performed)
06 = No challenge requested (Data Share Only)
07 = No challenge requested (SCA is already performed)
08 = No challenge requested (Utilize whitelist exemption if no challenge required)
09 = Challenge requested (Whitelist prompt requested if challenge required)

Available values for ```challengeWindowSize``` are:

01 = 250 x 400
02 = 390 x 400
03 = 500 x 600
04 = 600 x 400
05 = Full screen

The following JSON document represents an example of a Sale transaction request with minimal set of elements:

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

Not all Issuers support the collection of browser data using the 3DSMethod Form. In those cases, no data will be posted to the methodNotificationURL, and the flow should continue by posting a status of ```EXPECTED_BUT_NOT_RECEIVED``` – see below.

### Step 2: 3-D Secure Authentication Response

Our response will include a ```3DSMethod``` element, which generates a hidden iframe that helps to collect the browser data for the issuers. This information adds to the overall consumer profile and helps in identifying potentially fraudulent transactions. It also increases the probability of a frictionless, successful transaction.

You will need to include the ```3DSMethod``` in your website as hidden iframe. No user interface screen is presented to the cardholder.

At this point, a verification request takes place to determine if the 3-D Secure system is functional and the cardholder is enrolled for 3-D Secure. If the 3-D Secure system is not functioning or if the cardholder is not enrolled, the transaction will process normally and be approved or declined by the processing network.

In the above case the transaction status will appear like so:

```transactionStatus = `APPROVED` || `DECLINED` ```

If the cardholder is verified to be enrolled in the 3-D Secure program, then an 'authenticationResponse' object will be included in the transaction response.

While awaiting the response the transaction will have the following transaction status:

```transactionStatus = WAITING```

The ```authenticationResponse``` object will contain the following values:

| Attribute                            | Value                                                                                  |
|--------------------------------------|----------------------------------------------------------------------------------------|
| ```type```                           | 3D_SECURE                                                                              |
| ```version```                        | 2.1 or 2.2                                                                             |
| ```secure3DMethod/methodForm```      | HTML form data with hidden iFrame used to collect the web browser data for the Issuer. |
| ```secure3DMethod/secure3dTransId``` | A unique identifier for the transaction provided by the Issuer ACS server.             |

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

## See Also

- [Request Types](?path=docs/english/payments/3-1-request-types.md)
- [Payment Methods](?path=docs/english/payments/3-2-payment-methods.md)
- [Payment Types](?path=docs/english/payments/3-3-payment-types.md)
- [Post-authorisation & Returns](?path=docs/english/payments/3-4-post-auth.md)
- [Card Verification](?path=docs/english/payments/3-6-card-verification.md)
- [Currency Conversion](?path=docs/english/payments/3-7-currency-conversion.md)
- [Managed Redirect](?path=docs/english/payments/3-8-managed-redirect.md)
- [Orders](?path=docs/english/payments/3-9-orders.md)
- [Payment URL](?path=docs/english/payments/3-10-payment-url.md)
- [Recurring Payments](?path=docs/english/payments/3-11-recurring-payments.md)
- [Tokenisation](?path=docs/english/payments/3-12-tokenisation.md)
- [Error codes](?path=docs/english/payments/3-13-error-codes.md)

---
