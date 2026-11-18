# Web Application Penetration Testing Report

**Date:** 18-11-2026  
**Penetration Tester:** Krishan Kumar  
**Task:** Week 6 - Task 2 (Web Application Penetration Testing)  
**Target Scope:** `http://localhost:3000` / `http://vulnweb.com` (Authorized Lab Environment)  
**Methodology:** OWASP Web Security Testing Guide (WSTG)  

---

## 1. Executive Summary
A web application penetration test was executed against the target web application to evaluate its resilience against real-world attacks. Using **Burp Suite** for manual request manipulation and **OWASP ZAP** for reconnaissance, **four exploitable vulnerabilities** were identified: **Union-Based SQL Injection**, **Stored Cross-Site Scripting (XSS)**, **Insecure Direct Object Reference (IDOR)**, and **Cross-Site Request Forgery (CSRF)**. Immediate code-level fixes are recommended to protect user accounts and backend database integrity.

---

## 2. Penetration Testing Phases
1. **Information Gathering & Mapping:** Spidered application endpoints, analyzed HTTP response headers, and mapped input parameters using OWASP ZAP and Burp Target Site Map.
2. **Vulnerability Discovery:** Tested authentication forms, search fields, profile update endpoints, and API parameters for injection and access control flaws.
3. **Exploitation (PoC Validation):** Used **Burp Suite Repeater** and **Intruder** to confirm exploitability and capture reproducible Proof-of-Concept evidence.
4. **Reporting & Remediation:** Classified findings by business impact and mapped each flaw to secure coding fixes.

---

## 3. Summary of Exploited Findings

| Finding ID | Vulnerability | OWASP Top 10 (2021) | Severity | Target Endpoint |
| :--- | :--- | :--- | :--- | :--- |
| **PT-01** | Union-Based SQL Injection | A03:2021 - Injection | **Critical** | `GET /api/products?id=` |
| **PT-02** | Stored Cross-Site Scripting (XSS) | A03:2021 - Injection | **High** | `POST /api/reviews/submit` |
| **PT-03** | Insecure Direct Object Reference (IDOR) | A01:2021 - Broken Access Control | **High** | `GET /api/users/profile?uid=` |
| **PT-04** | Cross-Site Request Forgery (CSRF) | A01:2021 - Broken Access Control | **Medium** | `POST /account/update-email` |

---

## 4. Recommended Remediation Strategies
1. **Parameterized Queries (SQLi):** Replace dynamic SQL string concatenation with Prepared Statements across all database access layers.
2. **Context-Aware Output Encoding & CSP (XSS):** Sanitize user-generated content before storage, HTML-encode all outputs upon rendering, and enforce a strict `Content-Security-Policy` header.
3. **Server-Side Authorization Checks (IDOR):** Validate on every API request that the authenticated session ID matches the requested resource owner ID instead of relying on client-supplied parameters.
4. **Anti-CSRF Tokens & SameSite Cookies (CSRF):** Require unpredictable, per-session CSRF tokens on all state-changing HTTP requests and set session cookies to `SameSite=Strict`.
