# Security Hardening Checklist – Student Login Portal

*Optional portfolio upgrade: hardening checklist with verification steps*

---

## 🔴 HIGH PRIORITY – Fix Immediately

| # | Control | Addresses | Verification Step |
|---|---------|-----------|-------------------|
| 1 | Enable HTTPS (TLS 1.2+); redirect all HTTP to HTTPS | T10 | Visit http:// and confirm redirect to https://; check TLS version with SSL Labs |
| 2 | Use parameterized queries / prepared statements in all DB calls | T02 | Run SQLMap against login form; confirm no injection found |
| 3 | Hash passwords with bcrypt (cost factor ≥ 12) | T03 | Query DB directly; confirm all passwords show bcrypt hash format |
| 4 | Implement rate limiting: max 5 login attempts per minute per IP | T04 | Send 10 rapid requests; confirm 429 Too Many Requests returned |
| 5 | Set HttpOnly and Secure flags on all session cookies | T06 | Open browser DevTools → Application → Cookies; confirm flags present |
| 6 | Enable Multi-Factor Authentication (TOTP-based) | T01 | Attempt login without TOTP code; confirm login blocked |

---

## 🟡 MEDIUM PRIORITY – Fix Within 2 Weeks

| # | Control | Addresses | Verification Step |
|---|---------|-----------|-------------------|
| 7 | Add Content Security Policy (CSP) header | T06 | Check response headers; confirm Content-Security-Policy present |
| 8 | Suppress verbose error messages (show generic errors to user; log detail server-side) | T05 | Trigger a DB error; confirm user sees "Something went wrong" not stack trace |
| 9 | Log all login events: user ID, IP, timestamp, success/failure | T08 | Perform test login; confirm entry appears in audit log file |
| 10 | Implement CAPTCHA after 3 failed login attempts | T04 | Fail login 3 times; confirm CAPTCHA challenge appears |
| 11 | Implement server-side Role-Based Access Control (RBAC) | T07 | Log in as student; attempt to access /admin; confirm 403 Forbidden returned |
| 12 | Validate and sanitize all user inputs on both client and server side | T02, T06 | Submit `<script>alert(1)</script>` in login field; confirm it is not executed |
| 13 | Add breach-password detection at login and registration | T01 | Enter a known-breached password; confirm warning or block shown |
| 14 | Account lockout after 10 failed attempts (unlock after 15 minutes) | T09 | Fail login 10 times; confirm account temporarily locked |

---

## 🟢 ONGOING GOOD PRACTICE

| # | Control | Verification Step |
|---|---------|-------------------|
| 15 | Rotate session token immediately after successful login | Compare session cookie before and after login; confirm different value |
| 16 | Set session expiry: 30 minutes of inactivity | Leave session idle 31 minutes; confirm redirect to login page |
| 17 | Add security headers: X-Frame-Options, X-Content-Type-Options, Referrer-Policy | Check response headers using securityheaders.com |
| 18 | Run dependency audit regularly (`npm audit` or equivalent) | Run audit; confirm no high/critical vulnerabilities in dependencies |
| 19 | Redact all secrets and personal data from screenshots and reports | Review all submitted files; confirm no real passwords or PII present |

---

## Summary

| Priority | Controls | Key Threats Addressed |
|----------|----------|-----------------------|
| 🔴 HIGH | 6 | T01, T02, T03, T04, T06, T10 |
| 🟡 MEDIUM | 8 | T04, T05, T06, T07, T08, T09 |
| 🟢 ONGOING | 5 | General security hygiene |

---

*Environment: fictional student portal – localhost / isolated VM only.  
All secrets and personal data have been redacted from this report.*
