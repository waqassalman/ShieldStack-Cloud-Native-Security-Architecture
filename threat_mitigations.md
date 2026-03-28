# Threat Mitigation Plan
 
---
 
### Threat 1: Information Disclosure (Data Breach)
 
- **Description:** Sensitive customer PII (names, addresses, emails) and financial data (payment tokens, order history) stored in the database are exposed to unauthorized actors. This can occur via insecure direct object references, misconfigured access controls, or unencrypted data at rest/in transit.
 
- **Risk Score:** 20 (Critical) Likelihood: 4 × Impact: 5
 
- **Mitigation:**
  - Encrypt all sensitive data at rest using AES-256 and enforce TLS 1.3 for all data in transit.
  - Apply the principle of least privilege: application service accounts should only access the tables and columns they need.
  - Implement field-level encryption for PII columns (e.g., email, address) in the database.
  - Tokenize payment data so raw card numbers are never stored in the SecureShop system.
 
- **Security Control:**
  - **Preventive:** Database encryption at rest (AES-256), TLS 1.3 enforcement, column-level access controls statements.
  - **Detective:** Data Loss Prevention (DLP) monitoring, database activity monitoring (DAM) alerts on bulk data reads.
 
- **Residual Risk:** Low-Medium. Even with encryption, a compromised application-layer service account with valid credentials could still query and exfiltrate data. Continuous monitoring and anomaly detection reduce but cannot eliminate this risk.
 
---
 
### Threat 2: Tampering (SQL Injection)
 
- **Description:** An attacker injects malicious SQL code into an unsanitized input field (e.g., search bar, login form). The crafted query is passed to the database, allowing the attacker to read, modify, or delete records in the `users`, `orders`, or `products` tables without detection.
 
- **Risk Score:** 16 (High) Likelihood: 4 × Impact: 4
 
- **Mitigation:**
  - Use parameterized queries / prepared statements exclusively never concatenate user input into SQL strings.
  - Deploy a Web Application Firewall (WAF) with SQL injection rule sets to filter malicious payloads at the DMZ boundary.
  - Conduct regular static code analysis (SAST) and dynamic scanning (DAST) to detect injection vulnerabilities before deployment.
  - Apply input validation and output encoding across all user-facing fields.
 
- **Security Control:**
  - **Preventive:** Parameterized queries, WAF rules, ORM frameworks that abstract raw SQL.
  - **Detective:** Database query logging and anomaly detection (e.g., flagging `DROP`, `UNION SELECT`, `--` patterns in logs).
 
- **Residual Risk:** Low. Parameterized queries effectively eliminate classic SQL injection. Residual risk exists in legacy code paths or third-party integrations that may not enforce these controls consistently.
 
---
 
### Threat 3: Denial of Service (SYN Flood / DDoS)
 
- **Description:** An attacker overwhelms the Web Server with a high volume of malformed or incomplete TCP connection requests (SYN Flood), or floods it with application-layer HTTP requests, exhausting server resources and making the SecureShop storefront unavailable to legitimate customers.
 
- **Risk Score:** 12 (Medium) Likelihood: 3 × Impact: 4
 
- **Mitigation:**
  - Deploy a cloud-based DDoS protection service (e.g., Cloudflare, AWS Shield) upstream of the Web Server to absorb volumetric attacks before they reach the infrastructure.
  - Enable SYN cookies on the Web Server OS to handle incomplete connection storms without exhausting the TCP backlog.
  - Implement rate limiting and IP-based throttling at the WAF and load balancer layers.
  - Define and regularly test a DoS incident response runbook including escalation paths and failover procedures.
 
- **Security Control:**
  - **Preventive:** Cloud DDoS scrubbing, SYN cookies, rate limiting, auto-scaling policies.
  - **Detective:** Real-time traffic monitoring and alerting on abnormal request volumes (e.g., >X requests/sec per IP).
 
- **Residual Risk:** Medium. Sophisticated application-layer (Layer 7) DDoS attacks that mimic legitimate traffic are harder to filter and may still degrade performance even with mitigations in place.
 
---
 
### Threat 4: Elevation of Privilege (Admin Takeover / Session Hijacking)
 
- **Description:** An attacker steals or forges a management JWT token (e.g., via XSS, insecure storage, or token leakage in logs) and uses it to bypass RBAC controls, gaining administrative access to the Admin Portal, internal zones, or the database management interface.
 
- **Risk Score:** 10 (Medium) Likelihood: 2 × Impact: 5
 
- **Mitigation:**
  - Enforce short JWT expiration windows (e.g., 15 minutes) with secure refresh token rotation.
  - Store tokens in HttpOnly, Secure, SameSite=Strict cookies to prevent JavaScript-based theft.
  - Implement Multi-Factor Authentication (MFA) for all admin and management accounts.
  - Apply strict RBAC with the principle of least privilege — no admin account should have broader permissions than required for its role.
  - Log and alert on privilege escalation events and anomalous admin actions.
 
- **Security Control:**
  - **Preventive:** MFA, short-lived JWTs, HttpOnly cookies, RBAC enforcement.
  - **Detective:** SIEM alerting on unusual admin logins (off-hours, new IP), privilege change audit logs in tamper-evident Audit Log system.
 
- **Residual Risk:** Low-Medium. MFA significantly raises the bar, but insider threats or a fully compromised admin workstation remain difficult to prevent purely through technical controls.
 
---
 
### Threat 5: Spoofing (Credential Stuffing)
 
- **Description:** An attacker uses large datasets of previously leaked username/password pairs (from other breaches) to systematically attempt login to SecureShop customer accounts. Because many users reuse passwords, a percentage of attempts will succeed, granting unauthorized access to customer orders, saved payment methods, and personal data.
 
- **Risk Score:** 9 (Low) — Likelihood: 3 × Impact: 3
 
- **Mitigation:**
  - Integrate with breach credential databases (e.g., Have I Been Pwned API) to block or prompt password resets for accounts with known-compromised credentials.
  - Enforce account lockout or CAPTCHA challenges after a defined number of failed login attempts per account or IP.
  - Implement MFA (e.g., TOTP, email OTP) as an optional or mandatory step for customer accounts.
  - Monitor for distributed login attempts across many IPs targeting the same accounts.
 
- **Security Control:**
  - **Preventive:** CAPTCHA, account lockout policies, breached-password checks at registration and login, MFA.
  - **Detective:** WAF rules and SIEM alerts on high-volume failed authentication events; anomalous geographic login detection.
 
- **Residual Risk:** Low. Credential stuffing is a well-understood attack and layered controls (lockout + CAPTCHA + MFA) make large-scale automated attacks impractical. Residual risk targets users who do not enable MFA.