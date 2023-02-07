# Look up card information

Find out information about a card such as its brand, issuer & function.

---

- [Step 1: Make a post request with the card number](#step-1-make-post-request)

---

## Step 1: Make a post request with the card number

Generating the message signature requires that we hash the payload of the API call.
Firstly, import the required libraries.

```javascript
var raw = JSON.stringify({
  "paymentCard": {
    "number": "5204********1002"
  }
});

var requestOptions = {
  method: 'POST',
  headers: myHeaders,
  body: raw,
  redirect: 'follow'
};

fetch("https://prod.emea.api.fiservapps.com/sandbox/ipp/payments-gateway/v2/card-information", requestOptions)
  .then(response => response.text())
  .then(result => console.log(result))
  .catch(error => console.log('error', error));
```
