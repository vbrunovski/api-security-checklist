# api-security-checklist
Practical guide and checklist for finding API vulnerabilities (OWASP API Top 10)

Markdown
# 🛡️ API Security Testing Methodology & Checklist

Practical guide and checklist for finding API vulnerabilities based on the **OWASP API Security Top 10 (2023)**.

## 🚀 How to use
1. **Fork** this repository.
2. Use the checklist during your **Pentest** or **Bug Bounty** journey.
3. Check the boxes `- [x]` as you complete each task.
4. (Optional) Integrate these checks into your **CI/CD pipeline**.

---

## 🔍 Phase 1: Reconnaissance & Enumeration
*Before attacking, map the attack surface and find hidden entry points.*

- [ ] **Documentation Discovery:** Search for `/swagger.json`, `/v1/api-docs`, `/openapi.yaml`, `/raml`.
- [ ] **Endpoint Fuzzing:** Use `ffuf`, `dirsearch` or `gobuster` to find hidden endpoints (e.g., `/debug`, `/admin`, `/internal`).
- [ ] **Parameter Discovery:** Use `Arjun` to find hidden GET/POST parameters that might be vulnerable.
- [ ] **Version Scanning:** Check for older API versions (e.g., `/v1/`, `/v2/` vs `/beta/`).
- [ ] **Method Testing:** Try changing HTTP methods (e.g., Change `GET` to `POST`, `PUT`, `PATCH` or `DELETE`).

---

## 🔑 Phase 2: Broken Object Level Authorization (BOLA / IDOR)
*Can you access someone else's data by manipulating IDs?*

- [ ] **ID Substitution:** - Capture: `GET /api/v1/messages/1000`
  - Test: `GET /api/v1/messages/1001` (Check if you can see another user's data).
- [ ] **UUID Leakage:** Check if "unpredictable" UUIDs are leaked in other public API responses (e.g., in a public user profile).
- [ ] **Numeric vs String IDs:** If the API uses strings, try converting them to integers and vice versa.

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
[ ] Excessive Data Exposure: Inspect JSON responses for sensitive PII (emails, home addresses, internal IDs) that are sent by the server but hidden by the frontend.

🏗️ Phase 4: Unrestricted Resource Consumption
Testing how the API handles high loads and large data.

[ ] Rate Limiting: Can you send 1000+ requests per minute? (Use Burp Intruder).

[ ] Large Payload Attacks: Try sending a massive JSON body or a very long string to cause a Denial of Service (DoS).

[ ] Pagination Abuse: Try high values in limit parameters: /api/users?limit=99999999.

🔐 Phase 5: Broken Authentication & JWT
Is the identity verification process secure?

[ ] JWT Manipulation: Test for alg: none, weak secret keys (brute-force), and missing signature validation.

[ ] Token Expiration: Check if tokens remain valid after the user logs out.

[ ] Sensitive Data in JWT: Decode the token at jwt.io to check for plain-text passwords or sensitive internal data.

[ ] Password Reset Logic: Test for host header injection in reset links or predictable reset tokens.

🛠️ Phase 6: Server-Side Request Forgery (SSRF)
[ ] URL Parameters: If the API fetches data from a provided URL, try to point it to:

http://localhost:80 (Internal services)

http://169.254.169.254/latest/meta-data/ (Cloud Metadata for AWS/Azure).

⚠️ Phase 7: Security Misconfiguration
[ ] CORS Policy: Check for Access-Control-Allow-Origin: * which might allow data theft from browsers.

[ ] Error Handling: Do error messages reveal stack traces, database versions, or internal file paths?

[ ] Security Headers: Check for missing HSTS, Content-Security-Policy, and X-Content-Type-Options.

🤖 Phase 8: Lack of Protection from Automated Threats
[ ] Credential Stuffing: Check if the /login endpoint has a CAPTCHA or lockout mechanism.

[ ] Scraping: Can you automate the collection of all public data without being blocked by a WAF?

📂 Phase 9: Improper Inventory Management
[ ] Shadow & Rogue APIs: Look for /v0/, /v1/, /test/, or /staging/ versions that might be unpatched.

[ ] Host Header Injection: Can you manipulate the Host header to redirect traffic or poison caches?

🔗 Phase 10: Unsafe Consumption of APIs
[ ] Third-party Trust: Does your API trust data from integrated services (e.g., Slack, GitHub, Payment Gateways) without proper sanitization?

[ ] Injection via 3rd Party: Try to inject malicious payloads into the 3rd party service to see if your API executes them.
