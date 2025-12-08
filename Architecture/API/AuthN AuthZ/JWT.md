
JWT is a compact, self-contained token (`header.payload.signature`) for securely transmitting claims between parties, enabling stateless API authentication without server lookups.[perforce+1](https://www.perforce.com/blog/aka/what-is-jwt)​

---

## **JWT – Senior Architect Summary**

## **Structure (Memorize: 3 Parts → xxxxx.yyyyy.zzzzz)**

|Part|Content|Base64 Encoded|
|---|---|---|
|**Header**|`{"alg": "RS256", "typ": "JWT"}`|`eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9`|
|**Payload**|Claims: `sub`, `exp`, `iss`, `aud`, `iat`|`eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiZXhwIjoxNzMxNjI4ODAwfQ`|
|**Signature**|`HMACSHA256(header.payload, secret)`|`SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c`|

## **Claims Types (Must-Know 7)**

|Claim|Meaning|Required?|
|---|---|---|
|`sub`|Subject (user ID)|OAuth|
|`exp`|Expiration time|**ALWAYS**|
|`iss`|Issuer|**ALWAYS**|
|`aud`|Audience (API)|**ALWAYS**|
|`iat`|Issued at|Recommended|
|`nbf`|Not before|Optional|
|`jti`|JWT ID (anti-replay)|Recommended|

## **Algorithms (Security Priority)**

text

`✅ RS256/PS256 = Asymmetric (private sign, public verify) → PRODUCTION ✅ HS256 = Symmetric (shared secret) → INTERNAL ONLY ❌ NONE = Unsigned → NEVER ❌ Weak algos (HS384+) → AVOID`

---

## **Interview Checklist – JWT Mastery**

**✅ Structure & Flow**

-  3 parts: Header.Payload.Signature (Base64Url encoded)
    
-  Validation flow: signature → claims → expiry → audience
    
-  Stateless = no DB lookup, just crypto validation
    

**✅ Security Hardening**

-  **MUST** validate: sig + exp + iss + aud + nbf
    
-  RS256 > HS256 (asymmetric preferred)
    
-  Short expiry (15min) + refresh rotation
    
-  No sensitive data in payload (readable!)
    

**✅ Architecture Patterns**

-  OAuth Bearer tokens (access_token = JWT)
    
-  Distributed systems (stateless validation)
    
-  Mobile/SPA (local validation, secure storage)
    

**✅ Pitfalls & Monitoring**

-  "None" algo attacks
    
-  Clock skew (exp/nbf ±2min)
    
-  Audience mismatch exploits
    
-  Monitor: invalid sigs, expiry rates
    

---

## **60-Second Recap – "Must-Say" Bullets**

text

`"JWT = Header.Payload.Signature (Base64Url encoded) Header: alg=RS256, typ=JWT Payload: sub/exp/iss/aud/iat claims → NO secrets! Sig: HMACSHA256(header.payload, key) Validation: 1) Sig → 2) Claims → 3) Expiry → 4) Audience Security: RS256(prod), short expiry(15min), refresh rotation Use case: OAuth Bearer tokens, stateless API auth Pitfalls: NONE algo, clock skew, aud mismatch, monitor invalid sigs"`

**Reference**: [Perforce - What Is JWT?](https://www.perforce.com/blog/aka/what-is-jwt) + [jwt.io](https://jwt.io/introduction)[jwt+1](https://jwt.io/introduction)​

**Senior architect gold: crypto details, pitfalls, monitoring, positioning.** 🚀

1. [https://www.perforce.com/blog/aka/what-is-jwt](https://www.perforce.com/blog/aka/what-is-jwt)
2. [https://jwt.io/introduction](https://jwt.io/introduction)
3. [https://auth0.com/docs/secure/tokens/json-web-tokens/json-web-token-structure](https://auth0.com/docs/secure/tokens/json-web-tokens/json-web-token-structure)
4. [https://fusionauth.io/articles/tokens/jwt-components-explained](https://fusionauth.io/articles/tokens/jwt-components-explained)
5. [https://www.geeksforgeeks.org/web-tech/json-web-token-jwt/](https://www.geeksforgeeks.org/web-tech/json-web-token-jwt/)
6. [https://www.scalekit.com/blog/json-web-tokens-guide-for-developers](https://www.scalekit.com/blog/json-web-tokens-guide-for-developers)
7. [https://supertokens.com/blog/what-is-jwt](https://supertokens.com/blog/what-is-jwt)
8. [https://dev.to/jaypmedia/jwt-explained-in-4-minutes-with-visuals-g3n](https://dev.to/jaypmedia/jwt-explained-in-4-minutes-with-visuals-g3n)
9. [https://stytch.com/blog/rfc-7519-jwt-part-1/](https://stytch.com/blog/rfc-7519-jwt-part-1/)
10. [https://newsletter.systemdesign.one/p/how-jwt-works](https://newsletter.systemdesign.one/p/how-jwt-works)
11. [https://frontegg.com/guides/jwt-authorization](https://frontegg.com/guides/jwt-authorization)