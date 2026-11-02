# Threat Model – Student Login Portal

**Environment:** Fictional student portal running only on localhost or an isolated VM.  
Test defensive controls only. No external systems scanned.  
All IP addresses use documentation ranges (192.0.2.x per RFC 5737).

---

## System Diagram

```
┌──────────────────────────────────────────────────────────────┐
│                   UNTRUSTED ZONE (Internet)                  │
│                                                              │
│     [Student User]          [Attacker]                       │
│           │                      │                           │
└───────────┼──────────────────────┼───────────────────────────┘
            │                      │
            │        HTTPS (Port 443)
            ▼                      ▼
┌──────────────────────────────────────────────────────────────┐
│                  DMZ – Web Tier                              │
│                                                              │
│         ┌────────────────────────────┐                       │
│         │      Web Server            │                       │
│         │  - Login Form (HTML/JS)    │                       │
│         │  - Input Validation        │                       │
│         │  - HTTPS Termination       │                       │
│         └────────────┬───────────────┘                       │
└──────────────────────┼───────────────────────────────────────┘
                       │ Internal API (Port 8080)
┌──────────────────────┼───────────────────────────────────────┐
│                  TRUSTED ZONE – App Tier                     │
│                                                              │
│         ┌────────────────────────────┐                       │
│         │    Authentication Server   │                       │
│         │  - Credential Check        │                       │
│         │  - Session Token Issue     │                       │
│         │  - Rate Limiting           │                       │
│         └────────────┬───────────────┘                       │
└──────────────────────┼───────────────────────────────────────┘
                       │ DB Query (Port 5432)
┌──────────────────────┼───────────────────────────────────────┐
│              HIGHLY TRUSTED ZONE – Data Tier                 │
│                                                              │
│         ┌────────────────────────────┐                       │
│         │       User Database        │                       │
│         │  - Hashed Passwords        │                       │
│         │  - Student Records         │                       │
│         │  - Audit Logs              │                       │
│         └────────────────────────────┘                       │
└──────────────────────────────────────────────────────────────┘
```

---

## Trust Boundaries

| Boundary | Entry Point | Description |
|----------|-------------|-------------|
| Internet → DMZ | HTTPS Port 443 | All user input is untrusted here |
| DMZ → App Tier | Internal API Port 8080 | Only sanitized data passes through |
| App Tier → Data Tier | DB Port 5432 | Parameterized queries only |

---

## Actors

| Actor | Trust Level | Description |
|-------|-------------|-------------|
| Student User | Low (external) | Legitimate user with valid credentials |
| Attacker | None | Malicious actor attempting unauthorized access |
| Admin | High (internal) | Manages user accounts and system config |
| Auth Server | High | Internal service that validates credentials |
| Database | High | Stores student records and password hashes |

---

## External Dependencies

| Dependency | Risk |
|------------|------|
| TLS Certificate Authority | Certificate revocation could break HTTPS |
| Third-party auth libraries | Supply chain attack risk |
| Email provider (password reset) | Account takeover via email compromise |
| Cloud/hosting provider | Infrastructure breach or downtime |

---

## Data Flows

| Flow | From | To | Data | Protocol |
|------|------|----|------|----------|
| 1 | Student Browser | Web Server | Username + Password | HTTPS POST |
| 2 | Web Server | Auth Server | Sanitized credentials | Internal HTTP |
| 3 | Auth Server | Database | Parameterized query | PostgreSQL |
| 4 | Database | Auth Server | User record / hash | PostgreSQL |
| 5 | Auth Server | Web Server | Session token or error | Internal HTTP |
| 6 | Web Server | Student Browser | Session cookie / response | HTTPS |

---

## STRIDE Threat Scenarios

### S – Spoofing

| Asset | Owner |
|-------|-------|
| Student user account | Auth Server |

| Trust Boundary | Entry Point |
|----------------|-------------|
| Internet → DMZ | HTTPS login form |

| Threat Scenario | Precondition |
|----------------|--------------|
| Attacker uses stolen credentials from another breach to log in as a student | Credentials reused from another site; no MFA enabled |

| Existing Control | Evidence |
|-----------------|----------|
| Password required to log in | Login form exists |

| Risk Rating | Rationale |
|-------------|-----------|
| **HIGH** – Likelihood: 4/5, Impact: 5/5 | Credential stuffing is common; full account takeover possible |

