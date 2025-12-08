# **API Authentication & Authorization - Summary**

## **Core Concepts**
- **Authentication (AuthN):** Verifies *who* the client is. Establishes identity.
- **Authorization (AuthZ):** Determines *what permissions/actions* the authenticated identity has.
- **Golden Rule:** **AuthN happens before AuthZ.** Every request must be authenticated; authorization is evaluated per request/endpoint.

## **Key Authentication Methods (From Simple to Complex)**

### **1. API Keys**
- **What:** Simple token in header or query param.
- **Use Case:** Server-to-server, internal/machine APIs.
- **Pros:** Simple to implement/use.
- **Cons:** Poor security if leaked; no user identity; hard to rotate/revoke.
- **Best Practice:** Store hashed; never in URLs; use as *identification* not sole *authorization*.

### **2. HTTP Authentication**
- **Basic Auth:** `Authorization: Basic base64(user:pass)`. For legacy/internal use only. **Always with HTTPS.**
- **Bearer Token (JWT):** `Authorization: Bearer <token>`. Standard for OAuth2/OpenID. Token contains claims.

### **3. OAuth 2.0 & OpenID Connect (OIDC) - INDUSTRY STANDARD**
- **OAuth 2.0:** Authorization *framework* for delegated access. Grants tokens to act *on behalf of* user.
- **OIDC:** Identity layer *on top of OAuth 2.0*. Adds ID token (JWT) for authentication.
- **Flows (Grant Types):**
    - **Authorization Code (+ PKCE):** For web/mobile apps. Most secure for public clients.
    - **Client Credentials:** For machine-to-machine (service accounts).
    - **Resource Owner Password Credentials:** **Avoid** (legacy/direct username/password).
- **Tokens:**
    - **Access Token:** Short-lived, for API calls (minutes/hours).
    - **Refresh Token:** Long-lived, to get new access tokens (stored securely).
    - **ID Token (OIDC):** JWT with user info (authentication proof).

### **4. JWT (JSON Web Tokens)**
- **Structure:** Header.Payload.Signature. Contains claims (e.g., `sub`, `roles`, `exp`).
- **Key Point:** Stateless (no DB lookup). Validate signature with public key.
- **Risk:** Cannot revoke before expiry. Mitigate with short expiry + refresh tokens.

### **5. Mutual TLS (mTLS)**
- **What:** Both client and server present X.509 certificates.
- **Use Case:** High-security environments (finance, microservices in service mesh).
- **Pros:** Strong cryptographic identity; no token management.
- **Cons:** Complex certificate lifecycle (issuance, rotation, revocation).

### **6. Other Notable Methods**
- **HMAC/Signed Requests:** Client signs request with secret (AWS Sigv4). Good for server-to-server.
- **API Gateway Patterns:** Offload AuthN/Z to gateway (Kong, AWS API Gateway). Centralized policy enforcement.

## **Architectural Patterns & Best Practices**
- **Zero Trust:** Never trust, always verify. Authenticate *every* request.
- **Principle of Least Privilege:** Tokens/scopes should grant minimal necessary permissions.
- **Token Storage:**
    - **Backend:** Access token in memory; refresh token in HTTP-only, secure cookie.
    - **Never store tokens in local storage** (XSS vulnerability).
- **Secrets Management:** Never hardcode API keys/secrets. Use vaults (Azure Key Vault, AWS Secrets Manager).
- **Federation & SSO:** Use OIDC/OAuth to delegate to enterprise identity providers (Azure AD, Okta).
- **API Gateway/Proxy:** Centralize AuthN, rate limiting, logging. Microservices receive validated user context (e.g., via `X-User-Id` header).

## **Common Pitfalls & Security Considerations**
1. **Broken Object Level Authorization (BOLA):** #1 API security risk (OWASP). Validate user can access *each* requested resource ID.
2. **Insufficient Token Expiry & Rotation:** Short-lived access tokens; long-lived refresh tokens with rotation.
3. **Missing Rate Limiting** on authentication endpoints (prevent credential stuffing).
4. **Exposing Sensitive Data in URLs:** API keys/tokens in query params get logged.
5. **Not Using HTTPS:** **Non-negotiable** for all authentication.

---

## **Interview Checklist**
- [ ] Can clearly differentiate **Authentication vs. Authorization**.
- [ ] Can explain **OAuth 2.0 flows** and when to use each (Auth Code PKCE vs. Client Credentials).
- [ ] Can articulate **JWT structure, benefits, and revocation challenges**.
- [ ] Understands **mTLS use cases and trade-offs** vs. token-based auth.
- [ ] Knows **secure token storage patterns** (backend vs. frontend).
- [ ] Can discuss **API Gateway role** in offloading/auth delegation.
- [ ] Aware of **OWASP API Security Top 10**, especially BOLA.
- [ ] Can design **least privilege scopes/claims** for fine-grained authorization.
- [ ] Understands **identity federation** (OIDC with enterprise IdP).
- [ ] Can articulate **zero trust principles** applied to APIs.

---

## **60-Second Recap (Bullet Points)**
- **AuthN = Identity, AuthZ = Permissions.** Always in that order, per request.
- **OAuth 2.0 + OIDC is the modern standard** for user and machine authentication.
- **Choose the right flow:** Authorization Code PKCE for apps, Client Credentials for services.
- **JWTs are stateless tokens with claims,** but revocation is hard—use short expiry.
- **Never store tokens in frontend local storage;** use secure, HTTP-only cookies.
- **Offload auth to an API Gateway** for centralized policy and microservice simplicity.
- **Implement least privilege** via scopes and claims; validate object-level access.
- **Mitigate OWASP Top 10:** Prevent BOLA, enforce rate limits, use HTTPS always.

