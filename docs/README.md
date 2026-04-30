# **How Evidora Script Works**

Evidora is a digital interaction and evidence platform that enables organizations to document and verify form submissions and related website interactions. The Platform generates integrity-preserving records designed to help businesses validate user activity, reduce fraudulent or automated submissions, and support compliance with applicable communication and consent requirements.

---

## Step 1: Verify Your Domain

Before Evidora can create Evidence Records, you need to verify your domain in the back office:

**👉 [Verify Your Domain](https://dash.evidora.io/dashboard/domains)**

Once your domain is verified and our script is placed on your page(s),
Evidora will automatically start monitoring and recording all user interactions on that domain.

---

## Step 2: Add the Evidora Script

Next, grab your unique Evidora tracking script from your dashboard:

**👉 [Get Your Script](https://dash.evidora.io/dashboard/certify)**

Copy the script and paste it at the **bottom of your page**, just before the closing `</body>` tag.

> **💡 Tip:** Placing the script at the bottom ensures your page content loads first, keeping performance optimal. With that said, placing it in other parts of the page will still work.

---

## Step 3: What Happens When the Script Loads

Once the Evidora script loads on your page, here's what happens automatically:

#### Evidora Detects All Forms
The script scans your page and identifies every `<form>` element.

#### Hidden Fields Are Injected
Evidora **automatically appends 2 hidden fields** to every form on your page:

1. **Evidence Record ID** — A unique identifier for this specific session/submission
2. **Replay Link** — A direct link to view the recorded replay in your dashboard

These fields look like this in your form's HTML:

```html
<input type="hidden" name="evidence_record_id" value="xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx-xxxx">
<input type="hidden" name="evidence_lookup_link" value="https://dash.evidora.io/dashboard/lookup?e-rec-id=xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx-xxxx">
```

#### Evidence Record ID (evidence_record_id) in the `window` API

For convenience, Evidora also exposes the Evidence Record ID for the current session directly on the global `window` object:

```javascript
window.Evidora.ERecordID // "xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx-xxxx"
```

- This value is **identical** to the `evidence_record_id` injected into each form as a hidden field.
- The global variable is available **even if there are no forms on the page**. This allows you to reference the session’s Evidence Record ID elsewhere in your application (for example, passing it to APIs, analytics, or UI elements).
- You can access it immediately after the script loads.

#### Example Usage

```javascript
console.log('Current Evidence Record ID:', window.Evidora.ERecordID);
```

> **Note:** Having a global `ERecordID` enables non-form-based workflows and integrations—use it whenever you need to associate actions or data on the page with the current Evidora session.

---

## Step 4: Verify Leads with Evidence Record Data

When a user submits a form, the **hidden fields are submitted along with the rest of the form data**.

This means when you (or your CRM, email system, or webhook) receive the lead, you'll have:

| Field | Purpose                                                     |
|-------|-------------------------------------------------------------|
| `evidence_record_id` | Unique Evidence Record ID for this submission               |
| `evidence_lookup_link` | Direct link to replay the session and view other session info |

### How to Use the Evidence Record Data

#### Option 1: Click the Replay Link
Simply open the `evidence_lookup_link` in your browser to watch a **high-fidelity replay** of exactly how the user interacted with your form/website:
- See what they typed, corrected, and changed
- Detect bot-like behavior or suspicious patterns
- Verify the lead is legitimate before spending time or money on it

#### Option 2: Use the Evidora API for Validation & Retention
You can programmatically validate and retain e-records using the **Evidora Client API** (documented below):

**Validation Options:**
- **Verify email or phone association** — Confirm that a specific email address or phone number is tied to an Evidence Record ID
- **Check for form submission** — Detect whether a form submit event was captured on the page
- **Retain the e-record** — Store the e-record in your account for **5 years** as permanent proof of consent or submission

**Example Use Cases:**
- Validate a lead in your CRM before calling or emailing them
- Automatically flag and filter out e-records without form submissions
- Retain high-value e-records for long-term compliance and legal protection

> **💡 Pro Tip:** Retaining an e-record ensures the replay data is preserved for 5 years, giving you ironclad proof against unsolicited contact claims or regulatory audits.

---
# **Bot Detection**

Evidora employs a sophisticated, multi-factor bot detection system designed to validate the authenticity of every user session. By analyzing browser integrity, hardware capabilities, and behavioral biometrics, we assign a **Bot Score** to every generated evidence record.

This ensures that the leads you purchase or verify are generated by real humans, protecting your budget from automated scripts, scrapers, and headless browsers.

> **Important:** Reliable behavioral analysis requires data. E-Records generated from sessions lasting **less than 10 seconds** may have less accurate bot scores, as the system has had limited time to observe sufficient user interaction.

## The Bot Score

Every e-record is assigned a probability score ranging from **0 to 100**. This score represents the likelihood that the session was initiated by automated software.

| Score Range &nbsp;&nbsp;&nbsp;&nbsp;   | Classification   | Description |
|:---------------|:-----------------| :--- |
| **0 - 30**     | **Likely Human** | High-confidence human behavior. Validated interaction patterns (mouse curves, touch gestures) and clean browser environment. |
| **31 - 79**    | **Suspicious**   | Inconclusive or irregular behavior. May indicate privacy-hardened browsers, unusual device configurations, or extremely low activity. |
| **80 - 100**   | **Likely Bot**   | High-confidence automated traffic. Detected signals such as WebDriver usage, headless environments, or superhuman input speeds. |

> **Recommendation:** For the highest quality assurance, we recommend accepting leads with a score of **30 or below**.


## How It Works

Our detection engine runs silently in the background of the user session, utilizing a "Passive Analysis" approach. Rather than relying on simple CAPTCHAs that disrupt the user experience, we analyze three core dimensions of the session:

1.  **Behavioral Biometrics**  
    We analyze how the user interacts with the page over time—including mouse movement patterns, touch gestures, scrolling behavior, and typing rhythm. Humans have natural variances in their movements; bots are often mechanically perfect or suspiciously idle.

2.  **Browser & Device Integrity**  
    We validate that the browser environment matches the claimed device type. This includes checking for hardware capabilities (such as touch support on mobile devices) and identifying inconsistencies common in "spoofed" user agents.

3.  **Automation Fingerprinting**  
    We scan for technical signatures left behind by automation software (such as Selenium, Puppeteer, or Playwright) and headless browsers.


## Retrieving the Bot Score

Once an e-record is generated, its calculated Bot Score is available immediately through our API. We recommend verifying this score before processing any lead to filter out high-risk traffic programmatically.

You can retrieve the score, along with session duration and device metadata, using the **Get E-Record Metadata** endpoint.

[View API Documentation: Get E-Record Metadata](https://docs.evidora.io/#/?id=get-e-record-metadata)

---

# **Domain Verification**

This section will walk you through how to verify a domain in Evidora.

---

## Using DNS

To verify your domain ownership, you'll need to add a TXT record to your domain's DNS settings. Follow the steps below to complete the verification process.

### Step 1: Create a TXT Record

1. Log in to your domain registrar or DNS hosting provider (e.g., GoDaddy, Cloudflare, Namecheap, Route 53)
2. Navigate to your DNS management section
3. Create a new TXT record with the following details:

**Host/Name Field:**
```
_evidora
```

**Record Type:**
```
TXT
```

**Value/Content Field:**

Copy the unique verification key provided in the Evidora interface. It will look similar to this:
```
y0fl595xl......pgf5fqhgbk1bx
```

> **Note:** Your verification key is unique to your domain. Make sure to copy the exact value shown in your Evidora dashboard, not the example above.
>
> **Example:**
>
> ![DNS Verification Example](https://evidora.io/images/docs/dns_verification.png)

### Step 2: Save the DNS Record

After entering all the required information, save or publish the DNS record through your DNS provider's interface.

> **Important:** DNS changes can take anywhere from a few seconds to 48 hours to propagate, though most changes take effect within a few minutes.

### Step 3: Verify Your Domain

1. Return to the Evidora domain verification page
2. Click the **"Verify by DNS"** button
3. Evidora will check your DNS records for the TXT record
4. If the record is found, your domain will be verified successfully

> **💡 Pro Tip:** After adding the DNS records and clicking the "Verify by DNS" button, Evidora will automatically retry verification in the background if the initial attempt is unsuccessful due to DNS propagation delays. There is no need to manually refresh or repeatedly click the verification button. Once your DNS records have fully propagated, the system will automatically detect the changes and mark your domain as verified. Simply ensure the TXT record has been added correctly, and Evidora will handle the verification process.


# **Placing Evidora Script**

This section will walk you through how to place Evidora's script on your site.

---

## Auto Mode

You may load the Evidora script in Auto Mode. 
In Auto Mode the recorder is initialized automatically when 
the script is loaded and begins capturing user interactions 
immediately. Recording continues until the user 
leaves the page (e.g., closes the tab/window or navigates away).
Use Auto Mode when you want continuous, page-level capture
with no manual lifecycle control. **This is the default mode.**

Technical summary:
- Activation: automatic on script load.
- Lifecycle: starts on load; stops on page unload/navigation.
- Use case: full-page capture, minimal integration work.

## Manual Mode

Example of enabled Manual Mode in your control panel: https://dash.evidora.io/dashboard/certify — open the Advanced Options dropdown.

![Manual Mode Enabled](https://evidora.io/images/docs/manual_mode.png)

Manual Mode provides explicit programmatic control over the recorder lifecycle. When Manual Mode is enabled in your Evidora configuration (see admin/UI), the page gains access to two public API functions:

- `Evidora.startRecorder()`
- `Evidora.stopRecorder()`

Note: These functions are exposed on the global Evidora object (accessible as window.Evidora in browser contexts).


**API**
- `Evidora.startRecorder()`: void
    - Begins a new recording session. Each call to `Evidora.startRecorder()` generates a new evidence record ID which is injected into the page as a hidden field on all forms (so form submissions can be associated with the current recording session).
    - Do not call `Evidora.startRecorder()` repeatedly unless you intend to start distinct recording sessions; each invocation produces a new e-record ID.
- `Evidora.stopRecorder()`: void
    - Stops the active recording session. Use this to end capture when you no longer need to record user interactions.

**Usage patterns**
- Targeted section recording (recommended for multi-form / co-reg / multi-section apps)
    - Scenario: a single page application (SPA) or a page with multiple forms where you only want to record a specific part of the user journey.
    - Pattern:
        1. Keep the Evidora script included on the page (Manual Mode enabled).
        2. When the target section becomes active, call `Evidora.startRecorder()`.
        3. When the target section is complete, call `Evidora.stopRecorder()`.
        4. You may call `Evidora.startRecorder()` again later in the same session to record another discrete segment; each start generates a new e-record ID and subsequent hidden field value.

**Code examples**

- Manual start/stop around an application section:

```javascript
function onSectionEnter() {
    // begin recording for this section
    window.Evidora && window.Evidora.startRecorder();
}
```

```javascript
function onSectionExit() {
    // stop recording when the section is finished
    window.Evidora && window.Evidora.stopRecorder();
}
```

- Or start/stop recorder on button click:

```javascript
document.getElementById('start-btn').addEventListener('click', function (e) {
    window.Evidora && window.Evidora.startRecorder();
});

document.getElementById('stop-btn').addEventListener('click', function (e) {
    window.Evidora && window.Evidora.stopRecorder();
});
```

`Evidora.startRecorder()` and `Evidora.stopRecorder()` can be called
from anywhere on your page.

**Best practices and important notes**
- Ensure the Evidora script is present on the page before calling the API. Use an `onload` handler for dynamically inserted scripts or perform a runtime check (`window.Evidora`) before invoking any methods.
- Each `startRecorder()` call generates a unique Evidence Record (**e-record**) ID. This ID is automatically attached as a hidden field to all forms on the page. Design your integration so the e-record lifecycle aligns with your form submission flow.
- Avoid running the recorder for prolonged or unnecessary segments. Use targeted `startRecorder()` and `stopRecorder()` calls to limit captured data and keep e-records focused and relevant.
- Calling `startRecorder()` multiple times within a single page session creates multiple e-records (one per start). The most recent call replaces the active e-record ID for subsequent form submissions until another `startRecorder()` call is made.
- `stopRecorder()` halts capture immediately. Subsequent form submissions will continue to include the most recently generated e-record ID (unless a new `startRecorder()` call replaces it). Confirm this behavior in your environment if you require different lifecycle semantics.

**Troubleshooting**
- If `startRecorder()` or `stopRecorder()` are undefined, confirm Manual Mode is enabled in your Evidora configuration and that the Evidora script has fully loaded.
- If forms are not receiving the hidden e-record field after calling `startRecorder()`, ensure the forms exist in the DOM at the time of invocation. For dynamically rendered forms, re-run `startRecorder()` after insertion so the hidden field can be attached.
- For single-page application (SPA) route transitions, call `stopRecorder()` before unmounting the recorded section and `startRecorder()` when the target section mounts. This ensures e-records are properly scoped to the intended user interaction.

# **Evidence Records**

This section will give you a general overview of how Evidence Records are generated, retained and stored.

---

## Expiration

Unretained Evidora e-records automatically expire based on 
detected user activity. E-Records generated where no form 
submission is detected expire 3 days after generation, while
e-records that include a verified form submit event expire
30 days after generation. Once an e-record expires, it is 
permanently deleted from our system and can no longer be 
accessed, viewed, or shared, and any associated links 
immediately become invalid.

## Retention

Evidora offers an optional e-record retention feature that allows customers to preserve e-records beyond the standard expiration period.
When retained, the e-record is securely stored for up to five (5) years, during which time it may be accessed and viewed at any time. Retained e-records may also be shared with third parties, enabling organizations to provide verified proof of data collection, user interaction, and compliance whenever required.

## Timezones

All times shown on Evidora's e-records are displayed relative to a 
timezone, depending on how the e-record is accessed. 
When viewing an e-record while logged into your user account,
all timestamps associated with that e-record - including the 
visit date and the running time shown during session replay - are
shown in your user’s configured timezone. This timezone can
be adjusted at any time from your Profile settings. 
When a e-record is viewed via its publicly accessible 
link (without logging in), those same dates and times
are displayed in the timezone of the original session
being recorded, reflecting the end user’s local timezone 
at the time of the visit.


# **API Documentation**

Use this API to manage evidence records and track your session data.

---

## Get Your API Key

Create your API key here:  
[https://dash.evidora.io/profile/api](https://dash.evidora.io/profile/api)

All requests **must** include your API key in the header:

`api_key: xxxxxxxxxxx-xxxx`


---

## API Base URL

**All endpoints start with:**

`https://api.evidora.io/v1.0/clientapi`


---

## Test Your API Key

**Test your key with this GET request:**

```bash
curl -X GET https://api.evidora.io/v1.0/clientapi/test \
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

## Retain an E-Record

You can retain an e-record by making either a **GET** or **POST** request to the Evidora API.

- **GET request** (as shown below) simply retains the specified e-record.
- **POST request** allows you to include an optional `source` parameter in the request body, which you can use to indicate the originating platform or application making the retention request.

**Example: GET request**

```bash
curl -X GET https://api.evidora.io/v1.0/clientapi/retain/{e-record-id} \
     -H "api_key: xxxxxxxxxxx-xxxx" \
     -H "Content-Type: application/json"
```

**Example: POST request**
Include the `source` field in the JSON body to tag the request with your platform's identifier:

```bash
curl -X POST https://api.evidora.io/v1.0/clientapi/retain/{e-record-id} \
     -H "api_key: xxxxxxxxxxx-xxxx" \
     -H "Content-Type: application/json" \
     -d '{"source": "my-platform"}'
```

Use the POST variant if you have multiple systems integrating with the API and want to track or audit which platform is initiating the retention. The `source` parameter should be set to a concise identifier for your application or service.

**Request body format:**
```json
{
  "source": "my-platform"
}
```

**Success Response:**
```json
{
    "msg": "ok",
    "descr": "E-Record [e-record-id-here] is retained",
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

## Check Form Submission
The following API call allows you to check if a form submission
was detected during the e-record's web session. Form submissions
are a valuable action on the part of the user and strongly indicate
he or she took action while on the page.

**To check if an e-record has a form submission associate with it, make the following GET request**

```bash
curl -X GET https://api.evidora.io/v1.0/clientapi/ifsubmit/{e-record-id} \
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



## Validate E-Record
The Validate E-Records endpoint allows you to confirm that a 
given e-record ID is valid and that the specified email 
and/or phone number are indeed associated with it in Evidora’s database.

This endpoint is primarily used by lead buyers to ensure that
a received e-record ID corresponds to a legitimate lead 
recorded by Evidora.

**Example POST request**

```bash
curl -X POST https://api.evidora.io/v1.0/clientapi/validate/{e-record-id} \
     -H "api_key: xxxxxxxxxxx-xxxx" \
     -H "Content-Type: application/json"
     -d '{
           "email": "lead@example.com",
           "phone": "5551234567"
         }'
```

You may pass either `email`, `phone`, or both.

If both are passed, both must match for the e-record to be verified.

If only one field is passed, Evidora will validate based solely on that field.

---

**Success Response:**
```json
{
    "msg": "ok",
    "verified": true
}
```
`verified` will have one of two values: `true` or `false`. `true` means
the e-record does associate with your email and/or phone, `false` means
Evidora did not associate posted email/phone with the e-record, which
fails validation.

**Bad Response:** 
```json
{
    "msg": "error",
    "descr": "description of error"
}
```


## Get E-Record Metadata
This endpoint retrieves technical details regarding the
user session for a specific e-record ID. This includes the bot score,
device type, and the total duration of the session.

This endpoint is primarily used by lead buyers to gauge the quality of a lead
before purchase. By analyzing the `bot_score` and `duration`, you can
programmatically filter out automated or high-risk traffic.

> **Note:** Your account has to have retention credits to use this endpoint.

**Example GET request**

```bash
curl -X GET https://api.evidora.io/v1.0/clientapi/metadata/{e-record-id} \
     -H "api_key: xxxxxxxxxxx-xxxx" \
     -H "Content-Type: application/json"
```

---

**Success Response (Example):**
```json
{
  "msg": "ok",
  "metadata": {
    "duration": 91448,
    "device_type": "mobile",
    "bot_score": 30
  }
}
```

**Response Fields:**

*   `duration`: The length of the user session in milliseconds.
    > *Note: Sessions with a duration under 10,000ms (10 seconds) may have less reliable bot scores.*
*   `device_type`: The detected device category (e.g., `desktop`, `mobile`, `tablet`).
*   `bot_score`: A probability score from 0 to 100 indicating the likelihood of bot traffic (0 being human).

**Bad Response:**
```json
{
    "msg": "error",
    "descr": "description of error"
}
```


## Search Evidence Records by Phone or Email

**Endpoint:**  
`https://api.evidora.io/v1.0/clientapi/search`

Use this endpoint to search e-records by email and/or phone.
You can only search e-records you have generated and/or retained.

**Example POST request**

```bash
curl -X POST https://api.evidora.io/v1.0/clientapi/search \
     -H "api_key: xxxxxxxxxxx-xxxx" \
     -H "Content-Type: application/json"
     -d '{
           "email": "user@example.com",
           "phone": "1234567890"
         }'
```

Send a JSON payload with the following parameters:

```json
{
  "email": "user@example.com",
  "phone": "1234567890"
}
```

- `email` _(optional)_: The email address of the lead.
- `phone` _(optional)_: The phone number of the lead.

Both fields are optional, but at least one must be provided.
- If both are present, the search will return e-records matching **both** the specified email and phone.
- If only one field is present, the search will return e-records matching by **either** email or phone.

**Response:**

**When No E-Records Are Found**

```json
[]
```

**When E-Records Are Found**

A successful response returns an array of e-record objects. Each object contains:

- `e-record-id`: Unique Evidence Record ID for the lead
- `date_created`: ISO timestamp when the lead was created
- `ip`: IP address associated with the lead
- `signup_url`: URL where the signup occurred
- `email`: Email address of the lead
- `phone`: Phone number of the lead
- `status`: Array of status strings, e.g. `["generated", "retained"]`
- `share_key`: A unique key to access the e-record
- `share_link`: A URL that allows anyone with the link to view the e-record's details, including web session replay
- 
Example:

```json
[
  {
    "e-record-id": "************",
    "date_created": "2025-11-21T05:43:03.271Z",
    "ip": "***.***.***.***",
    "signup_url": "https://example.com/path",
    "email": "user@example.com",
    "phone": "**********",
    "status": ["generated", "retained"],
    "share_key": "gw7UqzDmvXXXXXXXXXXXXXXXXM5v90U",
    "share_link": "https://dash.evidora.io/dashboard/lookup?e-rec-id=*****************&share_key=gw7UqzDmvXXXXXXXXXXXXXXXXM5v90U"
  },
  {
    "e-record-id": "************",
    "date_created": "2025-12-12T16:06:05.434Z",
    "ip": "***.***.***.***",
    "signup_url": "https://example.com/path",
    "email": "user@example.com",
    "phone": "**********",
    "status": ["generated", "retained"],
    "share_key": "gw7UqzDmvXXXXXXXXXXXXXXXXM5v90U",
    "share_link": "https://dash.evidora.io/dashboard/lookup?e-rec-id=*****************&share_key=gw7UqzDmvXXXXXXXXXXXXXXXXM5v90U"
  },
  {
    "e-record-id": "************",
    "date_created": "2026-01-09T02:17:58.627Z",
    "ip": "***.***.***.***",
    "signup_url": "https://example.com/another-path",
    "email": "user@example.com",
    "phone": "**********",
    "status": ["generated"],
    "share_key": "gw7UqzDmvXXXXXXXXXXXXXXXXM5v90U",
    "share_link": "https://dash.evidora.io/dashboard/lookup?e-rec-id=*****************&share_key=gw7UqzDmvXXXXXXXXXXXXXXXXM5v90U"
  }
]
```

## Share E-Records

**Endpoint:**  
`https://api.evidora.io/v1.0/clientapi/share/{e-record-id}`

This endpoint allows clients to obtain a shareable link and key for a specific e-record.
**Only users who generated or have retained access to the e-record may share it.**

**Example GET request**

```bash
curl -X GET https://api.evidora.io/v1.0/clientapi/share/{e-record-id} \
     -H "api_key: xxxxxxxxxxx-xxxx" \
     -H "Content-Type: application/json"
```
Replace `{e-record-id}` with the ID of the e-record you wish to share.

**Success Response**

```json
{
    "msg": "ok",
    "share_key": "eKkLyhCcSTlyxxxxxxxxxxxxxxxxR8aiI6jg",
    "share_link": "https://dash.evidora.io/dashboard/lookup?e-rec-id=xxxxxxxx-67c6-4689-b888-xxxxxxxx-1925&share_key=eKkLyhCcSTlyxxxxxxxxxxxxxxxxR8aiI6jg"
}
```

- **share_key:** A unique key to access the e-record.
- **share_link:** A URL that allows anyone with the link to view the e-record's details, including web session replay.

**Error Response**

If you are not authorized to share the e-record:

```json
{
    "msg": "error",
    "descr": "You're not authorized to share this e-record."
}
```

---

**Important Notes**

- Only the e-record owner (who generated it) or a user who has retained the e-record is authorized to share it.
- The share link grants public access to the e-record and includes web session replay.
- When sharing unretained e-records, public access
will expire whenever the e-records itself expires. See e-record [expiration rules](#expiration) for more details


## Get E-Record Consent Language

**Endpoint:**  
`https://api.evidora.io/v1.0/clientapi/consent/{e-record-id}`

This endpoint returns an array of consent blocks detected on the e-record's form fill.  
**Consent blocks identify sections where the user gave explicit or implied consent.**

**Example GET request**

```bash
curl -X GET https://api.evidora.io/v1.0/clientapi/consent/{e-record-id} \
     -H "api_key: xxxxxxxxxxx-xxxx" \
     -H "Content-Type: application/json"
```
Replace `{e-record-id}` with the ID of the e-record you wish to query.

**Success Response**

```json
{
    "msg": "ok",
    "consent": [
        {
            "consent_type": "checkbox",
            "agree": true,
            "text": "I give permission to ......"
        },
        {
            "consent_type": "clickwrap",
            "agree": true,
            "text": "By clicking the Submit button, I...."
        },
        {
            "consent_type": "checkbox",
            "agree": true,
            "text": "By clicking the checkbox below, I hereby ...."
        },
        {
            "consent_type": "checkbox",
            "agree": true,
            "text": "By clicking “I Agree”, ...."
        }
    ]
}
```

- **consent_type:** The type of consent block detected. Can be `checkbox` or `clickwrap`.
    - *checkbox:* Indicates explicit agreement (can be `agree: true` or `agree: false`)
    - *clickwrap:* Indicates agreement by proceeding (always `agree: true`)
- **agree:** Whether the user agreed to the consent language.
- **text:** The consent language from the form.

**No Consent Blocks Detected**

If no consent blocks are found on the form:

```json
{
    "msg": "ok",
    "consent": []
}
```

**Error Response**

If an error occurs while querying the API:

```json
{
    "msg": "error",
    "descr": "description of error"
}
```

**Notes**
- Consent detection relies on the script identifying standardized consent sections during form completion.
- Checkboxes may show `agree: false` if the user did not check them.
- Clickwrap blocks always report `agree: true` since the user consents by proceeding past the block.


# **Webhooks**

Webhooks allow you to securely deliver real-time payment 
transaction evidence to Evidora from your platform. 
This ensures that all key customer payment events and 
transaction data are properly linked to the visitor's evidence 
record.

---

## Custom Webhooks

**Custom webhooks** are used in cases where you have the 
ability to post payment transaction details directly to Evidora,
rather than relying on an external payment provider (such as Stripe or Paypal)
to do so. This allows your system to send transaction data 
to Evidora and associate it with a specific digital evidence
record (a user session tracked by Evidora).

**Example Request:**
```bash
curl -X POST https://api.evidora.io/v1.0/clientapi/webhooks/custom \
     -H "Content-Type: application/json" \
     -d '{
           "event_id": "trx_9876543210",
           "event_type": "transaction.completed",
           "evidence_record_id": "0mocuwndo-d5b0-481a-aed8-fa0006c703bc-2060",
           "customer_email": "jane.doe@example.com",
           "customer_name": "Jane Doe",
           "amount": 2745,
           "currency": "usd",
           "card_last4": "5666",
           "products": [
             {
               "name": "Pillow For Dogs",
               "quantity": 2,
               "unit_price": 999,
               "line_total": 1998
             },
             {
               "name": "Doggy Treats Bag",
               "quantity": 3,
               "unit_price": 249,
               "line_total": 747
             }
           ]
         }'
```
---

### **Parameter Details**

- **event_id** (*string, required*)  
  Unique identifier for the payment transaction (e.g., transaction or order ID from your processor). Used to deduplicate incoming records.

- **event_type** (*string, optional*)  
  The type of transaction event, such as "transaction.completed", "charge.success", etc.

- **evidence_record_id** (*string, required*)  
  The Evidence Record ID (generated on your site by the Evidora tracking script (often stored in a hidden field named `evidence_record_id`). This ties the transaction to a user’s web session and digital evidence record.  
  **If you do not have this, the transaction cannot be linked and the webhook will be rejected.**

- **customer_email** (*string, required*)  
  The payer's email address.

- **customer_name** (*string, optional*)  
  Full name of the customer.

- **amount** (*integer, required*)  
  The payment amount in the smallest unit (e.g., cents). For $14.99, use `1499`.

- **currency** (*string, optional, default: "usd"*)  
  ISO 3-letter currency code (e.g., "usd", "eur").

- **card_last4** (*string, optional*)  
  The last four digits of the payment card (if available).

- **products** (*array, optional*)  
  List each product purchased.  
  Each object requires:
    - `name` (*string*): Product name.
    - `quantity` (*integer*): Number purchased.
    - `unit_price` (*integer*): Price per unit, in smallest unit (e.g., cents).
    - `line_total` (*integer*): Total for this product line (unit_price * quantity).

**Posting product line items:**  
Add a `products` array as shown above. Each entry gives Evidora detailed context of what was purchased in the transaction during the user's session - allowing for stronger evidence records.

---

### **Example Success Response**
```json
{
    "msg": "ok"
}
```

### **Example Error Response**
```json
{
    "msg": "error",
    "descr": "[Description of error here]"
}
```

---

### Best Practices & Notes

- **Always use the `evidence_record_id`** from the user's web session (provided by Evidora's JS tag on your page).
- **Send a unique `event_id` with each request**: Sending a unique `event_id` allows for easier troubleshooting should technical issues arise.
- If you have technical integration questions, contact our support or refer to additional documentation.

---



# **Code Examples**
## Retaining an E-Record

**Node:**
```javascript
const ID = "xxxxxxxx-a7c3-4689-bece-xxxxxxxxx-1565"; // Replace with your e-record ID
const API_KEY = "YOUR_API_KEY_HERE"; // Replace with your API key
const BASE_URL = "https://api.evidora.io/v1.0/clientapi";

async function retainERecord(id) {
  try {
    const response = await fetch(`${BASE_URL}/retain/${id}`, {
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
retainERecord(ID);

```

**PHP:**
```php
<?php
$e_rec_id = "xxxxxxxx-a7c3-4689-bece-xxxxxxxxx-1565"; // Replace with your e-record ID
$api_key = "YOUR_API_KEY_HERE"; // Replace with your API key
$base_url = "https://api.evidora.io/v1.0/clientapi";

// Initialize cURL
$ch = curl_init();

// Set URL and headers
curl_setopt($ch, CURLOPT_URL, "$base_url/retain/$e_rec_id");
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


## Validating an E-Record

**Node:**
```javascript
const axios = require('axios');

// Replace with your actual e-record ID and API key
const e_recordId = 'xxxxxxxx-a7c3-4689-bece-xxxxxxxxx-1565';
const apiKey = 'YOUR_API_KEY_HERE';

const payload = {
  email: 'lead@example.com',
  phone: '5551234567'
};

async function validateERecord() {
  try {
    const response = await axios.post(
      `https://api.evidora.io/v1.0/clientapi/validate/${e_recordId}`,
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

validateERecord();

```