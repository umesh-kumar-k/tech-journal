## Spring Security Authentication Interview Checklist

- **Core Concept**: Authentication verifies user identity (who you are) before authorization (what you can do); Spring Security handles username/password, tokens, and multi-factor flows.[spring](https://docs.spring.io/spring-security/reference/features/authentication/index.html)​
    
- **Architecture**: SecurityFilterChain processes requests; AuthenticationManager validates credentials via AuthenticationProvider (UserDetailsService + PasswordEncoder).
    
- **UserDetailsService**: Loads user data from DB/LDAP; implement custom for roles/permissions; BCryptPasswordEncoder for secure hashing (never store plaintext).
    
- **Configuration**: @EnableWebSecurity + SecurityFilterChain bean; http.authorizeHttpRequests() for URL patterns, .formLogin(), .oauth2Login() for social/enterprise.
    
- **Stateless/JWT**: Disable session via http.sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS); use JwtDecoder/Encoder for microservices.
    
- **OAuth2/OpenID**: Resource Server (http.oauth2ResourceServer().jwt()); Authorization Server (separate app); integrates with Keycloak, Auth0, Okta.
    
- **Tools/Frameworks**: Spring Security OAuth (legacy), Spring Authorization Server (new), Keycloak (IAM), JWT (jjwt.io), SAML (Shibboleth), MFA (TOTP via Google Authenticator).
    
- **Advanced**: Method security (@PreAuthorize("hasRole('ADMIN')")), CSRF/XSS protection, CORS config; reactive support via SecurityWebFilterChain.
    
- **Distributed/Microservices**: JWT propagation + gateway (Spring Cloud Gateway + security); service mesh (Istio) for mTLS; audit with Spring Boot Actuator + audit events.
    
- **Architect Trade-offs**: Stateful sessions vs stateless JWT (scale vs simplicity); custom vs standards (OAuth2.1); RBAC vs ABAC (XACML/PBAC extensions).
    
- **Migration/Security**: Upgrade to 6.x (OAuth2.1 compliance); zero-trust with mTLS; threat modeling (OWASP Top 10 coverage).
    
- **Monitoring**: Security events to ELK/Splunk; metrics via Micrometer (auth failures, token validations).
    

## 60-Second Recap

- Spring Security Auth = SecurityFilterChain → AuthenticationManager → UserDetailsService + PasswordEncoder.
    
- Config: @EnableWebSecurity + http.authorizeRequests/formLogin/oauth2Login; stateless JWT for microservices.
    
- Tools: Keycloak/Auth0 (IAM), Spring Auth Server (OAuth2), jjwt (tokens), SAML/MFA.
    
- Gold: Stateless scale, standards-first (OAuth2.1), audit+metrics for zero-trust.
    

