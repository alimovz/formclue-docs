# 🚀 FormClue API Documentation

Welcome to the **FormClue API** — a simple REST API for managing and tracking your lead data.  
All requests must be authenticated using your API key.

---

## 🔑 Authentication

Include your API key in every request header:
api_key: YOUR_API_KEY_HERE

**Example Request:**
```bash
curl -X POST https://api.formclue.com/v1/clientapi/test \
     -H "api_key: test_12345" \
     -H "Content-Type: application/json" \
     -d '{"cert_id": "abc123"}'



