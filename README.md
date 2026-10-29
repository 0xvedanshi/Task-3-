# Task-3-
# Web Application Security & Vulnerability Remediation 

## Overview
The primary objective of this project is to identify, exploit, and remediate critical OWASP Top 10 web application vulnerabilities in a controlled lab environment using DVWA (Damn Vulnerable Web Application) and custom Apache server configurations.

---

## Technical Stack & Tools Used
* **Operating System:** Kali Linux
* **Target Application:** DVWA (Damn Vulnerable Web Application)
* **Web Server & Database:** Apache2, MariaDB/MySQL, PHP
* **Security & Interception Tools:** Burp Suite Community Edition, Browser Developer Tools
* **External Security Scanners:** SecurityHeaders.com

---

## Vulnerability Scenarios & Remediation

### 1. SQL Injection (SQLi)
* **Exploitation:** Executed boolean-based and UNION-based SQL injections on the DVWA `SQL Injection` page (Low Security) to extract database tables, usernames, and password hashes.
* **Payload:** `' OR '1'='1` / `' UNION SELECT null, concat(user,':',password) FROM users-- -`
* **Mitigation:** Implemented Prepared Statements using PHP Data Objects (PDO) and parameterized queries to separate executable SQL logic from user inputs.

### 2. Cross-Site Scripting (XSS)
* **Stored XSS:** Injected a persistent script into the guestbook input field that executes whenever the page is loaded by any user.
  * **Payload:** `<script>alert('Stored XSS')</script>`
* **Reflected XSS:** Exploited unvalidated URL query parameters to execute arbitrary client-side JavaScript.
  * **Payload:** `http://localhost/dvwa/vulnerabilities/xss_r/?name=<script>alert('Reflected XSS')</script>`
* **Mitigation:** Applied context-aware output encoding using `htmlspecialchars()` in PHP and configured strict Content Security Policy (CSP) headers.

### 3. Cross-Site Request Forgery (CSRF)
* **Exploitation:** Crafted a malicious HTML form / URL payload that tricks an authenticated session into making an unauthorized password change request.
* **Mitigation:** Enforced unique, cryptographically secure anti-CSRF tokens (`user_token`) validation for all state-changing HTTP POST requests.

### 4. File Inclusion Attacks (LFI & RFI)
* **Local File Inclusion (LFI):** Manipulated directory traversal parameters to read sensitive system files (`/etc/passwd`).
  * **Payload:** `?page=../../../../etc/passwd`
* **Remote File Inclusion (RFI):** Passed external URLs to fetch and execute remote web shell scripts on the target server.
* **Mitigation:** Disabled `allow_url_include` in `php.ini` and implemented strict input whitelisting for requested filenames.

### 5. Advanced Burp Suite Operations
* **Request Interception:** Intercepted and modified raw HTTP POST login requests using Burp Suite Proxy to manipulate client-side logic.
* **Automated Fuzzing:** Used Burp Suite Intruder to perform dictionary-based brute-force attacks on authentication endpoints and analyzed HTTP response length and status codes.

### 6. Web Security Headers & Server Hardening
* **Security Audit:** Conducted a security header assessment using `securityheaders.com` to identify missing defensive headers.
* **Server Configuration:** Hardened the Apache web server by adding missing headers in `/etc/apache2/apache2.conf`:
  ```apache
  Header set X-Frame-Options "SAMEORIGIN"
  Header set X-Content-Type-Options "nosniff"
  Header set X-XSS-Protection "1; mode=block"
  Header set Content-Security-Policy "default-src 'self';"
  Header set Strict-Transport-Security "max-age=31536000; includeSubDomains"
