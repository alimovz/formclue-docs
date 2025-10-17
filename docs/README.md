# 🚀 FormClue API Documentation

Welcome to the **FormClue API**. Use this API to manage certificates and track your lead data.

---

## 🔑 Get Your API Key

Create your API key here:  
[https://dash.formclue.io/profile/api](https://dash.formclue.io/profile/api)

All requests **must** include your API key in the header:

`api_key: xxxxxxxxxxx-xxxx`


---

## 🌐 API Base URL

**All endpoints start with:**

`https://api.formclue.io/v1.0/clientapi`


---

## 🧪 Test Your API Key

**Test your key with this GET request:**

```bash
curl -X GET https://api.formclue.io/v1.0/clientapi/test \
     -H "api_key: xxxxxxxxxxx-xxxx" \
     -H "Content-Type: application/json"
```

**Success Response:**
```json
{
    "msg": "ok",
    "company": "[YOUR COMPANY NAME]"
}
```
**Bad Response:**
```json
{
    "msg": "error",
    "descr": "description of error"
}
```

## 📥 Retain a Certificate
**To retain a certificate, make a GET request:**

```bash
curl -X GET https://api.formclue.io/v1.0/clientapi/retain/{certificate_id} \
     -H "api_key: xxxxxxxxxxx-xxxx" \
     -H "Content-Type: application/json"
```

**Success Response:**
```json
{
    "msg": "ok",
    "descr": "Certificate [cert_id_here] is retained",
    "credits": "[number of credits left]"
}
```
**Bad Response:**
```json
{
    "msg": "error",
    "descr": "description of error"
}
```

## ✅ Check Form Submission
The following API call allows you to check if a form submission
was detected during the certificate's web session. Form submissions
are a valuable action on the part of the user and strongly indicate
he or she took action while on the page.

**To check if a cert has a form submission associate with it, make the following GET request**

```bash
curl -X GET https://api.formclue.io/v1.0/clientapi/ifsubmit/{certificate_id} \
     -H "api_key: xxxxxxxxxxx-xxxx" \
     -H "Content-Type: application/json"
```

**Success Response:**
```json
{
    "msg": "ok",
    "ifsubmit": true
}
```
`ifsubmit` will have one of two values: `true` or `false`. `true` means
a form submission was detected on the page, `false` means no submissions
were detected.

**Bad Response:** (means something went wrong during your 
request, and we could not return a result)
```json
{
    "msg": "error",
    "descr": "description of error"
}
```



## 📜 Validate Certificate
The Validate Certificate endpoint allows you to confirm that a 
given certificate ID is valid and that the specified email 
and/or phone number are indeed associated with it in FormClue’s records.

This endpoint is primarily used by lead buyers to ensure that
a received Certificate ID corresponds to a legitimate lead 
certified by FormClue.

**Example POST request**

```bash
curl -X POST https://api.formclue.io/v1.0/clientapi/validate/{certificate_id} \
     -H "api_key: xxxxxxxxxxx-xxxx" \
     -H "Content-Type: application/json"
     -d '{
           "email": "lead@example.com",
           "phone": "5551234567"
         }'
```

You may pass either `email`, `phone`, or both.

If both are passed, both must match for the certificate to be verified.

If only one field is passed, FormClue will validate based solely on that field.

---

**Success Response:**
```json
{
    "msg": "ok",
    "verified": true
}
```
`verified` will have one of two values: `true` or `false`. `true` means
the certificate does associate with your email and/or phone, `false` means
FormClue did not associate posted email/phone with the certificate, which
fails validation.

**Bad Response:** 
```json
{
    "msg": "error",
    "descr": "description of error"
}
```



# Code Examples
## Retaining a Certificate

**Node:**
```javascript
const CERT_ID = "xxxxxxxx-a7c3-4689-bece-xxxxxxxxx-1565"; // Replace with your cert ID
const API_KEY = "YOUR_API_KEY_HERE"; // Replace with your API key
const BASE_URL = "https://api.formclue.io/v1.0/clientapi";

async function retainCert(certId) {
  try {
    const response = await fetch(`${BASE_URL}/retain/${certId}`, {
      method: "GET",
      headers: {
        "api_key": API_KEY,
        "Content-Type": "application/json"
      }
    });

    const data = await response.json();

    if (data.msg === "ok") {
      console.log(`Success! ${data.descr}`);
      console.log(`Credits left: ${data.credits}`);
    } else {
      console.error(`Error: ${data.descr}`);
    }
  } catch (err) {
    console.error("Request failed:", err);
  }
}

// Run the function
retainCert(CERT_ID);

```

**PHP:**
```php
<?php
$cert_id = "xxxxxxxx-a7c3-4689-bece-xxxxxxxxx-1565"; // Replace with your cert ID
$api_key = "YOUR_API_KEY_HERE"; // Replace with your API key
$base_url = "https://api.formclue.io/v1.0/clientapi";

// Initialize cURL
$ch = curl_init();

// Set URL and headers
curl_setopt($ch, CURLOPT_URL, "$base_url/retain/$cert_id");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_HTTPHEADER, [
    "api_key: $api_key",
    "Content-Type: application/json"
]);

// Execute request
$response = curl_exec($ch);

// Check for errors
if (curl_errno($ch)) {
    echo "cURL Error: " . curl_error($ch);
} else {
    $data = json_decode($response, true);

    if ($data['msg'] === 'ok') {
        echo "Success! " . $data['descr'] . PHP_EOL;
        echo "Credits left: " . $data['credits'] . PHP_EOL;
    } else {
        echo "Error: " . $data['descr'] . PHP_EOL;
    }
}

// Close cURL
curl_close($ch);

```


## Validating a Certificate

**Node:**
```javascript
const axios = require('axios');

// Replace with your actual certificate ID and API key
const certificateId = 'xxxxxxxx-a7c3-4689-bece-xxxxxxxxx-1565';
const apiKey = 'YOUR_API_KEY_HERE';

const payload = {
  email: 'lead@example.com',
  phone: '5551234567'
};

async function validateCertificate() {
  try {
    const response = await axios.post(
      `https://api.formclue.io/v1.0/clientapi/validate/${certificateId}`,
      payload,
      {
        headers: {
          'api_key': apiKey,
          'Content-Type': 'application/json'
        }
      }
    );

    console.log('Response:', response.data);
  } catch (error) {
    if (error.response) {
      // Server returned an error response
      console.error('Error response:', error.response.data);
    } else {
      // Network or other error
      console.error('Error:', error.message);
    }
  }
}

validateCertificate();

```