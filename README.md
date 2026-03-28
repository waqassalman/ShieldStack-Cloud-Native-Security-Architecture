# ShieldStack — Cloud-Native Security Architecture

A reference implementation of a production-grade security architecture for a multi-tier e-commerce platform. Covers network segmentation, threat modeling, authentication design, and compliance mapping.

---

## Architecture

Three-tier segmented network with enforced firewall boundaries and zero-trust inter-zone communication.

```
Internet → WAF → DMZ (10.0.1.0/24) → Perimeter Firewall → App Tier (10.0.2.0/24) → Data Tier (10.0.3.0/24)
                                                                                   → Mgmt Zone (10.0.4.0/24)
```

| Zone | Purpose | Controls |
|------|---------|----------|
| DMZ | Public traffic termination | WAF, TLS 1.3, rate limiting |
| App Tier | Business logic & API | MFA, RBAC, JWT validation |
| Data Tier | PII & transactions | AES-256 at rest, parameterized queries |
| Management | Audit & admin access | Tamper-evident logs, privileged access controls |

---

## Security Controls

- **Authentication** — Short-lived JWTs (15 min expiry), refresh token rotation, HttpOnly cookies, MFA enforced for privileged accounts
- **Network** — Default-deny firewall rules, VPN gateways at zone boundaries, WAF with OWASP ruleset
- **Data Protection** — AES-256 at rest, TLS 1.3 in transit, payment tokenization (no raw card storage)
- **Audit** — Append-only audit log isolated from application processes, write-once access model

---

## Threat Model (STRIDE)

| Threat | Likelihood | Impact | Score | Control |
|--------|-----------|--------|-------|---------|
| Information Disclosure (Data Breach) | 4 | 5 | 20 | Encryption, DLP monitoring |
| Tampering (SQL Injection) | 4 | 4 | 16 | Parameterized queries, WAF |
| Denial of Service (DDoS) | 3 | 4 | 12 | Cloud scrubbing, SYN cookies |
| Elevation of Privilege (Session Hijack) | 2 | 5 | 10 | MFA, short-lived JWTs |
| Spoofing (Credential Stuffing) | 3 | 3 | 9 | Lockout, CAPTCHA, HaveIBeenPwned API |

---

## Architecture Decision Records

| ADR | Decision | Status |
|-----|----------|--------|
| [ADR-001](docs/adr_001_jwt_authentication.md) | JWT auth with short-lived tokens & refresh rotation | Accepted |
| [ADR-002](docs/adr_002_network_segmentation.md) | Network segmentation via DMZ and tiered zones | Accepted |

---

## Compliance

| Standard | Requirement | Status |
|----------|-------------|--------|
| PCI-DSS Req. 1 | Network segmentation | ✅ |
| PCI-DSS Req. 3 | Encrypted storage, tokenization | ✅ |
| PCI-DSS Req. 4 | TLS 1.3 in transit | ✅ |
| PCI-DSS Req. 7 | Least privilege / RBAC | ✅ |
| PCI-DSS Req. 10 | Tamper-evident audit logging | ✅ |
| NIST SP 800-63B | MFA, short-lived credentials | ✅ |

---
 
## Diagrams
 
| Diagram | Description |
|---------|-------------|
| [`Network_Architecture_Diagram.png`](/Network Architecture Diagram.png) | Full network topology with zones, firewalls, and gateways |
| [`Data_Flow_Diagram.png`](/Data%20Flow%20Diagram.png) | Data flow across customer, web, app, database, and payment processor |
 
---

## Stack

`Network Security` `STRIDE` `PCI-DSS` `Zero Trust` `JWT` `AES-256` `TLS 1.3` `WAF` `RBAC` `Threat Modeling`
