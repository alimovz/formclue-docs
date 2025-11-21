# **How FormClue Script Works**

FormClue is a session-intelligence and form-analytics platform that certifies and validates every form submission on your website. Think of it as a recording + forensic engine that **proves a user actually visited your site and submitted the form** — protecting you from claims of unsolicited contact or TCPA violations. It also helps you filter out bot traffic and fraudulent submissions, ensuring you only work with real, human interactions.

---

## Step 1: Verify Your Domain

Before FormClue can certify submissions, you need to verify your domain in the back office:

**👉 [Verify Your Domain](https://dash.formclue.io/dashboard/domains)**

Once your domain is verified, FormClue will automatically start monitoring and certifying all form interactions on that domain.

---

## Step 2: Add the FormClue Script

Next, grab your unique FormClue tracking script from your dashboard:

**👉 [Get Your Script](https://dash.formclue.io/dashboard/certify)**

Copy the script and paste it at the **bottom of your page**, just before the closing `</body>` tag.

> **💡 Tip:** Placing the script at the bottom ensures your page content loads first, keeping performance optimal. With that said, placing it in other parts of the page will still work.

---

## Step 3: What Happens When the Script Loads

Once the FormClue script loads on your page, here's what happens automatically:

### ✅ **FormClue Detects All Forms**
The script scans your page and identifies every `<form>` element.

### ✅ **Hidden Certification Fields Are Injected**
FormClue **automatically appends 2 hidden fields** to every form on your page:

1. **Certificate ID** — A unique identifier for this specific session/submission
2. **Replay Link** — A direct link to view the certified replay in your dashboard

These fields look like this in your form's HTML:

```html
<input type="hidden" name="fc_cert_id" value="xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx-xxxx">
<input type="hidden" name="fc_cert_lookup_link" value="https://dash.formclue.io/dashboard/lookup?cert_id=xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx-xxxx">
```
---

## Step 4: Verify Leads with Certification Data

When a user submits a form, the **hidden fields are submitted along with the rest of the form data**.

This means when you (or your CRM, email system, or webhook) receive the lead, you'll have:

| Field | Purpose |
|-------|---------|
| `fc_cert_id` | Unique certificate ID for this submission |
| `fc_cert_lookup_link` | Direct link to replay the session and verify authenticity |

### 🔍 **How to Use the Certification Data**

#### Option 1: Click the Replay Link
Simply open the `fc_cert_lookup_link` in your browser to watch a **high-fidelity replay** of exactly how the user filled out the form:
- See what they typed, corrected, and changed
- Detect bot-like behavior or suspicious patterns
- Verify the lead is legitimate before spending time or money on it

#### Option 2: Use the FormClue API for Validation & Retention
You can programmatically validate and retain certifications using the **FormClue Client API** (documented below):

**Validation Options:**
- **Verify email or phone association** — Confirm that a specific email address or phone number is tied to a certificate ID
- **Check for form submission** — Detect whether a form submit event was captured on the page
- **Retain the certificate** — Store the certification in your account for **5 years** as permanent proof of consent or submission

**Example Use Cases:**
- Validate a lead in your CRM before calling or emailing them
- Automatically flag and filter out certificates without form submissions
- Retain high-value certifications for long-term compliance and legal protection

> **💡 Pro Tip:** Retaining a certificate ensures the replay data is preserved for 5 years, giving you ironclad proof against unsolicited contact claims or regulatory audits.

---

# **Domain Verification**

This section will walk you through how to verify a domain in FormClue.

---

## **DNS Verification**

To verify your domain ownership, you'll need to add a TXT record to your domain's DNS settings. Follow the steps below to complete the verification process.

### **Step 1: Create a TXT Record**

1. Log in to your domain registrar or DNS hosting provider (e.g., GoDaddy, Cloudflare, Namecheap, Route 53)
2. Navigate to your DNS management section
3. Create a new TXT record with the following details:

**Host/Name Field:**
```
_formclue
```

**Record Type:**
```
TXT
```

**Value/Content Field:**

Copy the unique verification key provided in the FormClue interface. It will look similar to this:
```
y0fl595xl......pgf5fqhgbk1bx
```

> **Note:** Your verification key is unique to your domain. Make sure to copy the exact value shown in your FormClue dashboard, not the example above.

### **Step 2: Save the DNS Record**

After entering all the required information, save or publish the DNS record through your DNS provider's interface.

> **Important:** DNS changes can take anywhere from a few seconds to 48 hours to propagate, though most changes take effect within a few minutes.

### **Step 3: Verify Your Domain**

1. Return to the FormClue domain verification page
2. Click the **"Verify by DNS"** button
3. FormClue will check your DNS records for the TXT record
4. If the record is found, your domain will be verified successfully

> **💡 Pro Tip:** After adding the DNS records and clicking the "Verify by DNS" button, FormClue will automatically retry verification in the background if the initial attempt is unsuccessful due to DNS propagation delays. There is no need to manually refresh or repeatedly click the verification button. Once your DNS records have fully propagated, the system will automatically detect the changes and mark your domain as verified. Simply ensure the TXT record has been added correctly, and FormClue will handle the verification process.

# **FormClue API Documentation**

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



# **Code Examples**
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