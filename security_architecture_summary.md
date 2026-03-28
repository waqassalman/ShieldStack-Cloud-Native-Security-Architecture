# SecureShop Inc. Security Architecture Summary

## Overview
SecureShop is a three-tier e-commerce platform (DMZ → App → Data) with firewall-enforced boundaries between each zone. No external actor can reach the App Server or Database directly.

## Key Security Principles Applied
1. **Defence in Depth** - Multiple layers contain any single breach.
2. **Least Privilege** - Tiers, accounts, and roles access only what they need.
3. **Zero Trust at Boundaries** - Default deny; all inter-tier traffic is explicitly permitted.

## Network Zones

| Zone | Purpose | Key Controls |
|------|---------|--------------|
| DMZ | Terminates public traffic | WAF, TLS 1.3, rate limiting |
| App Tier | Business logic | RBAC, JWT validation |
| Data Tier | PII and transactions | AES-256 at rest, parameterized queries |
| Management | Audit and admin | Tamper-evident logs, MFA |

## Critical Security Controls
1. Network segmentation with default-deny firewall rules
2. JWT authentication — 15-min expiry, refresh rotation, MFA for admins
3. TLS 1.3 in transit; AES-256 at rest
4. WAF + parameterized queries against injection attacks
5. Append-only Audit Log isolated from application processes

## Compliance Mapping

| Requirement | Control | Status |
|-------------|---------|--------|
| PCI-DSS Req. 1 - Network Controls | DMZ segmentation | Implemented |
| PCI-DSS Req. 3 - Stored Data | AES-256, tokenization | Implemented |
| PCI-DSS Req. 4 - Data in Transit | TLS 1.3 | Implemented |
| PCI-DSS Req. 7 - Least Privilege | RBAC, SQL GRANT restrictions | Implemented |
| PCI-DSS Req. 10 - Audit Logging | Tamper-evident Audit Log | Implemented |

## Diagram Reference
`Network Architecture Diagram.png`

## Threat Model Reference
`threat_mitigations.md`