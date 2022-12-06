# Refund a transaction

Process a refund for the full or a partial amount.

---

- [Step 1: Import the message signature function](#step-1-import-message-signature-function)
- [Step 2: Patch the transaction ID from your payment](#step-2-patch-transaction-id-from-payment)
- [Step 3: Add the body ReturnTransaction](#step-3-add-body-return-transaction)

---

## Step 1: Import the message signature function

You can find another recipe that explains how to do this.

## Step 2: Patch the transaction ID from your payment

When you first make a payment you'll receive a transactionId, use this in the URL to patch it.

## Step 3: Add the body ReturnTransaction

Complete the body with the required details.

```javascript
fiservEncode(
    "PATCH",
    url + req.params.transactionId,
    {
      "requestType": "ReturnTransaction",
      "transactionAmount": {
        "total": "1",
        "currency": "EUR"
      }
    }
    (options) => {
      //Reminder, it's not seeing the request type
      request(options, function (error, paymentResponse) {
        if (error) throw new Error(error);
        let paymentData = JSON.parse(paymentResponse.body);
        return res.status(200).json({
          requestName: "Payment PATCH - Updating to notify that 3DSData has been received by the 3DSMethod",
          ...paymentData,
        });
      });
    }
  );
 ``` 
