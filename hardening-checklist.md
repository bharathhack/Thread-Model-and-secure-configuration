# Prioritized Security Hardening Checklist
## Simple Login Web Application

---

## 🔴 HIGH PRIORITY – Fix Immediately

These controls address the highest-risk threats (T01, T02, T04, T09).

- [ ] **Enable HTTPS everywhere** – Enforce TLS 1.2+ on all endpoints; redirect HTTP to HTTPS
- [ ] **Use parameterized queries / prepared statements** – Prevent SQL injection (addresses T04)
- [ ] **Implement rate limiting on login endpoint** – Max 5 attempts per minute per IP (addresses T09)
- [ ] **Hash passwords with bcrypt** – Use bcrypt with cost factor ≥ 12; never store plaintext passwords
- [ ] **Set HttpOnly and Secure flags on session cookies** – Prevent JS from reading cookies (addresses T02)
- [ ] **Enable Multi-Factor Authentication (MFA)** – Add TOTP or SMS-based second factor (addresses T01)

---

## 🟡 MEDIUM PRIORITY – Fix Within 2 Weeks

These address medium-risk threats.

- [ ] **Implement Content Security Policy (CSP) header** – Restrict script sources to prevent XSS (T02)
- [ ] **Add account lockout after failed attempts** – Lock account for 15 mins after 10 failed logins
- [ ] **Suppress verbose error messages** – Show generic errors to users; log details server-side only (T08)
- [ ] **Add audit logging for all login events** – Log success, failure, IP, timestamp, user ID (T05)
- [ ] **Implement CAPTCHA on login form** – Use reCAPTCHA v3 for automated attack prevention (T09)
- [ ] **Validate and sanitize all user inputs** – Strip dangerous characters on both client and server side
- [ ] **Use role-based access control (RBAC)** – Ensure users cannot access admin routes (T11, T12)
- [ ] **Add breach password detection** – Check new passwords against known-breached password lists (T01)

---

## 🟢 GOOD PRACTICE – Ongoing

- [ ] **Rotate session tokens after login** – Issue a new session ID after successful authentication
- [ ] **Set session expiry** – Expire sessions after 30 minutes of inactivity
- [ ] **Use security headers** – Add X-Frame-Options, X-Content-Type-Options, Referrer-Policy
- [ ] **Conduct dependency audits** – Regularly run `npm audit` or equivalent to catch supply chain risks
- [ ] **Perform penetration testing** – Schedule quarterly pen tests or use automated DAST tools
- [ ] **Enable Web Application Firewall (WAF)** – Block common attack patterns at the network edge

---

## Hardening Priority Summary

| Priority | Controls | Threat IDs Addressed |
|----------|----------|----------------------|
| 🔴 HIGH | 6 controls | T01, T02, T04, T09 |
| 🟡 MEDIUM | 8 controls | T02, T05, T08, T09, T10, T11, T12 |
| 🟢 ONGOING | 6 controls | General hygiene |

---

*Prepared as part of Cybersecurity Internship – Task 2: Threat Model and Secure Configuration*