**Reference**: [Spring Security Authentication](https://docs.spring.io/spring-security/reference/features/authentication/index.html)[spring](https://docs.spring.io/spring-security/reference/features/authentication/index.html)​

1. [https://docs.spring.io/spring-security/reference/features/authentication/index.html](https://docs.spring.io/spring-security/reference/features/authentication/index.html)


## Spring Security Authorization Interview Checklist

- **Authority Model**: Authentication holds GrantedAuthority objects representing permissions; most use SimpleGrantedAuthority with string roles/authorities; prefix customization possible via GrantedAuthorityDefaults.
    
- **AuthorizationManager**: Central interface replacing AccessDecisionManager/Voter; makes pre/post invocation decisions on secure objects (HTTP requests, methods); returns AuthorizationDecision (grant/deny/abstain).
    
- **Delegation**: RequestMatcherDelegatingAuthorizationManager chooses appropriate delegate per request; AuthorityAuthorizationManager checks specific authorities; AuthenticatedAuthorizationManager distinguishes anonymous/remember-me/full-authentication.
    
- **Role Hierarchy**: Supports hierarchical roles (e.g., ADMIN > USER) to simplify access control; configured globally for HTTP and method security; reduces explicit role assignments while enforcing access.
    
- **Legacy Support**: AccessDecisionManager and AccessDecisionVoters still usable for backward compatibility; voting schemes include AffirmativeBased, ConsensusBased, UnanimousBased with different grant/deny strategies.
    
- **Custom Extensions**: Custom AuthorizationManager or voters for complex business policies (e.g., with Open Policy Agent or suspended account check).
    
- **Method Security**: Supports pre/post authorization on methods via interceptors that integrate AuthorizationManager.
    
- **Config Samples**: Customize role prefix, trust resolver, role hierarchy, AuthorizationManagerFactory beans to adapt framework to business needs.
    
- **Tools/Frameworks**: Works with Spring Security core, integrates with OAuth2/OIDC, Keycloak, custom policy engines; supports annotation-based security for fine-grained control.
    
- **Architect Focus**: Emphasize composability, extensibility, migration from legacy voters, and simplifying role management for scalable, secure access control models.
    

## 60-Second Recap

- Spring Security Authorization uses GrantedAuthority strings, centralized AuthorizationManager for decisions.
    
- Supports role hierarchies, delegation, and legacy AccessDecision voting models.
    
- Extensible with custom managers/voters for business rules.
    
- Architect goal: simplify access policies, support complex scenarios, enable migration to modern manager model.
    

**Reference**: [Spring Security Authorization Architecture](https://docs.spring.io/spring-security/reference/servlet/authorization/architecture.html)

1. [https://docs.spring.io/spring-security/reference/servlet/authorization/architecture.html](https://docs.spring.io/spring-security/reference/servlet/authorization/architecture.html)
# **Spring Security Authentication & Authorization Summary**

## **Article 1: Authentication Features Overview**
**Source:** https://docs.spring.io/spring-security/reference/features/authentication/index.html

### **1. Authentication Core Concepts**
- **Authentication:** Verifying identity (who you are)
- **Principal:** Authenticated entity (user, device, system)
- **Credentials:** Proof of identity (password, token, certificate)
- **AuthenticationManager:** Main interface for authentication
- **AuthenticationProvider:** Processes specific authentication types

### **2. Authentication Components**
- **SecurityContext:** Holds current `Authentication` object
- **SecurityContextHolder:** Stores `SecurityContext` (ThreadLocal by default)
- **GrantedAuthority:** Authorization permissions for principal
- **UserDetails:** Core user information interface

---

## **Article 2: Password Storage**
**Source:** https://docs.spring.io/spring-security/reference/features/authentication/password-storage.html

### **1. Password Encoding Strategies**
- **DelegatingPasswordEncoder (Default):** Delegates to specific encoders with prefix
  - `{bcrypt}` - BCrypt (recommended)
  - `{pbkdf2}` - PBKDF2
  - `{scrypt}` - Scrypt
  - `{sha256}` - SHA-256 (legacy, not recommended)
- **PasswordEncoder:** Interface for encoding/verifying passwords

### **2. Password Encoder Recommendations**
- **Production:** BCryptPasswordEncoder (adaptive hashing)
- **High security:** Argon2PasswordEncoder (memory-hard)
- **Legacy migration:** DelegatingPasswordEncoder with multiple encoders
- **Never use:** Plain text, MD5, SHA-1 (without salt)

### **3. Configuration Examples**
```java
@Bean
public PasswordEncoder passwordEncoder() {
    return PasswordEncoderFactories.createDelegatingPasswordEncoder();
}

// OR specific encoder
@Bean 
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}
```

---

## **Article 3: Servlet Authentication**
**Source:** https://docs.spring.io/spring-security/reference/servlet/authentication/index.html

### **1. Authentication Mechanisms**
- **Form Login:** Username/password via HTML form
- **Basic Authentication:** HTTP Basic Auth (base64 encoded)
- **OAuth 2.0 / OpenID Connect:** Token-based authentication
- **Remember-Me:** Persistent authentication across sessions
- **Anonymous:** Unauthenticated users
- **Pre-authenticated:** External authentication (SAML, CAS)

### **2. Key Authentication Filters**
- `UsernamePasswordAuthenticationFilter`: Form login
- `BasicAuthenticationFilter`: HTTP Basic Auth
- `RememberMeAuthenticationFilter`: Remember-me tokens
- `AnonymousAuthenticationFilter`: Anonymous users

---

## **Article 4: Authentication Architecture**
**Source:** https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html

### **1. Authentication Flow**
```
1. Authentication Request → 
2. AuthenticationFilter → 
3. AuthenticationManager → 
4. AuthenticationProvider → 
5. UserDetailsService → 
6. PasswordEncoder → 
7. SecurityContextHolder
```

### **2. Core Components**
- **AuthenticationManager:** Entry point for authentication requests
  - `ProviderManager` (default implementation) delegates to AuthenticationProviders
- **AuthenticationProvider:** Processes specific authentication types
  - `DaoAuthenticationProvider`: Username/password authentication
  - `JwtAuthenticationProvider`: JWT token authentication
  - `LdapAuthenticationProvider`: LDAP authentication
- **UserDetailsService:** Loads user data (core interface for custom implementations)

### **3. SecurityContext Management**
```java
// Manual authentication
Authentication authentication = 
    new UsernamePasswordAuthenticationToken(principal, credentials, authorities);
SecurityContextHolder.getContext().setAuthentication(authentication);

// Retrieve current authentication
Authentication currentAuth = SecurityContextHolder.getContext().getAuthentication();
```

### **4. Thread Safety & Propagation**
- **ThreadLocal by default:** SecurityContext bound to current thread
- **Clear on completion:** Automatically cleared at request completion
- **Async propagation:** Use `DelegatingSecurityContextRunnable` or `@Async` with security context propagation

---

## **Article 5: Authorization Features Overview**
**Source:** https://docs.spring.io/spring-security/reference/features/authorization/index.html

### **1. Authorization Core Concepts**
- **Authorization:** Verifying permissions (what you can do)
- **Authority:** Granted permission (ROLE_USER, SCOPE_READ)
- **Access Decision:** Allow/deny based on authorization rules

### **2. Authorization Models**
- **Role-based (RBAC):** Based on user roles
- **Attribute-based (ABAC):** Based on attributes/conditions
- **Permission-based:** Fine-grained permissions
- **Domain object security:** Object-level authorization

---

## **Article 6: Servlet Authorization**
**Source:** https://docs.spring.io/spring-security/reference/servlet/authorization/index.html

### **1. Authorization Mechanisms**
- **URL Security:** Protect HTTP endpoints
- **Method Security:** Protect Java methods
- **Domain Object Security:** Protect individual object instances

### **2. URL Security Configuration**
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(authz -> authz
                .requestMatchers("/admin/**").hasRole("ADMIN")
                .requestMatchers("/api/**").authenticated()
                .requestMatchers("/public/**").permitAll()
                .anyRequest().authenticated()
            );
        return http.build();
    }
}
```

### **3. Method Security**
```java
@Configuration
@EnableMethodSecurity(prePostEnabled = true, securedEnabled = true)
public class MethodSecurityConfig {
}

// Usage
@Service
public class BankService {
    
    @PreAuthorize("hasRole('TELLER')")
    public Account post(Account account, double amount) { ... }
    
    @Secured("ROLE_ADMIN")
    public void deleteUser(Long userId) { ... }
    
    @PostAuthorize("returnObject.owner == authentication.name")
    public Account getAccount(Long id) { ... }
}
```

---

## **Article 7: Authorization Architecture**
**Source:** https://docs.spring.io/spring-security/reference/servlet/authorization/architecture.html

### **1. Authorization Flow**
```
1. Authorization Request → 
2. AccessDecisionManager → 
3. AccessDecisionVoter(s) → 
4. Authorization Decision
```

### **2. AccessDecisionManager Implementations**
- **AffirmativeBased (Default):** Grants if any voter grants
- **ConsensusBased:** Grants based on consensus
- **UnanimousBased:** Requires unanimous grant

### **3. AccessDecisionVoter Types**
- **RoleVoter:** Votes based on roles (ROLE_ prefix)
- **AuthenticatedVoter:** Votes based on authentication level
- **WebExpressionVoter:** Evaluates SpEL expressions
- **Custom Voters:** Implement `AccessDecisionVoter` interface

### **4. Expression-Based Access Control**
```java
@PreAuthorize("hasRole('ADMIN') or #contact.name == authentication.name")
public void updateContact(Contact contact);

@PreAuthorize("@securityService.canAccessUser(#userId)")
public User getUser(Long userId);
```

### **5. AfterInvocationManager**
- Post-invocation authorization (for filtering returned data)
- Used with `@PostAuthorize` and `@PostFilter`

---

## **60-Second Recap for Interview**

1. **Two Main Areas:** Authentication (verify identity) & Authorization (verify permissions)
2. **Core Architecture:** `AuthenticationManager` → `AuthenticationProvider` → `UserDetailsService`
3. **Password Security:** Use `DelegatingPasswordEncoder` with BCrypt for production
4. **Authorization Models:** URL security, Method security (@PreAuthorize), Domain security
5. **Key Components:** SecurityContextHolder, AccessDecisionManager, AuthenticationProvider

---

## **Interview Checklist**

### **Authentication Concepts:**
- [ ] Can explain difference between Authentication and Authorization
- [ ] Knows Authentication flow (AuthenticationManager → Provider → UserDetailsService)
- [ ] Understands SecurityContextHolder and ThreadLocal storage
- [ ] Can recommend password encoding strategies
- [ ] Knows different authentication mechanisms (Form, Basic, OAuth2)

### **Authorization Concepts:**
- [ ] Can explain AccessDecisionManager and Voter pattern
- [ ] Knows URL vs Method security configuration
- [ ] Understands expression-based authorization (SpEL)
- [ ] Can implement custom authorization logic
- [ ] Knows role vs permission-based models

### **Scenario Response:**
**If asked about implementing custom authentication:**
1. "Implement UserDetailsService to load user data"
2. "Configure AuthenticationProvider (often DaoAuthenticationProvider)"
3. "Set up PasswordEncoder (BCrypt recommended)"
4. "Extend authentication flow if needed with custom filters"
5. "Test with SecurityContextHolder verification"

**If asked about securing REST API:**
1. "Use JWT or OAuth2 for token-based authentication"
2. "Configure stateless sessions for REST"
3. "Implement method security with @PreAuthorize"
4. "Add global exception handling for authentication failures"
5. "Consider rate limiting and audit logging"

**If asked about password security:**
1. "Always use adaptive hashing (BCrypt, Argon2)"
2. "Use DelegatingPasswordEncoder for migration scenarios"
3. "Implement password policy (length, complexity, expiration)"
4. "Add account lockout for failed attempts"
5. "Never log or transmit passwords in plain text"

### **Common Interview Questions Prepared:**
- **"Authentication vs Authorization?"**
  - "Authentication verifies identity (who you are), authorization verifies permissions (what you can do)"

- **"How does SecurityContextHolder work?"**
  - "Stores SecurityContext in ThreadLocal by default, holds current Authentication object with principal and authorities"

- **"BCrypt vs other password encoders?"**
  - "BCrypt is adaptive hashing with work factor, resistant to brute force and rainbow tables, recommended for production"

- **"@PreAuthorize vs URL security?"**
  - "@PreAuthorize is method-level, flexible with SpEL. URL security is request-level, simpler for endpoint protection"

### **Architecture & Design:**
- [ ] **Security by Design:** "Build security in from the start, not as an afterthought"
- [ ] **Defense in Depth:** "Multiple layers of security (network, application, data)"
- [ ] **Principle of Least Privilege:** "Users get minimum permissions needed"
- [ ] **Audit Trail:** "Log all authentication and authorization decisions"
- [ ] **Regular Review:** "Periodic security review and dependency updates"

### **Key Phrases to Use:**
- "Spring Security separates authentication and authorization concerns for clear architecture"
- "The ProviderManager delegates to AuthenticationProviders, allowing multiple authentication mechanisms"
- "SecurityContextHolder uses ThreadLocal by default, but can be configured for different strategies"
- "DelegatingPasswordEncoder allows seamless migration between password encoding algorithms"
- "AccessDecisionManager with Voters provides flexible authorization decision making"
- "Method security with SpEL expressions enables powerful, context-aware authorization rules"

---

## **Security Implementation Matrix**

| **Layer** | **Authentication** | **Authorization** |
|-----------|-------------------|-------------------|
| **HTTP/Web** | Form Login, Basic Auth, OAuth2 | URL patterns, request matchers |
| **Method** | N/A | @PreAuthorize, @PostAuthorize, @Secured |
| **Domain/Object** | N/A | ACL, @PostFilter, custom permission evaluators |
| **Data/DB** | Connection authentication | Row-level security, column masking |

## **Common Security Pitfalls & Solutions**
1. **Session fixation:** Use `sessionManagement().sessionFixation().changeSessionId()`
2. **CSRF attacks:** Enable CSRF protection, exempt stateless endpoints
3. **XSS protection:** Content Security Policy, input validation
4. **Password in logs:** Never log credentials, use credential markers
5. **Insecure defaults:** Always override defaults for production

**Remember:** Security is not a feature you add, but a property you design for. Always think about security implications at every layer of your application!