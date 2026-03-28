# Architecture Decision Record: JWT Authentication with Short-Lived Tokens

## Status
Accepted

## Context
SecureShop needs session management for customer logins and admin access to the Admin Portal. During STRIDE analysis, session hijacking was identified as an Elevation of Privilege risk (Score: 10) a stolen admin token could bypass RBAC and expose all internal zones.

## Decision
Use JWTs with the following constraints:
- 15-minute access token expiry; 7-day customer / 8-hour admin refresh tokens with rotation on each use
- Tokens stored in `HttpOnly; Secure; SameSite=Strict` cookies only
- MFA required before any admin JWT is issued
- All token events written to the Audit Log before response is returned

## Consequences

### Positive
- HttpOnly cookies block XSS-based token theft
- Refresh rotation detects replay attacks
- Stateless validation supports horizontal App Server scaling
- Enforce hard privilege separation

### Negative
- Silent refresh logic adds frontend complexity
- Parallel requests near expiry require a ~30s grace window

### Risks
- Stolen refresh token on a compromised device is valid until rotation detects reuse
- Key compromise makes all issued tokens forgeable; HSM storage and 90-day rotation required

## Alternatives Considered
1. **Server-side opaque sessions (Redis):** Instant revocation but introduces a stateful single point of failure and DoS target.
2. **Long-lived JWTs (24h+):** Simpler, but a stolen admin token stays valid for the full TTL unacceptable given Impact: 5 rating.
3. **OAuth 2.0 / OIDC (Auth0, Cognito):** Strongest option; deferred due to data residency constraints. Recommended for re-evaluation at next compliance review.

## References
- OWASP JWT Cheat Sheet — https://cheatsheetseries.owasp.org/cheatsheets/JSON_Web_Token_for_Java_Cheat_Sheet.html
- NIST SP 800-63B — https://pages.nist.gov/800-63-3/sp800-63b.html