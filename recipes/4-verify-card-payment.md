# Verify a card payment

---

- [Step 1: Verify message signature](#step-1-verify-message-signature)

---

## Step 1: Verify message signature

```curl
var raw = JSON.stringify({
  "paymentCard": {
    "number": "5204********1002",
    "expiryDate": {
      "month": "10",
      "year": "22"
    },
    "securityCode": "002"
  },
  "billingAddress": {
    "address1": "5565 Glenridge Conn",
    "city": "Atlanta",
    "postalCode": "30342",
    "country": "USA"
  }
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://prod.emea.api.fiservapps.com/sandbox/ipp/payments-gateway/v2/card-verification", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));
```
