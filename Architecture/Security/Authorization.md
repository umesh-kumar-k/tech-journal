# **Authorization**

## **Core Definition & Relationship to Authentication**
- **Authorization (AuthZ):** The process of determining **what an authenticated identity can do** with specific resources.
- **Key Distinction:** **Authentication (AuthN) = Who are you?** vs. **Authorization (AuthZ) = What can you do?**
- **Sequential Process:** AuthN → Identity → AuthZ → Permissions/Access.
- **Scope:** Applies to actions (CRUD), data, features, and APIs.

## **Authorization Models & Patterns**

### **1. Role-Based Access Control (RBAC)**
- **Concept:** Permissions assigned to **roles**, users assigned to roles.
- **Structure:** User → Roles → Permissions.
- **Example:** "Admin" role has delete permission; "User" role has read-only.
- **Pros:** Simple, manageable, familiar.
- **Cons:** Inflexible; role explosion problem; doesn't consider resource context.

### **2. Attribute-Based Access Control (ABAC)**
- **Concept:** Evaluates **attributes** of user, resource, action, and environment.
- **Attributes:** User (department, clearance), Resource (sensitivity, owner), Environment (time, location).
- **Policy Language:** Often uses boolean logic (IF user.department == resource.department AND time < 5pm).
- **Pros:** Highly flexible, granular, context-aware.
- **Cons:** Complex, performance overhead, harder to audit.

### **3. Relationship-Based Access Control (ReBAC)**
- **Concept:** Determines access based on **relationships** between entities.
- **Common in:** Social networks, document collaboration, hierarchical organizations.
- **Examples:** "User can edit document if they are owner OR shared as editor."
- **Model:** Graph-based (users, resources as nodes; relationships as edges).
- **Pros:** Models real-world scenarios naturally; supports delegation.
- **Cons:** Complex to implement; requires relationship graph maintenance.

### **4. Policy-Based Access Control (PBAC)**
- **Concept:** Centralized policy engine evaluates policies written in declarative language.
- **Separation of Concerns:** Application code calls policy engine with context.
- **Example:** Open Policy Agent (OPA) with Rego language.
- **Pros:** Centralized, reusable, testable, audit-friendly.
- **Cons:** External dependency, network latency (if remote).

## **Key Concepts & Terminology**
- **Principle of Least Privilege:** Grant minimum permissions necessary.
- **Access Control Lists (ACLs):** List of permissions attached to resource (legacy, limited scale).
- **Implicit vs Explicit Deny:** Default-deny (secure) vs default-allow.
- **Coarse vs Fine-Grained:** RBAC is coarse; ABAC/ReBAC can be fine-grained.
- **Permission Inheritance:** Hierarchical permissions (folder → files).

## **Architectural Implementation Patterns**

### **1. Embedded/Library Model**
- Authorization logic in application code.
- **Risk:** Logic scattered, inconsistent, hard to maintain.

### **2. Centralized Service Model**
- Dedicated authorization service/microservice.
- **Pros:** Consistent, scalable, single point of update.
- **Cons:** Network dependency, potential latency.

### **3. Sidecar/Proxy Model**
- Authorization decisions at API gateway/service mesh.
- **Pros:** Transparent to application, consistent enforcement.
- **Cons:** Limited to request context.

### **4. Hybrid Approach**
- Simple checks in app; complex policies in external engine.