| Recommended Mitigation | Verification Step |
|------------------------|-------------------|
| Enable MFA (TOTP); integrate breach-password detection | Test login blocked when TOTP not provided |

---

### T – Tampering

| Asset | Owner |
|-------|-------|
| Login request parameters | Web Server |

| Trust Boundary | Entry Point |
|----------------|-------------|
| Internet → DMZ | Login form POST request |

| Threat Scenario | Precondition |
|----------------|--------------|
| Attacker injects SQL in username/password field to bypass authentication | No parameterized queries used |

| Existing Control | Evidence |
|-----------------|----------|
| Basic input fields | No server-side validation confirmed |

| Risk Rating | Rationale |
|-------------|-----------|
| **HIGH** – Likelihood: 3/5, Impact: 5/5 | SQL injection can bypass auth or dump entire database |

| Recommended Mitigation | Verification Step |
|------------------------|-------------------|
| Use parameterized queries / ORM; add WAF | Run sqlmap against login page; confirm no injection possible |

---

### R – Repudiation

| Asset | Owner |
|-------|-------|
| Login events and actions | Auth Server |

| Trust Boundary | Entry Point |
|----------------|-------------|
| App Tier | Auth Server logs |

| Threat Scenario | Precondition |
|----------------|--------------|
| Student denies logging in or performing an action; no audit trail exists | No logging implemented |

| Existing Control | Evidence |
|-----------------|----------|
| None confirmed | — |

| Risk Rating | Rationale |
|-------------|-----------|
| **MEDIUM** – Likelihood: 3/5, Impact: 3/5 | Cannot investigate incidents without audit logs |

| Recommended Mitigation | Verification Step |
|------------------------|-------------------|
| Log all login events (user ID, IP, timestamp, success/fail) | Check log file after test login attempt |

---

### I – Information Disclosure

| Asset | Owner |
|-------|-------|
| Student passwords and personal data | Database |

| Trust Boundary | Entry Point |
|----------------|-------------|
| Data Tier | Database storage |

| Threat Scenario | Precondition |
|----------------|--------------|
| Database breach exposes plaintext student passwords | Passwords stored without hashing |

| Existing Control | Evidence |
|-----------------|----------|
| None confirmed | — |

| Risk Rating | Rationale |
|-------------|-----------|
| **HIGH** – Likelihood: 2/5, Impact: 5/5 | Plaintext passwords cause immediate mass account compromise |

| Recommended Mitigation | Verification Step |
|------------------------|-------------------|
| Hash all passwords with bcrypt (cost ≥ 12) | Query DB and confirm no plaintext passwords exist |

---

### D – Denial of Service

| Asset | Owner |
|-------|-------|
| Login endpoint availability | Web Server |

| Trust Boundary | Entry Point |
|----------------|-------------|
| Internet → DMZ | HTTPS Port 443 |

| Threat Scenario | Precondition |
|----------------|--------------|
| Attacker floods login endpoint with thousands of automated requests | No rate limiting or CAPTCHA in place |

| Existing Control | Evidence |
|-----------------|----------|
| None confirmed | — |

| Risk Rating | Rationale |
|-------------|-----------|
| **HIGH** – Likelihood: 4/5, Impact: 4/5 | Easy to execute; makes portal unavailable for all students |

| Recommended Mitigation | Verification Step |
|------------------------|-------------------|
| Implement rate limiting (5 attempts/min/IP); add CAPTCHA | Use load testing tool; confirm requests throttled after limit |

---

### E – Elevation of Privilege

| Asset | Owner |
|-------|-------|
| Admin routes and student records | Auth Server / Database |

| Trust Boundary | Entry Point |
|----------------|-------------|
| App Tier → Data Tier | Session token / URL parameters |

| Threat Scenario | Precondition |
|----------------|--------------|
| Attacker manipulates session role parameter to gain admin-level access | No server-side role validation; roles stored client-side |

| Existing Control | Evidence |
|-----------------|----------|
| Login required | Roles not validated server-side |

| Risk Rating | Rationale |
|-------------|-----------|
| **MEDIUM** – Likelihood: 2/5, Impact: 5/5 | If exploited, attacker gains full admin control |

| Recommended Mitigation | Verification Step |
|------------------------|-------------------|
| Implement server-side RBAC; never trust client-supplied roles | Attempt to access /admin as student; confirm 403 returned |

---

*All secrets and personal data have been redacted from this report. Environment is fictional and isolated.*
