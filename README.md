# api-security-checklist
Practical guide and checklist for finding API vulnerabilities (OWASP API Top 10)

# 🛡️ API Security Testing Methodology & Checklist

Practical guide and checklist for finding API vulnerabilities based on the **OWASP API Security Top 10 (2023)**.

## 🚀 How to use
1. **Fork** this repository.
2. Use the checklist during your **Pentest** or **Bug Bounty** journey.
3. Check the boxes `- [x]` as you complete each task.

---

## 🔍 Phase 1: Reconnaissance & Enumeration
*Before attacking, map the attack surface and find hidden entry points.*

- [ ] **Documentation Discovery:** Search for `/swagger.json`, `/v1/api-docs`, `/openapi.yaml`, `/raml`.
- [ ] **Endpoint Fuzzing:** Use `ffuf`, `dirsearch` or `gobuster` to find hidden endpoints (e.g., `/debug`, `/admin`, `/internal`).
- [ ] **Parameter Discovery:** Use `Arjun` to find hidden GET/POST parameters.
- [ ] **Version Scanning:** Check for older API versions (e.g., `/v1/`, `/v2/` vs `/beta/`).
- [ ] **Method Testing:** Try changing HTTP methods (e.g., Change `GET` to `POST`, `PUT`, `PATCH` or `DELETE`).

## 🔑 Phase 2: Broken Object Level Authorization (BOLA / IDOR)
*Can you access someone else's data by manipulating IDs?*

- [ ] **ID Substitution:** - Capture: `GET /api/v1/messages/1000`
  - Test: `GET /api/v1/messages/1001` (Check if you can see another user's data).
- [ ] **UUID Leakage:** Check if "unpredictable" UUIDs are leaked in other public API responses (e.g., in public user profiles).
- [ ] **Numeric vs String IDs:** If the API uses strings, try converting them to integers and vice versa.
- [ ] **Bypassing with Headers:** Try adding `X-User-Id: 101` or `X-Remote-IP` to trick the server into thinking you are someone else.

---

## 📝 Phase 3: Broken Object Property Level Authorization (Mass Assignment)
*Can you update fields you shouldn't or see sensitive hidden data?*

- [ ] **Mass Assignment (Field Injection):** Try adding admin fields to `POST/PUT` requests:
  ```json
  {
    "username": "guest",
    "role": "admin",
    "is_vip": true,
    "balance": 99999
  }
- [ ] Excessive Data Exposure: Inspect JSON responses for sensitive PII (emails, home addresses, internal IDs) hidden on the frontend but present in the API response.

- [ ] Read-only Field Update: Try to modify fields that are supposed to be "read-only" (e.g., created_at or user_id).


## 🏗️ Phase 4: Unrestricted Resource Consumption
*Testing how the API handles high loads and large data.*

- [ ] **Rate Limiting:** Can you send 1000+ requests per minute? (Use Burp Intruder).
- [ ] **Large Payload Attacks:** Send a massive JSON body or a very long string to cause a Denial of Service (DoS).
- [ ] **Pagination Abuse:** Try high values in limit parameters: `/api/users?limit=999999`.
- [ ] **File Upload Limits:** If the API allows uploads, try sending a file that exceeds the documented size limit.

---

## 🔐 Phase 5: Broken Authentication & JWT
*Is the identity verification process secure?*

- [ ] **JWT Manipulation:** Test for `alg: none`, weak secret keys (brute-force), and missing signature validation.
- [ ] **Token Expiration:** Check if tokens remain valid after the user logs out.
- [ ] **Sensitive Data in JWT:** Decode the token at `jwt.io` to check for passwords or internal roles in plain text.
- [ ] **Token Replay:** Can a captured token be reused from a different IP or User-Agent?

---

## 🛠️ Phase 6: Server-Side Request Forgery (SSRF)
*Can you force the API server to make requests to internal resources?*

- [ ] **URL Parameter Injection:** If the API fetches data from a provided URL (e.g., for profile pictures or webhooks), try:
  - `http://localhost:80` (To find internal services).
  - `http://169.254.169.254/latest/meta-data/` (Cloud Metadata for AWS/Azure).
- [ ] **Blind SSRF:** Use a tool like `Burp Collaborator` to check if the server pings your external domain.
- [ ] **File Protocol:** Try `file:///etc/passwd` to see if the server can read local files.

---

## ⚠️ Phase 7: Security Misconfiguration
*Checking for "sloppy" server setups and info leaks.*

- [ ] **CORS Policy:** Check for `Access-Control-Allow-Origin: *`. Try to see if credentials (cookies) can be sent from a malicious domain.
- [ ] **Error Handling:** Trigger 404/500 errors. Do they reveal stack traces, database versions, or internal file paths?
- [ ] **HTTP Headers:** Check for missing security headers: `HSTS`, `Content-Security-Policy`, and `X-Content-Type-Options`.
- [ ] **Verbosity:** Check if `DEBUG` mode is enabled (common in staging environments).


## 🤖 Phase 8: Lack of Protection from Automated Threats
*Can the API be easily abused by scripts and bots?*

- [ ] **Credential Stuffing:** Test if you can automate 100+ login attempts without a CAPTCHA or account lockout.
- [ ] **Data Scraping:** Can you iterate through IDs (e.g., `/users/1` to `/1000`) and dump the database without being blocked?
- [ ] **SMS/Email Bombing:** Check if the "forgot password" or "OTP" endpoints have strict rate limits.

---

## 📂 Phase 9: Improper Inventory Management
*Finding "forgotten" endpoints that developers didn't secure.*

- [ ] **Shadow APIs:** Look for unofficial paths like `/beta/`, `/v0/`, or `/internal/`.
- [ ] **Deprecated Versions:** Check if old `/v1/` endpoints are still alive and have vulnerabilities fixed in `/v2/`.
- [ ] **Subdomain Recon:** Search for `dev-api.target.com` or `staging.api.com` — they often lack WAF/Auth.

---

## 🔗 Phase 10: Unsafe Consumption of APIs
*Does the API trust other services too much?*

- [ ] **Third-party Data Trust:** If the API uses data from Slack or GitHub, can you inject a payload into those services to trigger an exploit in *your* API?
- [ ] **Webhooks:** Can you spoof a webhook call from a payment provider (like Stripe) to "confirm" a fake purchase?

---

## 🛠️ Tooling Recommendations
* **Burp Suite** — The industry standard web proxy.
* **Postman** — Best for manual API requests and collections.
* **ffuf** — High-speed fuzzer for endpoint discovery.
* **Arjun** — Finds hidden HTTP parameters.
* **KiteRunner** — Advanced tool specifically for API content discovery.
* **JWT.io** — Essential for manual JWT decoding and analysis.

---

## 📚 Resources
* [OWASP API Security Top 10 Official](https://owasp.org/www-project-api-security/)
* [APIsecurity.io](https://apisecurity.io/) - Weekly newsletter.
