
## Microservices Authorization Interview Checklist

- **Core Patterns**
    
    - **Gateway Authorization:** API Gateway validates tokens/claims, attaches user context to requests.[osohq](https://www.osohq.com/post/microservices-authorization-patterns)​
        
    - **Service-Level Authorization:** Each microservice enforces fine-grained access using embedded PDP/PEP.[cheatsheetseries.owasp](https://cheatsheetseries.owasp.org/cheatsheets/Microservices_Security_Cheat_Sheet.html)​
        
    - **Centralized Auth Service:** Dedicated service (OPA, Zanzibar) makes all decisions, services query it.[talentica+1](https://www.talentica.com/blogs/key-authorization-security-patterns-inmicroservice-architecture/)​
        
- **Authorization Models**
    
    |Model|Use Case|Pros|Cons|
    |---|---|---|---|
    |**RBAC**|Simple role-based access|Easy to implement|Limited granularity|
    |**ABAC**|Complex policy decisions|Fine-grained, contextual|Complex policy management|
    |**ReBAC**|Google Zanzibar-style|Handles sharing/relationships|High complexity|
    
- **Data Distribution Strategies**
    
    - **Leave Data Where It Is:** Services query owning service directly (high latency).
        
    - **Gateway Enrichment:** Gateway fetches/stuffs auth data into headers/tokens.[osohq](https://www.osohq.com/post/microservices-authorization-patterns)​
        
    - **Centralized Store:** Single source of truth, replicated or cached to services.
        
- **Best Practices**
    
    - **Zero Trust + Least Privilege:** Continuous verification, minimal access scopes.[techtarget](https://www.techtarget.com/searchapparchitecture/tip/Best-practices-for-microservices-authorization)​
        
    - **Edge + Service Authorization:** Coarse-grained at gateway, fine-grained at service.[krakend](https://www.krakend.io/blog/microservices-authorization-secure-access/)​
        
    - **mTLS Service-to-Service:** Mutual TLS for inter-service communication.
        
    - **Policy as Code:** OPA (Rego), Casbin for declarative policies.
        
- **Security Implementation**
    
    - **JWT Tokens:** Embed claims/roles, validate signature at gateway/services.
        
    - **API Gateway:** Auth0, Kong, Azure API Mgmt (central policy enforcement).
        
    - **Service Mesh:** Istio mTLS + authorization policies.
        
- **Tools & Frameworks**
    
    |Category|Tools|
    |---|---|
    |**Policy Engines**|Open Policy Agent (OPA), Casbin|
    |**Auth Servers**|Keycloak, Auth0, Ory, Zanzibar|
    |**API Gateways**|Kong, Ambassador, Spring Cloud Gateway|
    |**Service Mesh**|Istio, Linkerd (mTLS + auth)|
    
- **Architectural Trade-offs**
    
    |Pattern|Latency|Complexity|Consistency|
    |---|---|---|---|
    |**Gateway**|Low|Medium|Good|
    |**Centralized**|High|High|Excellent|
    |**Embedded**|Lowest|Low|Data sync issues|
    

## 60-Second Recap

- **Patterns:** Gateway (enrich requests), Centralized (auth service), Service-level (embedded PDP).
    
- **Models:** RBAC (simple), ABAC (granular), ReBAC (relationships).
    
- **Flow:** Edge coarse auth → Service fine-grained → mTLS inter-service.
    
- **Gold:** OPA policies, JWT claims, API Gateway + service mesh, least privilege.
    

**Reference**: [Microservices Authorization Best Practices](https://www.osohq.com/post/microservices-authorization-patterns)[osohq](https://www.osohq.com/post/microservices-authorization-patterns)​

1. [https://www.osohq.com/post/microservices-authorization-patterns](https://www.osohq.com/post/microservices-authorization-patterns)
2. [https://cheatsheetseries.owasp.org/cheatsheets/Microservices_Security_Cheat_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Microservices_Security_Cheat_Sheet.html)
3. [https://www.talentica.com/blogs/key-authorization-security-patterns-inmicroservice-architecture/](https://www.talentica.com/blogs/key-authorization-security-patterns-inmicroservice-architecture/)
4. [https://www.techtarget.com/searchapparchitecture/tip/Best-practices-for-microservices-authorization](https://www.techtarget.com/searchapparchitecture/tip/Best-practices-for-microservices-authorization)
5. [https://www.krakend.io/blog/microservices-authorization-secure-access/](https://www.krakend.io/blog/microservices-authorization-secure-access/)
6. [https://www.permit.io/blog/best-practices-for-authorization-in-microservices](https://www.permit.io/blog/best-practices-for-authorization-in-microservices)
7. [https://microservices.io/post/architecture/2025/04/25/microservices-authn-authz-part-1-introduction.html](https://microservices.io/post/architecture/2025/04/25/microservices-authn-authz-part-1-introduction.html)
8. [https://www.techtarget.com/searchAppArchitecture/tip/Best-practices-for-microservices-authorization](https://www.techtarget.com/searchAppArchitecture/tip/Best-practices-for-microservices-authorization)
9. [https://www.sayonetech.com/blog/microservices-authorization/](https://www.sayonetech.com/blog/microservices-authorization/)
10. [https://www.softensity.com/blog/authentication-authorization-in-a-microservices-architecture-part-3/](https://www.softensity.com/blog/authentication-authorization-in-a-microservices-architecture-part-3/)
11. [https://www.tigera.io/learn/guides/microservices-security/](https://www.tigera.io/learn/guides/microservices-security/)
12. [https://stackoverflow.com/questions/70294542/authorization-and-authentication-in-microservices-the-good-way](https://stackoverflow.com/questions/70294542/authorization-and-authentication-in-microservices-the-good-way)
13. [https://developer.okta.com/blog/2020/03/23/microservice-security-patterns](https://developer.okta.com/blog/2020/03/23/microservice-security-patterns)
14. [https://www.geeksforgeeks.org/system-design/authentication-and-authorization-in-microservices/](https://www.geeksforgeeks.org/system-design/authentication-and-authorization-in-microservices/)
15. [https://www.dailysmarty.com/posts/4-best-practices-for-microservices-authorization](https://www.dailysmarty.com/posts/4-best-practices-for-microservices-authorization)
16. [https://www.osohq.com/learn/microservices-security](https://www.osohq.com/learn/microservices-security)
17. [https://www.osohq.com/academy/microservices-authorization](https://www.osohq.com/academy/microservices-authorization)
18. [https://permify.co/post/microservices-authentication-authorization-using-api-gateway/](https://permify.co/post/microservices-authentication-authorization-using-api-gateway/)
19. [https://stackoverflow.com/questions/73423308/authentication-patterns-in-microservices](https://stackoverflow.com/questions/73423308/authentication-patterns-in-microservices)
20. [https://microservices.io/post/architecture/2025/05/28/microservices-authn-authz-part-2-authentication.html](https://microservices.io/post/architecture/2025/05/28/microservices-authn-authz-part-2-authentication.html)
### **4 Best Practices for Microservices Authorization**

**Source:** The New Stack | [https://thenewstack.io/microservices/4-best-practices-for-microservices-authorization/](https://thenewstack.io/microservices/4-best-practices-for-microservices-authorization/)

**Main Idea:** Authorization in a microservices architecture requires a deliberate, consistent strategy to manage complexity and maintain security. The core principle is to externalize and centralize authorization logic, keeping it separate from business logic within individual services.

---

#### **Notes: The Four Best Practices**

**1. Use a Consistent Authorization Framework**
*   **Problem:** In a polyglot architecture, having each service team implement custom authorization logic leads to inconsistency, security gaps, and audit nightmares.
*   **Solution:** Adopt a **unified, language-agnostic framework** or model (e.g., **Open Policy Agent (OPA)** with Rego, AWS Cedar, Google Zanzibar) that all services can call.
*   **Benefit:** Ensures uniform enforcement of security policies, simplifies audits, and allows security experts to manage policies centrally while developers focus on business logic.

**2. Centralize Authorization Logic**
*   **Concept:** Decouple authorization decisions from the service's code. The service should ask a centralized component **"Can user X perform action Y on resource Z?"** and receive a yes/no answer.
*   **Implementation Models:**
    *   **Sidecar Pattern:** Deploy an authorization sidecar (e.g., an OPA agent) alongside each service. The service makes a local API call to the sidecar.
    *   **External Service:** Call a dedicated, shared authorization service (requires careful management for latency and availability).
*   **Why:** Enables policy updates across all services without redeploying them. Provides a single point of control and logic.

**3. Grant Access Based on Roles and Attributes (RBAC/ABAC)**
*   **Evolution of Models:**
    *   **Role-Based Access Control (RBAC):** Permissions are assigned to roles (e.g., "admin," "viewer"). Simple but can become bloated ("role explosion").
    *   **Attribute-Based Access Control (ABAC):** More granular. Decisions are based on **attributes** of the user, resource, action, and environment (e.g., `user.department == resource.ownerDepartment`).
*   **Recommended Approach:** Use a **hybrid model** (often called **ReBAC** or **RBAC with attributes**). Start with RBAC for simplicity and incorporate ABAC where finer-grained control is needed (e.g., "a user can edit a document if they are the `owner` **and** the document's `status` is `draft`").
*   **Key Tool:** **Open Policy Agent (OPA)** excels here, as its Rego language is designed for evaluating complex rules against structured attribute data.

**4. Perform Authorization at Every Hop (Zero Trust)**
*   **Problem:** Assuming that a request validated at the API Gateway is "trusted" internally is a major security flaw in microservices.
*   **Principle:** **Zero Trust Network.** Every service must validate the authorization of an incoming request, even if it comes from another internal service.
*   **Practice:** Propagate the user's identity and context (e.g., via a signed **JWT token** or other credential) through the entire call chain. Each service uses this context to make its own authorization decision based on centralized policies.
*   **Why:** Prevents lateral movement if a single service is compromised and enforces least-privilege access throughout the system.

---

#### **Summary / Key Takeaways for Interview:**

*   **Decouple & Centralize:** The overarching theme is to **externalize authorization logic** from service code. Treat authorization as a separate, configurable concern managed by a dedicated framework.
*   **OPA is the De Facto Standard:** For microservices authorization, **Open Policy Agent (OPA)** is frequently the recommended tool. It provides the unified, decoupled, policy-as-code model required by these best practices.
*   **Zero Trust is Non-Negotiable:** Authorization checks must happen at every service boundary. Trust is never assumed based on network location.
*   **Policy as Code:** Authorization policies should be defined in code (like Rego), stored in version control, and tested through CI/CD pipelines, enabling transparency, rollback, and automation.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"How do you handle authorization in a microservices architecture?"** → Structure your answer around these four practices: 1) Use a consistent framework (OPA), 2) Centralize the logic, 3) Use RBAC/ABAC, 4) Authorize at every hop.
*   **"What is OPA and why is it used?"** → Describe it as a **general-purpose policy engine** that decouples policy from code. It allows you to define policies in Rego language and evaluate them across all your services uniformly.
*   **"Why is authorization at the API Gateway insufficient?"** → Explain the **Zero Trust** principle. The gateway validates the *initial* user, but internal services need to authorize for their specific actions and data. A billing service shouldn't process a request just because the Order Service sent it.
*   **"What's the difference between RBAC and ABAC?"** → RBAC uses **roles** (coarse-grained), ABAC uses **attributes** (fine-grained). In microservices with complex rules, you often need a hybrid (e.g., "User with role `manager` can approve invoices in their own `region`").
*   **"How do services get the user context for authorization?"** → Explain the use of a **signed JWT token** (containing user ID, roles, attributes) passed from the gateway through the call chain (e.g., in the HTTP `Authorization` header). Each service decodes the token to get the context for its policy decision.