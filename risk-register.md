# Risk Register – Student Login Portal

**Rating Scale:**  
- Likelihood: 1 (Rare) → 5 (Almost Certain)  
- Impact: 1 (Negligible) → 5 (Critical)  
- Risk Score = Likelihood × Impact  
- Priority: 🔴 HIGH (15–25) | 🟡 MEDIUM (8–14) | 🟢 LOW (1–7)

---

## Risk Table (Ranked by Score)

| Rank | ID | STRIDE | Threat | Likelihood | Impact | Score | Priority |
|------|----|--------|--------|------------|--------|-------|----------|
| 1 | T01 | Spoofing | Credential stuffing – attacker logs in using stolen passwords from other breaches | 4 | 5 | 20 | 🔴 HIGH |
| 2 | T04 | Denial of Service | Brute force flood on login endpoint – no rate limiting | 4 | 4 | 16 | 🔴 HIGH |
| 3 | T02 | Tampering | SQL injection in login fields to bypass authentication or dump database | 3 | 5 | 15 | 🔴 HIGH |
| 4 | T06 | Spoofing | Session hijacking via XSS – stolen cookie reused by attacker | 3 | 5 | 15 | 🔴 HIGH |
| 5 | T03 | Information Disclosure | Database breach exposes plaintext student passwords | 2 | 5 | 10 | 🟡 MEDIUM |
| 6 | T07 | Elevation of Privilege | Role parameter manipulation – student gains admin access | 2 | 5 | 10 | 🟡 MEDIUM |
| 7 | T05 | Information Disclosure | Verbose error messages reveal server/DB info to attacker | 4 | 3 | 12 | 🟡 MEDIUM |
| 8 | T08 | Repudiation | No audit logs – cannot investigate or prove login events | 3 | 3 | 9 | 🟡 MEDIUM |
| 9 | T09 | Denial of Service | Account lockout abuse – attacker locks out legitimate students | 3 | 3 | 9 | 🟡 MEDIUM |
| 10 | T10 | Information Disclosure | Password sent over HTTP (no HTTPS) – intercepted on network | 2 | 5 | 10 | 🟡 MEDIUM |

---

## Risk Summary

| Priority | Count | Threat IDs |
|----------|-------|------------|
| 🔴 HIGH | 4 | T01, T02, T04, T06 |
| 🟡 MEDIUM | 6 | T03, T05, T07, T08, T09, T10 |
| 🟢 LOW | 0 | — |

---

## Top Risks – Detail with Rationale

### 🔴 T01 – Credential Stuffing (Score: 20)
- **Rationale:** Attackers routinely use leaked username/password lists. Without MFA, any student reusing passwords is immediately vulnerable. Full account takeover is the result.
- **Mitigation:** MFA (TOTP), breach-password detection at registration and login.

### 🔴 T04 – Login Flood / DoS (Score: 16)
- **Rationale:** No rate limiting means an attacker can send unlimited requests. Easy to automate with free tools. Renders the portal unavailable for all students.
- **Mitigation:** Rate limit to 5 attempts/min/IP, CAPTCHA after 3 failures.

### 🔴 T02 – SQL Injection (Score: 15)
- **Rationale:** If login fields are not using parameterized queries, a single crafted input can bypass authentication entirely or expose all student records.
- **Mitigation:** Parameterized queries, ORM, Web Application Firewall.

### 🔴 T06 – Session Hijacking via XSS (Score: 15)
- **Rationale:** If the app reflects unsanitized input, an attacker can steal the session cookie via a script and fully impersonate any student without needing their password.
- **Mitigation:** HttpOnly + Secure cookie flags, Content Security Policy (CSP), input sanitization.
