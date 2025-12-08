OAuth 2.0 is an **authorization framework** (not authentication) for delegated API access via scopes/consent, replacing password anti-pattern with short-lived tokens.[developer.okta](https://developer.okta.com/blog/2017/06/21/what-the-heck-is-oauth)​

---

## **OAuth 2.0**

## **Core Components (Memorize These 6)**

|Component|Role|Key Detail|
|---|---|---|
|**Resource Owner**|Data owner (user/company)|Gives consent|
|**Client**|App requesting access|Public (browser/mobile) vs Confidential (server)|
|**Authorization Server**|Issues tokens|`/authorize` + `/token` endpoints|
|**Resource Server**|API with protected data|Validates access tokens|
|**Access Token**|Short-lived API key|Hours/minutes, opaque or JWT|
|**Refresh Token**|Long-lived renewal|Days/months, revocable|

## **OAuth Flows (Must-Know 5)**

|Flow|Use Case|Channel|Security|
|---|---|---|---|
|**Auth Code + PKCE**|SPA/Mobile **GOLD STANDARD**|Front + Back|Highest security|
|**Implicit**|Legacy browser-only **DEPRECATED**|Front only|Vulnerable|
|**Client Credentials**|M2M service-to-service|Back only|Service accounts|
|**Resource Owner Password**|Legacy desktop **AVOID**|Back only|Password anti-pattern|
|**Device Code**|Smart TV/IoT/CLI|Back + polling|No browser|

## **Security Checklist**

text

`✅ State param = CSRF protection ✅ Whitelist redirect_uris   ✅ PKCE for public clients ✅ HTTPS everywhere ✅ Validate client_id binding ❌ Never: client_secret in mobile/SPA`

---

## **Interview Checklist – OAuth Mastery**

**✅ Core Concepts**

-  "OAuth = authorization framework, NOT authentication"
    
-  Actors: Resource Owner → Client → Auth Server → Resource Server
    
-  Scopes + Consent = explicit user authorization
    
-  Access (short) vs Refresh (long, revocable) tokens
    

**✅ Flow Selection**

-  Auth Code + PKCE = modern SPA/mobile standard
    
-  Client Credentials = M2M service accounts
    
-  Why Implicit flow is dead (security risks)
    
-  Device flow for browserless clients
    

**✅ Architecture Decisions**

-  Public vs Confidential clients + registration
    
-  Front channel (browser) vs Back channel (server)
    
-  OIDC extends OAuth for authentication (ID tokens)
    

**✅ Security/Scale**

-  State/CSRF + redirect_uri validation
    
-  Token revocation via refresh token kill
    
-  Enterprise: scopes → fine-grained policy
    

---

## **60-Second Recap – "Must-Say" Bullets**

text

`"OAuth 2.0 = authorization framework for delegated API access via scopes/consent Actors: User → Client → Auth Server → API Flow gold standard: Auth Code + PKCE (SPA/mobile) M2M: Client Credentials flow Tokens: Access (15min) + Refresh (24h, revocable) Security: State(CSRF) + PKCE + redirect_uri whitelist + HTTPS OIDC = OAuth + ID tokens for authentication Positioning: Replace password anti-pattern, enable fine-grained API policy"`

**Reference**: [Okta Developer Blog - What the Heck is OAuth?](https://developer.okta.com/blog/2017/06/21/what-the-heck-is-oauth)[developer.okta](https://developer.okta.com/blog/2017/06/21/what-the-heck-is-oauth)​

**Perfect senior architect depth: flows, security pitfalls, positioning vs alternatives.** 🚀

1. [https://developer.okta.com/blog/2017/06/21/what-the-heck-is-oauth](https://developer.okta.com/blog/2017/06/21/what-the-heck-is-oauth)