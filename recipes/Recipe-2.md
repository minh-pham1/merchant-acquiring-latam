# Make a card payment

We show you how to make a simple card payment

---

- [Step 1: Generate a message signature](#step-1-generate-message-signature)
- [Step 2: Configure the body](#step-2-configure-body)
- [Step 3: Submit the request](#step-3-submit-request)

---

## Step 1: Generate a message signature

You can find another recipe that explains how to do this.

```javascript
  fiservEncode(
```

## Step 2: Configure the body

Use PaymentCardSaleTransaction, enter the amount, currency and card details.

```javascript
      requestType: "PaymentCardSaleTransaction",
      transactionAmount: { total: "13", currency: "GBP" },
      paymentMethod: {
        paymentCard: {
          number: "1234********1234,
          securityCode: "123",
          expiryDate: { month: "02", year: "22" },
```

## Step 3: Submit the request

```javascript
  fiservEncode(
    "POST",
    url,
    {
      requestType: "PaymentCardSaleTransaction",
      transactionAmount: { total: "13", currency: "GBP" },
      paymentMethod: {
        paymentCard: {
          number: "1234********1234,
          securityCode: "123",
          expiryDate: { month: "02", year: "22" },
        },
      },
    },
    (options) => {
      //Submit the API call to Fiserv
      request(options, function (error, paymentResponse) {
        if (error) throw new Error(error);
        let paymentData = JSON.parse(paymentResponse.body);
      
        return res.status(200).json({
          requestName: "Payment POST - Creating the payment request",
          ...paymentData
        });
      });
    }
  );
```
