# Security

### 1. Authentication & Access Control
Security measures that validate identity and control access.
- Authentication
- Multi-factor Authentication (MFA)
- Single Sign-On (SSO)
- Authorization
- Federated RBAC (Role-Based Access Control)
- Access Logging
- Broken Authentication
- Session Management
- Account Lockout Mechanisms

### 2. Data Protection
Protecting data at rest and in transit using cryptographic methods.
- Data Encryption
- TLS (Transport Layer Security)
- SSL (Secure Sockets Layer) ❗ (Legacy, replaced by TLS)
- Hashing and Salts
- Information Privacy
- Tokenization
- Key Management

### 3. Input Validation & Injection Attacks
Securing applications against malicious input.
- Input Validation and Sanitization
- SQL Injection
- Command Injection
- Cross-site Scripting (XSS)
- Cross-site Request Forgery (CSRF)
- Path Traversal 
- XML External Entity (XXE) Attacks 
- Deserialization Vulnerabilities 

### 4. Web & Browser Security
Specific to web application/browser interaction security.
- XSS (Cross-site Scripting)
- CSRF (Cross-site Request Forgery)
- CSP (Content Security Policy) 
- SameSite Cookies 
- Secure Headers (HSTS, X-Frame-Options, etc.) 

### 5. Secure Configuration & Maintenance
Preventing misconfigurations and maintaining secure systems.
- Insecure Configuration
- Patch Management
- Regular Security Audits and Updates
- Security Hardening 
- Dependency Scanning / Vulnerability Scanning 
- Secrets Management (e.g., API keys, credentials) 

### 6. Monitoring & Incident Response
Visibility, logging, and alerts to detect and respond to threats.
- Logging and Monitoring
- Access Logging (Already included in group 1, but overlaps here too)
- Intrusion Detection and Prevention Systems (IDS/IPS) 
- Security Information and Event Management (SIEM) 
- Incident Response Planning