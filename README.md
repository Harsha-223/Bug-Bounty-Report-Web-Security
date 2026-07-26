# Bug Bounty Report - Web Security Assignment

**Course:** IE2062 - Web Security Assignment  
**Assignment:** Bug Bounty Report  
**Author:** D.M.H.W Dissanayake  
**Group:** Y2S2.WE.CS.01  

---

## 🎯 Assignment Overview

This report documents security vulnerabilities identified during an authorized bug bounty assessment on multiple target domains. The assessment covered security misconfigurations, unauthenticated access, reflected XSS, and web application security weaknesses.

---

## 🔍 Targets Assessed

- banfield.us
- docuflow.stg.inspectorio.com
- personal.trexcloud.com

---

## 🛠️ Tools Used

- Nmap - Port scanning and service enumeration
- Nikto - Web server vulnerability scanning
- curl - Manual header and endpoint verification
- nslookup - DNS and IP information gathering
- ping - Host reachability verification
- OpenSSL - TLS/SSL testing
- Amass - Subdomain enumeration

---

## 🚨 Vulnerabilities Identified

### 1. Security Misconfigurations (banfield.us)

**Missing HTTP Security Headers:**
- X-Frame-Options - Missing (clickjacking risk)
- Strict-Transport-Security (HSTS) - Missing (TLS downgrade risk)
- X-Content-Type-Options - Missing (MIME sniffing risk)

**Potential Backup File Exposure:**
- Files like /backup.tar, /database.sql, /banfield.us.pem returned HTTP 200 OK
- Could expose sensitive database dumps, certificates, or source code

**Severity:** Medium to High

---

### 2. Unauthenticated Access to Sensitive Configuration File (docuflow.stg.inspectorio.com)

**Vulnerability:** /config.json publicly accessible without authentication

**Exposed Sensitive Data:**
- API endpoints
- Authentication server URLs
- Sentry DSN key
- Zendesk integration keys
- Environment flag (staging)

**Impact:**
- Reconnaissance for targeted attacks
- Service abuse using exposed keys
- Potential SSRF attacks
- Increased risk due to staging environment

**Severity:** High (CVSS 7.5)

---

### 3. Reflected XSS (banfield.us)

**Vulnerability:** Reflected Cross-Site Scripting in q parameter

**Endpoint:** http://banfield.us/search?q=<script>alert(1)</script>

**Description:** User input reflected without sanitization, allowing arbitrary JavaScript execution

**Impact:**
- Session hijacking
- Phishing and social engineering
- DOM manipulation
- Reputation damage

**Severity:** Medium-High

---

### 4. Web Application Security Misconfigurations (personal.trexcloud.com)

**Missing Secure and HttpOnly Flags on Cookies:**
- Cookies (sid, xids, autolaunch_triggered, activate_ca_modal_triggered) missing Secure and HttpOnly flags
- Allows session hijacking via XSS
- **Severity:** High

**Missing X-Frame-Options Header:**
- Enables clickjacking attacks
- **Severity:** Medium

**Missing X-Content-Type-Options Header:**
- Allows MIME sniffing attacks
- **Severity:** Low-Medium

**Weak Cipher Suites Enabled:**
- TLS_RSA_* ciphers without forward secrecy
- If private key compromised, past traffic can be decrypted
- **Severity:** Medium

**CORS Misconfiguration:**
- Access-Control-Allow-Origin: * allows any domain to access resources
- **Severity:** Medium

**Subdomain Enumeration Exposure:**
- Numerous publicly available subdomains increase attack surface
- **Severity:** Informational to Medium

**Server Banner Disclosure:**
- Reveals server software details aiding attackers
- **Severity:** Low

---

## 📋 Remediation Recommendations

### Security Headers
- Add X-Frame-Options: SAMEORIGIN
- Add Strict-Transport-Security (HSTS)
- Add X-Content-Type-Options: nosniff

### Cookies
- Set Secure flag
- Set HttpOnly flag
- Set SameSite=Strict

### Configuration Files
- Restrict /config.json behind authentication
- Move sensitive files out of web root
- Use server-side environment variables for sensitive keys

### TLS/SSL
- Disable RSA key exchange ciphers
- Enable forward secrecy (ECDHE_RSA only)

### CORS
- Restrict Access-Control-Allow-Origin to trusted domains only

### General
- Implement Content Security Policy (CSP)
- Mask server headers
- Minimize public subdomain enumeration
- Input validation and output encoding for XSS prevention

---

## 💡 Key Learnings

- Automated tools combined with manual verification effectively identify security misconfigurations
- Sensitive configuration files can serve as stepping stones for more severe attacks
- Missing security headers are common but easily fixed with proper configuration
- XSS vulnerabilities indicate broader input/output handling weaknesses
- Real-world applications often have configuration weaknesses in server hardening

---

## ⚠️ Challenges Faced

- Limited access to screenshots due to disclosure guidelines
- Interpreting automated scanning results and filtering false positives
- Formulating specific, realistic remediation suggestions
- Determining full impact requires additional contextual information

---

## 📁 Repository Structure
