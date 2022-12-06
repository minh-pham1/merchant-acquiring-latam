# Payments

Use this API to accept Payments of all types, and to process secondary actions like Voids, Refunds or Pre-Auth Completions

You can use our payment API to offer a range of payment methods to your customers. It allows you to take payment using whatever the most appropriate transaction type is.

Fiserv’s Payment API supports the following major payment methods; ```Visa```, ```Mastercard```, ```Amex```, ```Apple Pay```, ```Google Pay```, ```Sepa```, as well as multiple other international and local payment methods. To gain access to payment methods beyond this main set, please contact your integration services representative.

Our payments end points can be used to create and complete a payment, create and manage a recurring payment schedule, generate a payment link to send to a customer, verify a customer’s card or offer them the option to pay in their local currency.

Once you’ve executed a transaction using these APIs, you can retrieve all the detail you need on the transaction using our Merchant Intelligence APIs.

## Header and Parameters

Our Payments API have a consistent Header structure based on a set of Parameters. To create the header, provide the following values:

|Header Parameter|Type|	Description|
| ---------- | ------- | ------- |
|Content-Type|String|	Define this attribute as application/json|
|Client-Request-Id|String|This is an ID you generate so we can mutually track your requests. It should be unique per request. We recommend this is built in 128-bit UUID format.|
|Api-Key|String|This is the key you'll use to identify yourself to us. You'll be given a key for development and testing, then a different key when you're ready to go live.|
|Timestamp|Integer|You should define this as an epoch timestamp in milliseconds. This is used for message signature generation and for time limit control.
|Message-Signature|String|We use this for security. The Message-Signature is the Base64 encoded HMAC hash (SHA256 algorithm with the API Secret as the key.) For more information, refer to the supporting documentation on the Developer Portal.|

The header is constructed as per the following example.

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

Generating a message signature is a key component of this API. You can learn more here:

[Generate a message signature](/recipes/Recipe-1)

## Polymorphism

Our Payments API is Polymorphic - this means that you can submit various request types with different payloads to the same API resource and generate different payments types and responses.

The term is taken from chemistry - Polymorphism is the ability of solid materials to exist in two or more crystalline forms with different arrangements.

When we apply this to API design, it means the same API can be used for multiple payment actions. We enable this by defining different schemas for the payload body, each of which is used for a payment action.

Polymorphism for our Payments API is based on request types (the ```requestType``` field), which enables you to distinguish between customer payments based on the type of transaction (```sale```, ```refund```, ```cancellation```, etc.) and the payment method the customer wants to use (credit or debit cart, digital wallet, SEPA, Paypal etc.).

You'll use the same endpoint to execute a payment for all of these variations, but the ```requestType``` value you submit, and the other objects in the payload, will vary dependent on the type of customer payment you are trying to take.

As an example, you'll use the ```PaymentCardSaleTransaction``` requestType to take a normal card payment when a customer wants to check out. You can then use a secondary transaction requestType` to refund or void the transaction.

## Example

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

**These examples are show below:**

**Make a card payment**
**Open Recipe**
**Refund a transaction**
**Open Recipe**

## How the resource works

The ```/payments``` endpoint allows you to ```create```, ```inquire``` about, and ```finalize``` payment transactions.

It will enable you to ```void``` a previous transaction, to ```refund``` a previous transaction or to execute ```partial refunds``` or ```voids```. Finally, and where necessary, it will enable you to ```pre-authorise``` transactions that can be completed later.

The API enables you to make payments using different payment instruments, via credit or debit cards, tokens or through local payments methods such as SEPA DD or Paypal account. All of these methods are explained below.

You can use the API to accept wallet transactions via payment methods such as Apple Pay or Google Pay. It will allow you to use extra authentication protocols to ensure customer payments are safer, and to protect you from fraud.

Our Payments API has two main uses - Primary and Secondary Transactions. Primary transactions are typical sale transactions, pre-authorisations or credits. Secondary Transactions let you refund a transaction, void a transaction or complete a pre-authorisation.

## Creating a 'primary' transaction

Primary transactions are used to execute a customer payment, pre-authorisation or credit transaction without reference to a prior transaction. For this walkthrough, we’re going to use the PaymentCardSaleTransaction request type (all request types should have links), which you can use for a normal credit or debit card payment transaction without reference to a previous transaction. Each of the different request types, the scenarios in which you might want to use them, and the differences in the JSON for each request type, are explained within this guide.

The ```PaymentCardSaleTransaction``` request type requires the following fields to post a Primary Transaction.

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

Within the model, the most important objects are the ```transactionAmount``` and the ```paymentMethod```. Without these objects, we can't process the payment.

We also recommend you include billing information and shipping information as this allows the 3DSecure fingerprint check to run in the background (see the [Implementing 3DSecure](docs/3-5-3d-secure.md) page for more information). This makes the checkout process frictionless for your customer and allows our fraud systems to protect you more easily.


Both the ```billing``` and ```shipping``` objects follow the same structure:

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

The flow below shows a standard payments process using a card payment transaction.
![standard payments process!](/assets/images/3-payment-process.png "Standard payment process")


**Verify a card payment**
**Open Recipe**

**Look up card information**
**Open Recipe**

