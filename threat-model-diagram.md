# Threat Model Diagram
## Simple Login Web Application

---

## System Components

```
┌─────────────────────────────────────────────────────────────────┐
│                        INTERNET (Untrusted Zone)                │
│                                                                 │
│    [Attacker]          [Legitimate User]                        │
│         │                     │                                 │
└─────────┼─────────────────────┼───────────────────────────────-┘
          │                     │
          │         HTTPS (Port 443)
          │                     │
┌─────────┼─────────────────────┼────────────────────────────────┐
│         │   DMZ / Web Tier    │                                 │
│         ▼                     ▼                                 │
│   ┌──────────────────────────────────┐                         │
│   │        Web Server / Frontend     │                         │
│   │   - Login Form (HTML/JS)         │                         │
│   │   - Input Validation             │                         │
│   │   - HTTPS Termination            │                         │
│   └──────────────┬───────────────────┘                         │
│                  │                                              │
└──────────────────┼──────────────────────────────────────────────┘
                   │ Internal API Call (Port 8080)
┌──────────────────┼──────────────────────────────────────────────┐
│                  │   Application Tier (Trusted Zone)            │
│                  ▼                                              │
│   ┌──────────────────────────────────┐                         │
│   │      Authentication Server       │                         │
│   │   - Credential Verification      │                         │
│   │   - Session Token Generation     │                         │
│   │   - Rate Limiting Logic          │                         │
│   └──────────────┬───────────────────┘                         │
│                  │                                              │
└──────────────────┼──────────────────────────────────────────────┘
                   │ DB Query (Port 5432)
┌──────────────────┼──────────────────────────────────────────────┐
│                  │   Data Tier (Highly Trusted Zone)            │
│                  ▼                                              │
│   ┌──────────────────────────────────┐                         │
│   │         User Database            │                         │
│   │   - Hashed Passwords (bcrypt)    │                         │
│   │   - User Records                 │                         │
│   │   - Audit Logs                   │                         │
│   └──────────────────────────────────┘                         │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Trust Boundaries

| Boundary | Description |
|----------|-------------|
| **Internet → DMZ** | Untrusted users send HTTPS requests to the web server. All input is untrusted here. |
| **DMZ → App Tier** | Web server passes sanitized data to the auth server via internal API. |
| **App Tier → Data Tier** | Auth server queries the database using parameterized queries only. |

---

## Actors (Users)

| Actor | Trust Level | Description |
|-------|-------------|-------------|
| Legitimate User | Low (external) | Person with valid credentials trying to log in |
| Attacker | None | Malicious actor trying to gain unauthorized access |
| Admin | High (internal) | System administrator managing user accounts |
| Auth Server | High | Internal service that validates credentials |
| Database | High | Stores user data and password hashes |

---

## Data Flows

| Flow # | From | To | Data | Protocol |
|--------|------|----|------|----------|
| 1 | User Browser | Web Server | Username + Password | HTTPS POST |
| 2 | Web Server | Auth Server | Sanitized credentials | Internal HTTP |
| 3 | Auth Server | Database | DB Query (parameterized) | PostgreSQL |
| 4 | Database | Auth Server | User record / hash | PostgreSQL |
| 5 | Auth Server | Web Server | Session token / error | Internal HTTP |
| 6 | Web Server | User Browser | Session cookie / response | HTTPS |

---

## External Dependencies

| Dependency | Risk |
|------------|------|
| Third-party login libraries | Supply chain attack risk |
| Cloud hosting provider | Infrastructure failure or breach |
| TLS certificate authority | Certificate revocation risk |
| Email provider (for password reset) | Account takeover via email compromise |
