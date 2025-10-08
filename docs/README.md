# 🚀 FormClue API Documentation

Welcome to the **FormClue API**. Use this API to manage certificates and track your lead data.

---

## 🔑 Step 1 — Get Your API Key

Create your API key here:  
[https://dash.formclue.io/profile/api](https://dash.formclue.io/profile/api)

All requests **must** include your API key in the header:

api_key: xxxxxxxxxxx-xxxx


---

## 🌐 Step 2 — Base URL

All endpoints start with:

https://api.formclue.io/v1.0/clientapi


---

## 🧪 Step 3 — Test Your API Key

Test your key with:

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

## 📥 Step 4 — Retain a Certificate
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


## Code Examples:
**Retaining a certificate.**

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
?>


```