## **Modern Trends & Best Practices**
- **Externalized Authorization:** Decouple from application code (Zanzibar paper, Google's system).
- **Policy as Code:** Store, version, and test policies in Git (GitOps approach).
- **Real-time vs Cached Decisions:** Balance latency vs consistency.
- **Audit Trail:** Log all authorization decisions for compliance.
- **Scalability:** Authorization systems must handle thousands of requests per second.

## **Security Considerations**
1. **Broken Object Level Authorization (BOLA):** #1 API security risk (OWASP). Must validate user can access **this specific resource ID**.
2. **Permission Escalation:** Ensure users can't grant themselves higher privileges.
3. **Session Management:** Authorization tied to session; proper invalidation on logout/change.
4. **Caching Risks:** Permissions changes must invalidate cached decisions.
5. **Zero Trust:** Never trust, always verify - even for internal requests.

## **Evaluation Criteria for Solutions**
- **Performance:** Decision latency (<10ms), throughput.
- **Expressiveness:** Can model complex business rules.
- **Auditability:** Can explain why access was granted/denied.
- **Integration:** Ease of integration with existing AuthN systems.
- **Scalability:** Handle growth in users, resources, policies.

---

## **Interview Checklist**
- [ ] Can clearly **differentiate Authorization vs Authentication**.
- [ ] Can explain **RBAC, ABAC, and ReBAC** with pros/cons for each.
- [ ] Understands **BOLA (Broken Object Level Authorization)** and mitigation.
- [ ] Knows **implementation patterns**: embedded, centralized, sidecar.
- [ ] Can discuss **externalized authorization** benefits and trade-offs.
- [ ] Aware of **policy as code** and GitOps practices.
- [ ] Understands **principle of least privilege** in practice.
- [ ] Can evaluate **performance implications** of fine-grained authorization.
- [ ] Knows **audit and compliance requirements**.
- [ ] Can explain **relationship to zero trust architecture**.

---

## **60-Second Recap (Bullet Points)**
- **Authorization determines permissions** after authentication establishes identity.
- **RBAC:** Simple roles→permissions; suffers from role explosion.
- **ABAC:** Flexible attribute-based rules (user, resource, environment).
- **ReBAC:** Relationship-based (social graphs, hierarchies).
- **Modern trend:** Externalized policy engines (OPA, Cedar) decoupled from apps.
- **BOLA is #1 API risk:** Always validate access to specific resource IDs.
- **Implementations:** Embedded (simple), centralized service (consistent), sidecar (transparent).
- **Policy as Code:** Version, test, and deploy policies like application code.
- **Audit everything:** Log all authorization decisions for compliance.
- **Performance matters:** Authorization checks happen on every request; optimize accordingly.

---

**Reference Link:** [What is Authorization?](https://www.cerbos.dev/blog/what-is-authorization)

**Memorization Mnemonic:** **AUTHZ MODELS:**
- **A** = **A**fter authentication
- **U** = **U**ser context matters
- **T** = **T**hree main models (RBAC, ABAC, ReBAC)
- **H** = **H**andle BOLA risk
- **Z** = **Z**ero trust default
- **M** = **M**odern = externalized
- **O** = **O**PA/Cedar engines
- **D** = **D**ecouple from app
- **E** = **E**valuate performance
- **L** = **L**east privilege principle
- **S** = **S**ecure by design

Authorization controls **what** authenticated principals can access/do via policy evaluation (RBAC/ABAC/ReBAC); senior architects select models balancing granularity, performance, and auditability.[cerbos](https://www.cerbos.dev/blog/what-is-authorization)​

---

## **Authorization – Senior Architect Summary**

## **Core Models (Memorize 5 + Use Cases)**

|Model|Control|Granularity|Use Case|
|---|---|---|---|
|**RBAC**|Roles|**Coarse**|Corporate IT (manager/employee)|
|**ABAC**|Attributes|**Fine**|User+resource+context (dept/time/device)|
|**ReBAC**|Relationships|**Dynamic**|Social (friends-of-friends), Docs (collaborators)|
|**DAC**|Owner grants|**Flexible**|File systems, personal data|
|**MAC**|Central labels|**Strict**|Military/gov (top-secret clearance)|

## **Components (Policy Triangle)**

|Component|Role|Example|
|---|---|---|
|**Policy**|Rules|`editor can create articles`|
|**Context**|Inputs|user.role, resource.owner, time=9-5|
|**PDP**|Decision|Cerbos/Oso → allow/deny|

---

## **Interview Checklist – Authorization Mastery**

**✅ Model Selection**

-  RBAC: simple, audit-friendly, role explosion risk
    
-  ABAC: flexible, complex policy mgmt
    
-  ReBAC: relationship-driven, graph complexity
    
-  PDP → PEPs pattern (central policy + edge enforcement)
    

**✅ Architecture Decisions**

-  **Least privilege** → default deny
    
-  **Audit trail** → all decisions logged
    
-  **Scale**: compile-time vs runtime evaluation
    
-  **Migration**: RBAC → ABAC → ReBAC evolution
    

**✅ Implementation**

text

`✅ Centralized PDP (Cerbos/Open Policy Agent) ✅ YAML/Rego policy language ✅ Context: principal+resource+action+context ✅ Fallback: explicit deny`

**✅ Real-World Mapping**

-  Corporate: RBAC+ABAC
    
-  Social: ReBAC+DAC
    
-  Gov: MAC
    

---

## **60-Second Recap – "Must-Say" Bullets**

text

`"Authorization = WHAT after authentication (RBAC/ABAC/ReBAC) RBAC(roles): corporate → simple/audit ABAC(attributes): enterprise → user+resource+context ReBAC(relationships): social/docs → dynamic access Architecture: PDP(central policy) → PEPs(enforce) Least privilege + audit all decisions + explicit deny Scale: compile-time policy → runtime context eval"`

**Reference**: [Cerbos - What Is Authorization?](https://www.cerbos.dev/blog/what-is-authorization)[cerbos](https://www.cerbos.dev/blog/what-is-authorization)​

**Architect gold: model progression, PDP/PEP separation, real-world mapping.** 🚀

1. [https://www.cerbos.dev/blog/what-is-authorization](https://www.cerbos.dev/blog/what-is-authorization)


---

## Authorization Frameworks & Tools – Senior Architect Reference

|Model|Tool/Framework|Description|Language / Ecosystem|
|---|---|---|---|
|**RBAC**|**Keycloak**|Open-source IAM, supports RBAC, user federation, and SSO|Java, distributed|
||**Apache Shiro**|Flexible security framework with RBAC support|Java|
|**ABAC**|**Open Policy Agent (OPA)**|General-purpose policy engine using Rego for attribute-based policies|Any (REST API)|
||**Cerbos**|Policy decision point focused on ABAC with developer-friendly DSL|Go, Kubernetes-native|
|**ReBAC**|**AuthZForce**|XACML-based engine supporting relationship-based policies|Java|
||**Oso**|Embedded policy framework emphasizing relationship-aware authorization|Python, JS, Ruby, Go|
|**DAC**|**Linux/Windows File ACLs**|Native filesystem discretionary access controls|OS-native|
||**AWS IAM** (also ABAC)|Policy-driven resource permissions, supports owner-based rules|Cloud-native|
|**MAC**|**SELinux**|Mandatory Access Controls for Linux security contexts|Linux kernel level|
||**AppArmor**|MAC for Linux with profile-based restrictions|Linux kernel level|

## Additional Tools & Patterns

- **Policy Engines**: OPA, Cerbos, Oso — central PDP with fine-grained context evaluation
    
- **Identity Platforms**: Okta, Auth0, Keycloak — combined authN + authZ with RBAC/ABAC
    
- **Kubernetes**: RBAC + ABAC roles + OPA Gatekeeper (policy enforcement)
    
- **Cloud Native**: AWS IAM, GCP IAM — fine-grained, attribute and role based controls
    

---

## Quick Architecture Integration Tips

- Use **OPA/Cerbos** for dynamic, environment/context aware ABAC policies.
    
- Combine **Keycloak** with **OPA** for hybrid RBAC + ABAC implementation.
    
- Use **Oso** for app-embedded, graph relationship-based decisions (ReBAC).
    
- Use OS-native DAC/MAC for low-level resource isolation and mandatory access control.
    
- Cloud: Use vendor IAM (AWS/GCP/Azure) policies to enforce ownership, attributes at scale.
    

---

This list balances mature enterprise tools and modern cloud-native policy engines, helping bridge architectural decisions to concrete platforms.

Ready to build or evaluate authorization pipelines quickly with these at hand is a plus for senior architect interviews.