---

**Reference Links:**
- [Frontegg: API Authentication vs. Authorization](https://frontegg.com/guides/api-authentication-api-authorization)
- [Zuplo: Top 7 API Authentication Methods](https://zuplo.com/learning-center/top-7-api-authentication-methods-compared)
- [Kong: Common API Authentication Methods](https://konghq.com/blog/engineering/common-api-authentication-methods)
- [Apideck: API Auth Methods & Examples](https://www.apideck.com/blog/api-auth-authentication-methods-and-examples)
- [Postman: API Authentication Guide](https://www.postman.com/api-platform/api-authentication/)

**Memorization Tip:** Think in **layers**: **Transport (HTTPS/mTLS) → Identity (OIDC/JWT) → Delegation (OAuth Flows) → Enforcement (Gateway/Scopes).**

You're right—it's too high-level for a senior architect interview. Here's an **expanded version** with the depth expected:

---

## API AuthN/AuthZ – **Senior Architect Depth** (interview-ready)

## **Core Methods + Decision Matrix**

|Method|Use Case|Pros|Cons|Security Posture|
|---|---|---|---|---|
|**API Key**|Internal tools, rate-limiting, simple 3rd-party|Simple, fast|Shared secret, no delegation, hard revocation|**Low** - TLS + rotate frequently|
|**Basic Auth**|Legacy/internal only|Universal support|Base64(not encryption), credentials everywhere|**Very Low** - TLS + short expiry only|
|**Bearer Token**|Stateless sessions (mobile/SPA)|Simple header, works everywhere|Opaque, needs validation endpoint|**Medium** - Short expiry + refresh|
|**OAuth 2.0**|Public APIs, delegated access|Scopes, delegation, mature ecosystem|Complex flows, state management|**High** - PKCE + token introspection|
|**OAuth 2.1**|Modern SPA/mobile|Fixes OAuth 2.0 (no implicit flow)|Still evolving adoption|**High** - PKCE mandatory|
|**OpenID Connect**|User identity + access|ID tokens + access tokens|Adds identity layer complexity|**High** - JWT ID tokens + discovery|
|**mTLS**|Zero-trust service mesh|Strongest client identity|Cert management overhead|**Very High** - Mutual auth|
|**JWT**|Distributed stateless auth|Self-contained, no DB lookup|Must validate EVERY claim|**High** - If properly validated|

## **OAuth Flows – Must Know**

text

`1. Authorization Code + PKCE → SPA/Mobile (replaces implicit) 2. Client Credentials → M2M service-to-service   3. Refresh Token → Long-lived sessions 4. Device Code → Smart TVs/IoT`

## **JWT Security Checklist (interview gold)**

text

`✅ Signature validation (RS256/PS256) ✅ Expiry (exp) + Not Before (nbf)  ✅ Issuer (iss) + Audience (aud) ✅ JWT ID (jti) for replay protection ✅ No sensitive data in payload ❌ Never store secrets in JWT`

---

## **Interview Checklist – Architect Depth**

**✅ Authentication Methods**

-  API Key: "Simple shared secret in header. Internal/low-risk only. Rotate + TLS"
    
-  OAuth flows: "Auth Code+PKCE → SPA/mobile, Client Creds → M2M, why no implicit flow?"
    
-  JWT pitfalls: "Signature/expiry/audience/issuer validation. No secrets in payload"
    
-  mTLS: "Zero-trust service mesh. Client cert validates identity before token"
    

**✅ Authorization Patterns**

-  RBAC vs ABAC: "Roles → simple/fast, Attributes → fine-grained"
    
-  Token introspection vs JWT validation: "Introspection → revocable, JWT → stateless"
    
-  Scopes vs Claims: "OAuth scopes → coarse, JWT claims → fine-grained"
    

**✅ Architecture Decisions**

-  External API → OAuth 2.1 + PKCE + scopes
    
-  Internal services → mTLS + short-lived JWTs
    
-  BFF/SPA → Auth Code flow + secure cookies
    
-  Scale → Centralized auth (Okta/Auth0) + gateway enforcement
    

**✅ Security Hardening**

-  Threat model per client type (public/internal/BFF)
    
-  Token rotation/revocation strategy
    
-  Rate limiting + anomaly detection
    
-  Compliance mapping (GDPR/PCI/SOC2)
    

---

## **60-Second Recap – Full Depth**

text

`"AuthN = WHO (API Key/OAuth/mTLS), AuthZ = WHAT (scopes/claims/RBAC) External APIs: OAuth 2.1 + PKCE + scopes + introspection Internal services: mTLS + short JWTs (validate sig/exp/iss/aud) BFF/SPA: Auth Code flow → secure cookies → backend-for-frontend tokens JWT pitfalls: ALWAYS validate signature + expiry + audience + issuer OAuth flows: AuthCode+PKCE (SPAs), ClientCreds (M2M), NO implicit flow Scale pattern: Auth0/Okta → API Gateway → uniform enforcement across services"`

**This covers 95% of senior architect API auth questions.** References same as before.

