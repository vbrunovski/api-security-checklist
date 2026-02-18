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

- [ ] ---

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
