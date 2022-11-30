# Generate a message signature


We show you how to generate the signature required for use with our payments API.

---
- [Step 1: Import required libraries](#step-1-import-required-libraries)
- [Step 2: Add your API key & secret key](#step-2-add-api-key-and-secret-key)
- [Step 3: Generate a unique ID for each request](#step-3-genetate-unique-id)
- [Step 4: Generate the unix time](#step-4-generate-unix-time)
- [Step 5: Combine all of these ingredients into a string](#step-5-combine-into-string)
- [Step 6: Hash using SHA256](#step-6-hash-using-sha256)
- [Step 7: Set up your headers](#step-7-set-up-headers)
- [Step 8: Make the request](#step-8-make-request)

---

## Step 1: Import required libraries

Generating the message signature requires that we hash the payload of the API call.
Firstly, import the required libraries.
```phyton
import hmac
import hashlib
import base64
import requests
import time
import uuid

key = 'Insert your API key here'
secret = 'Insert your API secret here'
content = 'Insert your request content here'
url = 'Insert your target url here'

clientRequestId = str(uuid.uuid4())

timestamp = str(int(time.time()))

message = '{}{}{}{}'.format(apiKey, clientRequestId, timestamp, str(content))

signature = hmac.new(apiSecret.encode(), message.encode(), hashlib.sha256).digest()
b64_sig = base64.b64encode(signature).decode()

headers = {
    'Api-Key': apiKey,
    'Timestamp': timestamp,
    'Client-Request-Id': clientRequestId,
    'Message-Signature': b64_sig
}
print(headers)

r = requests.post(url, content, headers=headers)
print(r.content)
```

## Step 2: Add your API key & secret key

Insert your API key and secret here

```json
{
  "amount": {
    "total": "12.04",
    "currency": "USD"
  },
  "paymentSource": {
    "sourceType": "PaymentCard",
    "card": {
      "cardData": "4005550000000019",
      "expirationMonth": "02",
      "expirationYear": "2035",
      "securityCode": "123"
    }
  },
  "transactionDetails": {
    "captureFlag": true
  },
  "merchantDetails":{
      "merchantId": "123456789789567",
      "terminalId": "123456"
    }
}
```

## Step 3: Process Payment

The benefits of a encyrpted PIN Pad solution are:
- Reduced coding effort for the developer because the encryption handling is already implemented by the third party vendor
- All forms of electronic payment are accepted
- Business security by enabling acceptance of chip and signature, and chip and PIN
<!-- theme: info -->
> Commerce Hub highly recommends testing against our sandbox and end to end environments before using our production environment.

## Step 4: Finalize Transaction

The benefits of a encyrpted PIN Pad solution are:
- Reduced coding effort for the developer because the encryption handling is already implemented by the third party vendor
- All forms of electronic payment are accepted
- Faster payment improving the customer experience
<!--
type: tab
titles: Request, Response
-->

##### Example of a charge payload request using `dynamicDescriptors`.

```json
 {
   "amount":{
      "total": "12.04",
      "currency": "USD"
   },
   "source":{
      "sourceType": "PaymentCard",
      "card":{
         "cardData": "4005550000000019",
         "expirationMonth": "02",
         "expirationYear": "2035"
      }
   },
   "dynamicDescriptors":{
      "mcc": "4457",
      "merchantName": "Mywebsite.com",
      "customerServiceNumber": "1231231234",
      "serviceEntitlement": "67893827513",
      "address":{
         "street": "123 Main Street",
         "houseNumberOrName": "Unit B",
         "city": "Atlanta",
         "stateOrProvince": "GA",
         "postalCode": "30303",
         "country": "US"
      }
   },
   "transactionDetails":{
      "captureFlag": true
   },
   "merchantDetails":{
      "merchantId": "123456789789567",
      "terminalId": "123456"
   }
}
```

<!--
type: tab
-->

##### Example of a charge (201: Created) response.

<!-- theme: info -->
> See [Response Handling](?path=docs/Resources/Guides/Response-Codes/Response-Handling.md) for more information.

```json
{
   "gatewayResponse":{
      "orderId": "R-3b83fca8-2f9c-4364-86ae-12c91f1fcf16",
      "transactionType": "CHARGE",
      "transactionState": "AUTHORIZED",
      "transactionOrigin": "ECOM",
      "transactionProcessingDetails":{
         "transactionTimestamp": "2016-04-16T16:06:05Z",
         "apiTraceId": "rrt-0bd552c12342d3448-b-ea-1142-12938318-7",
         "clientRequestId": "30dd879c-ee2f-11db-8314-0800200c9a66",
         "transactionId": "838916029301"
      }
   },
   "source":{
      "sourceType": "PaymentCard",
      "card":{
         "cardData": "4005550000000019",
         "nameOnCard": "Jane Smith",
         "expirationMonth": "02",
         "expirationYear": "2035",
         "bin": "400555",
         "last4": "0019"
      }
   },
   "paymentReceipt":{
      "approvedAmount":{
         "total": 12.04,
         "currency": "USD"
      },
      "processorResponseDetails":{
         "approvalStatus": "APPROVED",
         "approvalCode": "OK3483",
         "authenticationResponseCode": "string",
         "referenceNumber": "845366457890-TODO",
         "schemeTransactionId": "019078743804756",
         "feeProgramIndicator": "123",
         "processor": "FISERV",
         "host": "NASHVILLE",
         "responseCode": "000",
         "responseMessage": "APPROVAL",
         "hostResponseCode": "00",
         "hostResponseMessage": "APPROVAL",
         "localTimestamp": "2016-04-16T16:06:05Z",
         "bankAssociationDetails":{
            "associationResponseCode": "000",
            "transactionTimestamp": "2016-04-16T16:06:05Z"
         }
      }
   },
   "dynamicDescriptors":{
      "mcc": "4457",
      "merchantName": "Mywebsite.com",
      "customerServiceNumber": "1231231234",
      "serviceEntitlement": "67893827513",
      "address":{
         "street": "123 Main Street",
         "houseNumberOrName": "Unit B",
         "city": "Atlanta",
         "stateOrProvince": "GA",
         "postalCode": "30303",
         "country": "US"
      }
   }
}
```

<!-- type: tab-end -->


---
## See also

- [API Explorer](./api/?type=post&path=/payments/v1/charges)
- [Charge Request](?path=docs/Resources/API-Documents/Payments/Charges.md)
- [EMV Chip and PIN](?path=docs/In-Person/Encrypted-Payments/EMV.md)
- [Track Data](?path=docs/In-Person/Encrypted-Payments/Track.md)
- [NFC/Contactless](?path=docs/In-Person/Encrypted-Payments/Contactless.md)
- [Manual Entry](?path=docs/In-Person/Encrypted-Payments/Manual.md)


---
