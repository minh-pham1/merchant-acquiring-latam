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
```

## Step 2: Add your API key & secret key

Insert your API key and secret here

```json
key = 'Insert your API key here'
secret = 'Insert your API secret here'
```

## Step 3: Generate a unique ID for each request

We can use this track your individual API calls later

```json
clientRequestId = str(uuid.uuid4())
```

## Step 4: Generate the unix time

<!--
type: tab
titles: Javascript, Pyhton, Java
-->

### Javascript

```javascript
  var time = new Date().getTime();
```

<!--
type: tab
-->

### Python

```python
clientRequestId = str(uuid.uuid4())
```

<!-- type: tab -->

### Java

```java
            final String timestamp = Long.toString(Instant.now().toEpochMilli());
```

<!-- type: tab-end -->
