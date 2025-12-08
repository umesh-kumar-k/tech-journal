## API Gateway Interview Checklist

- **Core Purpose**
    
    - **Single Entry Point:** Centralized reverse proxy for all client-to-service traffic; hides service decomposition from clients.
        
    - **Cross-Cutting Concerns:** Offloads auth, SSL termination, rate limiting, logging from individual services.
        
- **Key Benefits**
    
    - **Simplified Clients:** One endpoint vs multiple; no service discovery needed by clients.
        
    - **Loose Coupling:** Clients decoupled from backend changes/refactoring.
        
    - **Reduced Latency:** Aggregation patterns minimize chattiness (one client call → multiple service calls → aggregated response).
        
    - **Security:** Reduced attack surface; centralized WAF, IP filtering, mTLS.
        
- **Design Patterns**
    
    - **Gateway Routing:** L7 routing to services based on path/headers; service discovery integration.
        
    - **Gateway Aggregation:** Single client request → fan-out to services → aggregate responses.
        
    - **Gateway Offloading:** SSL, auth (OAuth/JWT), rate limiting, caching, compression, static content.
        
- **Implementation Options**
    
    - **Reverse Proxies:** **Nginx**, **HAProxy** (open-source, high-perf, L7 routing).
        
    - **Service Mesh Ingress:** **Istio**, **Linkerd**, **Consul Connect** (built-in with Kubernetes).
        
    - **Cloud Managed:** **Azure App Gateway** (WAF + L7), **Azure API Mgmt** (policy-rich), **Azure Front Door** (CDN + global routing).
        
    - **API-Focused:** **Kong**, **Ambassador**, **Traefik** (cloud-native, plugin ecosystems).
        
- **Feature Comparison**
    
    |Feature|Nginx/HAProxy|Istio Ingress|Azure API Mgmt|Spring Cloud Gateway|
    |---|---|---|---|---|
    |L7 Routing|✅|✅|✅|✅|
    |Rate Limiting|✅ (paid)|✅|✅|✅|
    |Auth/JWT|Plugin|✅|✅|✅|
    |WAF|❌|❌|✅|Plugin|
    
- **Deployment Models**
    
    - **Outside Cluster:** Dedicated VMs/Ingress (isolation, simpler scaling).
        
    - **Inside Cluster:** DaemonSet/Ingress controller (auto-scaling with workloads).
        
    - **Managed PaaS:** Azure/AWS/GCP services (zero ops).
        
- **Architectural Trade-offs**
    
    - **Single Point Failure:** High availability (multi-zone), circuit breakers critical.
        
    - **Latency Overhead:** Minimal vs direct service calls; aggregation tradeoff.
        
    - **Complexity:** Centralized config vs distributed; GitOps for declarative management.
        
- **Tools & Integrations**
    
    - **Monitoring:** Prometheus + Grafana, Azure Monitor.
        
    - **Service Discovery:** Consul, Eureka, Kubernetes services.
        
    - **Security:** mTLS (Istio), OAuth2 (Keycloak), WAF rules.
        
    - **Circuit Breakers:** Resilience4j, Hystrix (legacy).
        
- **Best Practices**
    
    - Prefer **platform-native** gateways (AKS Istio, Azure App Gateway).
        
    - **Infrastructure-as-Code:** Terraform, Helm, GitOps (ArgoCD/Flux).
        
    - Early **load testing** for aggregation patterns.
        

## 60-Second Recap

- **API Gateway** = reverse proxy + cross-cutting concerns (auth/rate-limit/SSL) + routing/aggregation.
    
- **Patterns:** Routing (decouple), Aggregation (reduce chattiness), Offloading (centralize concerns).
    
- **Choices:** Nginx/HAProxy (general), Istio (mesh), Azure API Mgmt (managed), Spring Cloud Gateway (Spring).
    
- **Gold:** Platform-native first, GitOps management, multi-zone HA, early perf testing.
    

