# Tokenisation

## Tokens

Tokens are useful when storing the details of a customer’s payment instruments to secure and speed up checkout later. When you collect a customer's payment methods, you can request a token, which you can then use in place of a customer’s card details to execute future payments. You will need to store the token on your platform, mapped to the customer’s account details.

Tokens are useful as they reduce the risk of attacks on your and our payments systems being successful. Tokenisation also reduces the PCI Requirement for you, as it means you don't need to store card details within your systems, which should reduce your costs associated with PCI Compliance.

To generate a Token for future use at the same time as submitting a payment, use the createToken object to set a Token up for multiple use (set reusable to True). In addition, you can create your own token for the merchant, send it to us within the object, and set rules as to whether you want to decline payments with duplicate payment details.

```json
  "createToken": {
    "value": "optional - define your own token",
    "reusable": true | false,
    "declineDuplicates": true | false
   }
```

Once the token is created and you have stored it against the customer’s account detail, you can use it to execute payments for the customer. To use a tokenised payment instrument, use the relevant PaymentToken* requestTypes. If you supply a token value, we will store that, otherwise we'll generate a token value and pass it back to you in the response.

When a customer is checking out, and you've previously tokenised their payment details (and you’ve stored the token with the customer’s account record in your systems), you can request token details to enable the customer to confirm they want to pay with the stored payment instrument. To do this, use the GET endpoint, providing the ```tokenid``` to receive a PaymentTokenDetails response. Our suggestion is you use the ```last4``` value and ```brand``` to enable the customer to correctly identify their payment instrument.

You can then use the Payment Token in the relevant Primary Payment request type to execute the customer payment. To Update or Delete customer payment tokens, see the Token section.

### Create a Payment Token

To tokenise a payment card separately to a payment, POST the payload below to /payment-tokens (example provided for card, but use the relevant paymentMethod object for the instrument of your choice).

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

If you want to set your own value for the token, include the ```value``` attribute in the createToken object. If you don't include this, we'll define the token value and return it in the response as ```value``` attribute in the paymentToken object. The ```reuseable``` attribute is boolean. If set to true, the token can be reused. If false, it has only a single use. The ```declineDuplicates``` attribute is also boolean. If you've provided your own token value for payment instrument, and a payment is submitted with the same payment card, but untokenised, the system will decline the payment (this is a fraud control you can set at token level).

Use the following sample generator to see payloads and responses:

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

## Updating Payment Tokens
You can update one or more Payment Tokens at a time, and change the settings associated with the Token, or the payment card associated with the Token. To make these updates, make a PATCH to /payment-tokens using requestType ```PaymentCardPaymentTokenUpdateRequest```.

The ```PaymentTokens``` object is a list, and can include multiple payment tokens. See the example below to see how to construct the payload. Each of the token objects below includes the updates required - these will automatically be written to the token record on our systems if the request is processed successfully.

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

If one of the updates fails, this will be specified in the response - see below for an example of a response to the above in which the second update failed.

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

## Other Payment Token Functions
A payment token can be deleted by sending a DELETE to /payment-tokens/{token-id}.

You can retrieve the payment card data associated with a token, and the token settings, by sending a GET to /payment-tokens/{token-id}.
