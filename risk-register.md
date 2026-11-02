# Risk Register
## Simple Login Web Application – STRIDE Analysis

**Rating Scale:**  
- Likelihood: 1 (Low) → 5 (High)  
- Impact: 1 (Low) → 5 (High)  
- Risk Score = Likelihood × Impact  
- Priority: HIGH (15–25) | MEDIUM (8–14) | LOW (1–7)

---

## STRIDE Threat Table

| ID | STRIDE Category | Threat Description | Attack Vector | Likelihood | Impact | Risk Score | Priority |
|----|----------------|-------------------|---------------|------------|--------|------------|----------|
| T01 | **Spoofing** | Attacker uses stolen credentials to log in as another user | Credential stuffing / phishing | 4 | 5 | 20 | 🔴 HIGH |
| T02 | **Spoofing** | Session token stolen via XSS and reused by attacker | Cross-site scripting attack | 3 | 5 | 15 | 🔴 HIGH |
| T03 | **Tampering** | Attacker modifies login request to bypass authentication | Parameter tampering via intercepted request | 2 | 5 | 10 | 🟡 MEDIUM |
| T04 | **Tampering** | SQL Injection to alter database queries | Malicious input in login fields | 3 | 5 | 15 | 🔴 HIGH |
| T05 | **Repudiation** | User denies performing a login action | No audit log exists | 3 | 3 | 9 | 🟡 MEDIUM |
| T06 | **Information Disclosure** | Password transmitted in plaintext over HTTP | Lack of HTTPS / network sniffing | 2 | 5 | 10 | 🟡 MEDIUM |
| T07 | **Information Disclosure** | Database breach exposes plaintext passwords | Weak or no password hashing | 2 | 5 | 10 | 🟡 MEDIUM |
| T08 | **Information Disclosure** | Verbose error messages reveal system info | Error handling exposes stack traces | 4 | 3 | 12 | 🟡 MEDIUM |
| T09 | **Denial of Service** | Brute force / flooding the login endpoint | Automated requests with no rate limiting | 4 | 4 | 16 | 🔴 HIGH |
| T10 | **Denial of Service** | Account lockout abuse to lock out legitimate users | Intentionally triggering lockout on victim accounts | 3 | 3 | 9 | 🟡 MEDIUM |
| T11 | **Elevation of Privilege** | Attacker gains admin access via broken access control | Manipulating role/session parameters | 2 | 5 | 10 | 🟡 MEDIUM |
| T12 | **Elevation of Privilege** | Attacker bypasses login entirely via insecure direct object reference | Guessing or manipulating user ID in URL | 2 | 5 | 10 | 🟡 MEDIUM |

---

## Risk Summary

| Priority | Count | Threat IDs |
|----------|-------|------------|
| 🔴 HIGH | 4 | T01, T02, T04, T09 |
| 🟡 MEDIUM | 8 | T03, T05, T06, T07, T08, T10, T11, T12 |
| 🟢 LOW | 0 | — |

---

## Top 4 High Priority Risks – Detail

### T01 – Credential Stuffing / Account Takeover
- **Threat:** Attacker uses leaked username/password lists from other breaches
- **Impact:** Full account takeover, data theft
- **Mitigation:** Multi-factor authentication, breach-password detection, rate limiting

### T02 – Session Hijacking via XSS
- **Threat:** Malicious script steals session cookie
- **Impact:** Attacker gains full authenticated session
- **Mitigation:** HttpOnly cookies, Content Security Policy (CSP), input sanitization

### T04 – SQL Injection
- **Threat:** Attacker injects SQL in login fields to bypass auth or dump database
- **Impact:** Authentication bypass, full database exposure
- **Mitigation:** Parameterized queries / prepared statements, ORM usage, WAF

### T09 – Brute Force / DoS on Login Endpoint
- **Threat:** Attacker sends thousands of login attempts automatically
- **Impact:** System slowdown, account compromise, denial of service
- **Mitigation:** Rate limiting, CAPTCHA, IP blocking, account lockout policy
