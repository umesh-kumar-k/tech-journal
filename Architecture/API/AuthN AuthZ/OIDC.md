OIDC is an **identity layer on OAuth 2.0** that adds `ID tokens` (JWTs) for user authentication + standard claims, enabling SSO across apps with one login.[auth0](https://auth0.com/docs/authenticate/protocols/openid-connect-protocol)​

---

## **OIDC – Senior Architect Summary**

## **OIDC vs OAuth (Core Distinction)**

|Protocol|Purpose|Tokens|Claims|
|---|---|---|---|
|**OAuth 2.0**|**Authorization**|Access Token|Scopes (read/write)|
|**OIDC**|**Authentication**|**ID Token + Access**|User profile (name/email)|

## **OIDC Tokens (Memorize These 2)**

|Token|JWT?|Purpose|Claims|
|---|---|---|---|
|**ID Token**|✅|**User identity**|`sub`, `name`, `email`, `picture`|
|**Access Token**|Maybe|API access|OAuth scopes|

## **OIDC Standard Claims (Must-Know 7)**

text

`sub (user ID), name, email, picture,  given_name, family_name, locale`

## **OIDC Flow (Auth Code + PKCE)**

text

`1. Client → /authorize (scope=openid profile email) 2. User login + consent → Authorization Code 3. Client → /token → ID Token + Access Token 4. Validate ID Token → User authenticated!`

---

## **Interview Checklist – OIDC Mastery**

**✅ Core Concepts**

-  "OIDC = OAuth 2.0 + ID tokens for authentication"
    
-  ID Token (identity) vs Access Token (authorization)
    
-  `scope=openid` triggers OIDC flow
    

**✅ Token Validation**

-  ID Token = JWT: validate sig/exp/iss/aud
    
-  Standard claims: sub/name/email/picture
    
-  OIDC Discovery: `/.well-known/openid_configuration`
    

**✅ Architecture Decisions**

-  SSO: one login → multiple apps (ID token reuse)
    
-  SPA/Mobile: Auth Code + PKCE flow
    
-  Enterprise: SAML → OIDC federation
    

**✅ Security/Scale**

-  PKCE mandatory for public clients
    
-  Nonce param (replay protection)
    
-  Dynamic client registration + discovery
    

---

## **60-Second Recap – "Must-Say" Bullets**

text

`"OIDC = OAuth 2.0 identity layer → ID Token (JWT) + Access Token OAuth = authorization (scopes), OIDC = authentication (user claims) Flow: scope=openid → Auth Code + PKCE → ID Token validation ID Token claims: sub/name/email/picture (standard set) Security: PKCE + nonce + OIDC discovery (.well-known) Positioning: SSO across apps, SPA/mobile auth, SAML replacement"`

**Reference**: [Auth0 - OpenID Connect Protocol](https://auth0.com/docs/authenticate/protocols/openid-connect-protocol)[auth0](https://auth0.com/docs/authenticate/protocols/openid-connect-protocol)​

**Perfect positioning: OAuth (authZ) vs OIDC (authN) + SSO use case.** 🚀

1. [https://auth0.com/docs/authenticate/protocols/openid-connect-protocol](https://auth0.com/docs/authenticate/protocols/openid-connect-protocol)