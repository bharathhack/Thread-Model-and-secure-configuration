# Threat Model and Secure Configuration
## Cybersecurity Internship – Task 2

**App modelled:** Simple Login Web Application  
**Method used:** STRIDE  
**Author:** [Your Name]  
**Date:** November 2026

---

## Project Overview

This project contains a complete threat model for a simple login web application. It includes:

- Threat model diagram (trust boundaries, data flows, actors)
- STRIDE-based threat identification
- Risk register with likelihood and impact ratings
- Prioritized hardening checklist

---

## Files in This Repository

| File | Description |
|------|-------------|
| `README.md` | Project overview (this file) |
| `threat-model-diagram.md` | Threat model diagram with trust boundaries |
| `risk-register.md` | Full risk register with STRIDE threats |
| `hardening-checklist.md` | Prioritized security hardening checklist |

---

## Application Description

The target application is a **simple web-based login system** with:
- A frontend login form (username + password)
- A backend authentication server
- A user database
- Session management via cookies

---

## Threat Modelling Method: STRIDE

| Letter | Threat Type | Description |
|--------|-------------|-------------|
| S | Spoofing | Attacker pretends to be someone else |
| T | Tampering | Unauthorized modification of data |
| R | Repudiation | Denying an action was performed |
| I | Information Disclosure | Exposing data to unauthorized parties |
| D | Denial of Service | Making the system unavailable |
| E | Elevation of Privilege | Gaining unauthorized access/permissions |