**Reference**: [Azure API Gateway Design](https://learn.microsoft.com/en-us/azure/architecture/microservices/design/gateway)[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/microservices/design/gateway)​

1. [https://learn.microsoft.com/en-us/azure/architecture/microservices/design/gateway](https://learn.microsoft.com/en-us/azure/architecture/microservices/design/gateway)

**Source:** Microsoft Azure Architecture Center | [Gateway Design Pattern](https://learn.microsoft.com/en-us/azure/architecture/microservices/design/gateway)

**Main Idea:** A **Gateway** (or API Gateway) is a **single entry point** that sits between client applications and a collection of microservices. It handles cross-cutting concerns like routing, composition, security, and monitoring, abstracting the complexity of the backend from clients and enabling centralized management.

---

#### **Notes: Core Functions & Patterns**

**1. The Problem a Gateway Solves**
*   **Complexity Exposure:** Without a gateway, clients must manage multiple backend endpoints, understand service topologies, and make numerous network calls.
*   **Cross-Cutting Concerns Duplication:** Each client would need to implement authentication, logging, retry logic, etc.
*   **Backend Coupling:** Changes to backend service APIs or deployments directly impact clients.

**2. Primary Responsibilities of a Gateway**
*   **Routing (Request Forwarding):** Routes client requests to appropriate backend services based on URL path, headers, or other criteria.
*   **API Composition/Aggregation:** Combines calls to multiple downstream services to fulfill a single client request (e.g., fetch user profile + recent orders).
*   **Protocol Translation:** Translates between client protocols (e.g., WebSocket, HTTP/2) and backend protocols (e.g., gRPC, AMQP).
*   **Offloading Cross-Cutting Concerns:**
    *   **Authentication & Authorization:** Verifies credentials (JWT, API keys) before forwarding requests.
    *   **Rate Limiting & Throttling:** Protects backend services from excessive traffic.
    *   **Caching:** Stores frequent responses to reduce backend load.
    *   **Logging & Monitoring:** Centralized request logging, metrics collection, and distributed trace correlation.
    *   **Load Balancing:** Distributes traffic across service instances.
    *   **SSL Termination:** Handles HTTPS decryption.

**3. Gateway Types & Variations**
*   **API Gateway (North-South Traffic):** The standard pattern for **external client-to-service** communication. (**Azure API Management**).
*   **Backend for Frontend (BFF) Pattern:** A **specialized API Gateway** tailored for a specific client type (e.g., mobile BFF, web BFF). Each BFF provides an API shaped precisely for its client's UI needs.
*   **Aggregation Gateway:** Focuses primarily on the composition/aggregation function to reduce client chattiness.
*   **Internal Gateway:** Sometimes used within a private network to provide a unified interface for internal services, though a **service mesh** is often more appropriate for pure service-to-service traffic.

**4. Azure Implementation: Azure API Management (APIM)**
*   **Primary Tool:** **Azure API Management** is the recommended managed service for implementing the Gateway pattern.
*   **Key Features:**
    *   **Policy Engine:** Define XML-based policies for transformation, security, routing (e.g., `validate-jwt`, `set-backend-service`, `cache-store`).
    *   **Developer Portal:** Auto-generated, customizable site for API discovery, documentation, and onboarding.
    *   **Analytics & Insights:** Built-in dashboards for API usage, performance, and errors.
    *   **Lifecycle Management:** Versioning, staging, and product/group management for APIs.

**5. Design Considerations & Best Practices**
*   **Avoid Business Logic:** The gateway should handle **infrastructure concerns**, not business rules. Keep it thin and focused on integration.
*   **Resilience:** The gateway becomes a **single point of failure**. Must be deployed with high availability (multiple instances, across zones).
*   **Performance:** Aggregation can increase latency if calls are sequential. Design for **parallel calls** where possible. Implement caching.
*   **Decentralization of Data Ownership:** The gateway **does not** own or store business data. It routes and transforms requests.
*   **Gateway vs. Service Mesh:** Complementary patterns.
    *   **Gateway:** Manages **north-south** traffic (external ingress).
    *   **Service Mesh (e.g., Istio):** Manages **east-west** traffic (internal service-to-service).

---

#### **Summary / Key Takeaways for Interview:**

*   **Abstraction Layer:** The Gateway's primary value is **hiding the complexity** of the microservices backend from clients, providing a unified, stable facade.
*   **Separation of Concerns:** It **centralizes cross-cutting, infrastructure-level concerns** (auth, logging, rate limiting) so individual services and clients don't have to.
*   **APIM is More Than a Router:** **Azure API Management** is a **full API platform** with policy enforcement, developer engagement, and analytics, making it a strategic choice beyond simple routing.
*   **BFF for Client-Specific Optimization:** The **Backend for Frontend** pattern is a critical variation when different client types (mobile vs. desktop) have vastly different data and performance requirements.
*   **Don't Create a Monolithic Gateway:** Resist the temptation to embed business logic. Keep it focused on routing, composition, and cross-cutting concerns to avoid it becoming a bottleneck or a "god" service.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"What is the purpose of an API Gateway in microservices?"** → Frame it as the **front door and facade**: 1) **Simplifies clients** (single entry point), 2) **Centralizes security** (auth, rate limiting), 3) **Enables composition** (aggregates backend calls), 4) **Abstracts backend evolution** from clients.
*   **"How does Azure API Management (APIM) work?"** → Describe it as a **managed reverse proxy with a policy engine**. You publish APIs by pointing it to backend services, define policies (XML) for security/routing/transformation, and it provides a developer portal and analytics.
*   **"What is the Backend for Frontend (BFF) pattern?"** → A **specialized gateway per client application type** (e.g., mobile app, web app). Each BFF tailors the API to its client's specific needs—aggregating data, transforming formats, managing protocols—preventing a "lowest common denominator" API.
*   **"When would you *not* use an API Gateway?"** → For pure **internal service-to-service communication** within a secure network boundary. There, a **service mesh** is often more appropriate. Also, for extremely simple architectures with 1-2 services, the overhead may not be justified.
*   **"How do you prevent the API Gateway from becoming a bottleneck or single point of failure?"** → **Deploy redundantly** (multiple instances across zones/regions). Use **caching** at the gateway to reduce backend load. Keep it **stateless** to enable horizontal scaling. Offload heavy composition logic to dedicated **aggregator services** if needed.