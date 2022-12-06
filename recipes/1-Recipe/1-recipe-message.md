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

```python
import hmac
import hashlib
import base64
import requests
import time
import uuid
```

## Step 2: Add your API key & secret key

Insert your API key and secret here.

```python
key = 'Insert your API key here'
secret = 'Insert your API secret here'
```

## Step 3: Generate a unique ID for each request

We can use this track your individual API calls later.

```python
clientRequestId = str(uuid.uuid4())
```

## Step 4: Generate the unix time

```python
timestamp = str(int(time.time()))
```

## Step 5: Combine all of these ingredients into a string

To prepare our message for signing, we need to combine all the ingredients together into a string.

```python
message = '{}{}{}{}'.format(apiKey, clientRequestId, timestamp, str(content))
```

## Step 6: Hash using SHA256

Using your desired library, take the message and using the secret key, create the hash and then base64 encode it.

There are libraries that may make this easier, such as CryptoJS in our javascript example.

```python
signature = hmac.new(apiSecret.encode(), message.encode(), hashlib.sha256).digest()
b64_sig = base64.b64encode(signature).decode()
```

## Step 7: Set up your headers

We need to add the signature, along with some of the values used as headers to the request.

```python
headers = {
    'Api-Key': apiKey,
    'Timestamp': timestamp,
    'Client-Request-Id': clientRequestId,
    'Message-Signature': b64_sig
}
print(headers)
```

## Step 8: Make the request!

Submit your request along with the message signature headers.

```python
r = requests.post(url, content, headers=headers)
print(r.content)
```
