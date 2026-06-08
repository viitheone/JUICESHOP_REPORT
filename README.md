# OWASP Juice Shop — Vulnerability Assessment Report

A manual black-box penetration test conducted against **OWASP Juice Shop v17.x**, deployed locally via Docker on Ubuntu Linux. This report documents **5 confirmed vulnerabilities** — 2 Critical and 3 High severity — including exact payloads, affected endpoints, business impact analysis, and remediation recommendations.

---

## Engagement Overview

| Field | Details |
|---|---|
| **Target** | OWASP Juice Shop v17.x |
| **Environment** | Ubuntu Linux — `localhost:3000` (Docker) |
| **Test Method** | Manual Black-Box Penetration Testing |
| **Date** | March 25, 2026 |
| **Total Findings** | 5 (2 Critical, 3 High) |
| **Tools Used** | Burp Suite, curl, browser DevTools |

---

## Vulnerability Summary

| # | Vulnerability | OWASP Category | Severity |
|---|---|---|---|
| 01 | SQL Injection — Login Bypass | A03:2021 – Injection | 🔴 CRITICAL |
| 02 | Reflected XSS — Search Field | A03:2021 – Injection (XSS) | 🟠 HIGH |
| 03 | Broken Access Control — Exposed Admin Panel | A01:2021 – Broken Access Control | 🔴 CRITICAL |
| 04 | Sensitive Data Exposure — Unprotected API Endpoints | A02:2021 – Cryptographic Failures | 🟠 HIGH |
| 05 | IDOR — User Basket Access | A01:2021 – Broken Access Control | 🟠 HIGH |

---

## Findings at a Glance

### 01. SQL Injection — Login Bypass `CRITICAL`
- **Location:** `/login` — Email field
- **Payload:** `' OR 1=1--`
- **Impact:** Complete authentication bypass; attacker gains admin-level access without valid credentials.

### 02. Reflected XSS — Search Field `HIGH`
- **Location:** Search bar — `/?q=`
- **Payload:** `<iframe src="javascript:alert('xss')">`
- **Impact:** Arbitrary JavaScript execution in victim's browser; enables session hijacking, credential theft, and UI redressing.

### 03. Broken Access Control — Exposed Admin Panel `CRITICAL`
- **Location:** `/#/administration`
- **Payload:** Direct URL navigation — no server-side privilege check enforced
- **Impact:** Unauthenticated access to all registered user data, feedback, and admin controls.

### 04. Sensitive Data Exposure — Unprotected API Endpoints `HIGH`
- **Location:** `/api/Users`, `/api/Products`, `/rest/products/search`
- **Payload:** `curl http://localhost:3000/api/Products` with Bearer token or direct URL access
- **Impact:** Full user PII and product data exposed without proper authorization enforcement on API layer.

### 05. IDOR — User Basket Access `HIGH`
- **Location:** `/rest/basket/{id}`
- **Payload:** `curl /rest/basket/1`, `/rest/basket/2`, `/rest/basket/3` with Bearer token
- **Impact:** Horizontal privilege escalation; any authenticated user can access or enumerate other users' basket data by incrementing the object ID.

---

## Setup & Reproduction

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y docker.io curl
sudo systemctl start docker
sudo docker pull bkimminich/juice-shop
sudo docker run -d -p 3000:3000 bkimminich/juice-shop
```

Application accessible at `http://localhost:3000`

---

## Report

The full report with screenshots, timestamps, and remediation recommendations is available as a PDF:

📄 [`juiceshop.pdf`](./juiceshop.pdf)

---

## References

- [OWASP Top 10 (2021)](https://owasp.org/Top10/)
- [OWASP Juice Shop Project](https://owasp.org/www-project-juice-shop/)
- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)

---

## Disclaimer

This assessment was conducted in a **controlled, local environment** against a deliberately vulnerable application designed for security training purposes. All findings are for **educational use only**. Do not attempt to reproduce these techniques against any system without explicit written authorization.

---
