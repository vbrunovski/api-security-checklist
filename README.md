# api-security-checklist
Practical guide and checklist for finding API vulnerabilities (OWASP API Top 10)

# 🛡️ API Security Testing Methodology & Checklist

## 🚀 How to use
1. **Fork** this repository.
2. Use the checklist during your **Pentest** or **Bug Bounty** journey.
3. Check the boxes `- [x]` as you complete each task.
4. (Optional) Integrate these checks into your **CI/CD pipeline**.

This repository contains a structured approach to performing security assessments on REST APIs. It focuses on identifying common vulnerabilities, including the **OWASP API Security Top 10**.

---

## 🔍 Phase 1: Reconnaissance & Enumeration
*Before attacking, we need to map the attack surface.*

- [ ] **Documentation Discovery:** Search for `/swagger.json`, `/v1/api-docs`, `/openapi.yaml`.
- [ ] **Endpoint Fuzzing:** Use `ffuf` or `Arjun` to find hidden endpoints (e.g., `/debug`, `/admin`, `/internal`).
- [ ] **Version Scanning:** Check for older API versions (e.g., `/v1/` vs `/v2/` vs `/beta/`).
- [ ] **Method Testing:** Try changing HTTP methods (e.g., Change `GET` to `POST`, `PUT`, or `DELETE`).

---

## 🔑 Phase 2: Broken Object Level Authorization (BOLA / IDOR)
*The most critical API vulnerability. Can you access someone else's data?*

- [ ] **ID Substitution:** - Capture: `GET /api/v1/messages/1000`
  - Test: `GET /api/v1/messages/1001` (Check if you can see another user's message).
- [ ] **UUID Brute Force:** If IDs are UUIDs, check if they are leaked in other API responses.
- [ ] **Header Manipulation:** Try adding `X-User-Id: 1` or `Referer` headers to bypass checks.

---

## 📝 Phase 3: Mass Assignment & Data Exposure
- [ ] **Field Injection:** Try adding administrative fields to `POST/PUT` requests:
  ```json
  {
    "username": "test",
    "password": "password123",
    "role": "admin",
    "is_vip": true
  }

  ---

## 🏗️ Phase 4: Resource & Hierarchy Logic
*Testing how the API handles resource limits and logic flows.*

- [ ] **Unrestricted Resource Consumption:** Can you send 10,000 requests per minute? Test for missing Rate Limiting.
- [ ] **Large Payload Attacks:** Try sending a massive JSON body (e.g., 5MB) to see if the server crashes (DoS).
- [ ] **Business Logic Flaws:** Can you buy a product for a negative price? (e.g., `"quantity": -1`).

---

## 🔐 Phase 5: Authentication & JWT (Broken Authentication)
*How does the API identify you, and can that identity be stolen?*

- [ ] **JWT Header Manipulation:** Change `alg` to `none` or try to switch from RS256 to HS256 (Key Confusion).
- [ ] **Token Expiration:** Does the token stay valid after logout or for 30+ days?
- [ ] **Weak Secrets:** Try to brute-force the JWT secret key using `hashcat` or `jwt-cracker`.
- [ ] **Sensitive Data in JWT:** Check if the payload contains PII (emails, names, roles) that shouldn't be there.

---

## 🛠️ Phase 6: Server-Side Request Forgery (SSRF)
- [ ] **URL Parameters:** If the API fetches data from a URL (e.g., `/api/v1/proxy?url=...`), try to point it to `http://localhost` or internal cloud metadata (e.g., `169.254.169.254`).

---

## ⚠️ Phase 7: Improper Assets Management
- [ ] **Host Header Injection:** Change the `Host` header to a malicious domain.
- [ ] **Information Disclosure:** Check if error messages reveal stack traces, database versions, or internal IP addresses.

- [ ] ---

## 🏗️ Phase 4: Resource & Hierarchy Logic
*Testing how the API handles resource limits and logic flows.*

- [ ] **Unrestricted Resource Consumption:** Can you send 10,000 requests per minute? Test for missing Rate Limiting.
- [ ] **Large Payload Attacks:** Try sending a massive JSON body (e.g., 5MB) to see if the server crashes (DoS).
- [ ] **Business Logic Flaws:** Can you buy a product for a negative price? (e.g., `"quantity": -1`).

---

## 🔐 Phase 5: Authentication & JWT (Broken Authentication)
*How does the API identify you, and can that identity be stolen?*

- [ ] **JWT Header Manipulation:** Change `alg` to `none` or try to switch from RS256 to HS256 (Key Confusion).
- [ ] **Token Expiration:** Does the token stay valid after logout or for 30+ days?
- [ ] **Weak Secrets:** Try to brute-force the JWT secret key using `hashcat` or `jwt-cracker`.
- [ ] **Sensitive Data in JWT:** Check if the payload contains PII (emails, names, roles) that shouldn't be there.

---

## 🛠️ Phase 6: Server-Side Request Forgery (SSRF)
- [ ] **URL Parameters:** If the API fetches data from a URL (e.g., `/api/v1/proxy?url=...`), try to point it to `http://localhost` or internal cloud metadata (e.g., `169.254.169.254`).

---

## ⚠️ Phase 7: Improper Assets Management
- [ ] **Host Header Injection:** Change the `Host` header to a malicious domain.
- [ ] **Information Disclosure:** Check if error messages reveal stack traces, database versions, or internal IP addresses.
