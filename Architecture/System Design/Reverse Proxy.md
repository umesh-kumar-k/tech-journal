
## Reverse Proxy vs Forward Proxy vs API Gateway vs Load Balancer Interview Checklist

- **Core Definitions & Perspectives**
    
    |Component|Protects|Position|Primary Role|
    |---|---|---|---|
    |**Forward Proxy**|**Clients**|Client → Internet|Anonymity, filtering|
    |**Reverse Proxy**|**Servers**|Client → Server|Routing, caching, security|
    |**Load Balancer**|N/A|Client → Server Pool|**Traffic distribution**|
    |**API Gateway**|APIs|Client → Microservices|API mgmt, auth, rate limiting|
    
- **Layer & Capabilities**
    
    |Component|OSI Layer|Content Inspection|Examples|
    |---|---|---|---|
    |**Forward Proxy**|L4/L7|Yes (filtering)|Squid, Privoxy|
    |**Reverse Proxy**|**L7**|Yes|**NGINX**, HAProxy, Apache|
    |**Load Balancer**|**L4/L7**|L4=No, L7=Yes|F5, AWS ALB/NLB|
    |**API Gateway**|**L7**|✅ Full API logic|Kong, AWS API Gateway|
    
- **Feature Comparison**
    
    |Feature|Forward Proxy|Reverse Proxy|Load Balancer|API Gateway|
    |---|---|---|---|---|
    |**Load Balancing**|❌|✅ Basic|✅ **Advanced**|✅|
    |**Caching**|❌|✅|Limited|✅|
    |**SSL Termination**|❌|✅|✅|✅|
    |**Rate Limiting**|✅|Limited|❌|✅ **Advanced**|
    |**Auth**|✅|Limited|❌|✅ **Full**|
    |**Client Anonymity**|✅|❌|❌|❌|
    
- **Common Architectures**
    
    text
    
    `Client → [DNS LB] → [L4 LB] → [Reverse Proxy/API Gateway] → Backend Pool Internet ← [Forward Proxy] ← Client (internal traffic)`
    
- **Implementation Tools**
    
    |Category|Tools|
    |---|---|
    |**Forward Proxy**|**Squid**, Blue Coat|
    |**Reverse Proxy**|**NGINX**, HAProxy, Varnish|
    |**Load Balancer**|**F5 BIG-IP**, AWS ALB/NLB, GCP LB|
    |**API Gateway**|**Kong**, **AWS API Gateway**, Tyk, Ambassador|
    
- **Decision Framework**
    
    |Use Case|Best Choice|
    |---|---|
    |**Corporate filtering**|Forward Proxy|
    |**Web serving**|Reverse Proxy + LB|
    |**Microservices**|**API Gateway + LB**|
    |**High throughput**|L4 Load Balancer|
    

## 60-Second Recap

- **Forward:** Client anonymity/filtering (Squid).
    
- **Reverse:** Server protection/routing/caching (NGINX).
    
- **Load Balancer:** Traffic distribution (F5, ALB)—subset of reverse proxy.
    
- **API Gateway:** API-specific + reverse proxy features (Kong, AWS).
    
- **Gold:** NGINX (reverse+LB) → API Gateway (microservices) + L4 LB (throughput).
    

**Reference**: [Network Components Cheat Sheet](https://hackernoon.com/the-network-system-design-cheat-sheet-load-balancer-reverse-proxy-forward-proxy-api-gateway)[geeksforgeeks+2](https://www.geeksforgeeks.org/system-design/api-gateway-vs-load-balancer-vs-reverse-proxy/)​

1. [https://www.geeksforgeeks.org/system-design/api-gateway-vs-load-balancer-vs-reverse-proxy/](https://www.geeksforgeeks.org/system-design/api-gateway-vs-load-balancer-vs-reverse-proxy/)
2. [https://api7.ai/learning-center/api-gateway-guide/api-gateway-vs-reverse-proxy-vs-load-balancer](https://api7.ai/learning-center/api-gateway-guide/api-gateway-vs-reverse-proxy-vs-load-balancer)
3. [https://rockybhatia.substack.com/p/system-design-load-balancer-vs-reverse](https://rockybhatia.substack.com/p/system-design-load-balancer-vs-reverse)
4. [https://www.youtube.com/watch?v=RqfaTIWc3LQ](https://www.youtube.com/watch?v=RqfaTIWc3LQ)
5. [https://www.designgurus.io/blog/load-balancer-reverse-proxy-api-gateway](https://www.designgurus.io/blog/load-balancer-reverse-proxy-api-gateway)
6. [https://www.reddit.com/r/devops/comments/py1q54/difference_between_reverse_proxy_load_balancer/](https://www.reddit.com/r/devops/comments/py1q54/difference_between_reverse_proxy_load_balancer/)
7. [https://blog.algomaster.io/p/load-balancer-vs-reverse-proxy-vs-api-gateway](https://blog.algomaster.io/p/load-balancer-vs-reverse-proxy-vs-api-gateway)
8. [https://hackernoon.com/the-network-system-design-cheat-sheet-load-balancer-reverse-proxy-forward-proxy-api-gateway](https://hackernoon.com/the-network-system-design-cheat-sheet-load-balancer-reverse-proxy-forward-proxy-api-gateway)
9. [https://www.youtube.com/watch?v=Q4XUptm9S8w](https://www.youtube.com/watch?v=Q4XUptm9S8w)
10. [https://www.linkedin.com/pulse/load-balancer-vs-reverse-proxy-api-gateway-anand-jha-zarhc](https://www.linkedin.com/pulse/load-balancer-vs-reverse-proxy-api-gateway-anand-jha-zarhc)

## Reverse Proxy Interview Checklist

- **Core Concepts**
    
    |Type|Role|Key Difference|
    |---|---|---|
    |**Reverse Proxy**|Protects **servers**|Sits in front, routes to backends|
    |**Forward Proxy**|Protects **clients**|Hides client IP from internet|
    
- **Key Benefits**
    
    |Benefit|Implementation|
    |---|---|
    |**Load Balancing**|Distribute across servers|
    |**Caching**|Store responses, reduce backend load|
    |**Security**|Hide backend IPs, DDoS protection, WAF|
    |**SSL Termination**|Offload TLS from backends|
    |**A/B Testing**|Traffic splitting|
    
- **Drawbacks & Mitigations**
    
    |Issue|Mitigation|
    |---|---|
    |**Complexity**|Use managed solutions (Cloudflare)|
    |**Bottleneck**|Horizontal scaling + health checks|
    |**Cert Management**|Automated (Let's Encrypt, Caddy)|
    
- **Implementation Tools**
    
    |Tool|Strengths|Config Example|
    |---|---|---|
    |**NGINX**|Performance, flexibility|`proxy_pass http://backend`|
    |**Apache**|Modules (proxy_http)|`ProxyPass / http://backend/`|
    |**HAProxy**|TCP/HTTP LB|High-speed load balancing|
    |**Caddy**|Auto HTTPS|Simple config, built-in cache|
    |**Varnish**|HTTP caching|Cache-heavy use cases|
    
- **Advanced Use Cases**
    
    |Scenario|Reverse Proxy Role|
    |---|---|
    |**Microservices**|Service discovery, routing|
    |**WordPress**|Static caching, SSL offload|
    |**API Gateway**|Rate limiting, auth|
    |**CDN**|Edge caching, geo-routing|
    
- **HTTPS Flow**
    
    text
    
    `Client → HTTPS → Reverse Proxy (TLS Termination)                   ↓ Plain HTTP → Backend                  ↑ Plain HTTP ← Backend Client ← HTTPS ← Reverse Proxy`
    

## 60-Second Recap

- **Reverse Proxy:** Server-side intermediary (vs forward = client-side).
    
- **Powers:** LB, caching, security (DDoS/WAF), SSL termination, A/B testing.
    
- **Tools:** NGINX (flexible), HAProxy (speed), Caddy (simple HTTPS).
    
- **Drawbacks:** Complexity, potential bottleneck → scale horizontally.
    
- **Gold:** NGINX + health checks + caching + auto-cert (Let's Encrypt).
    

**Reference**: [Reverse Proxy Guide](https://systemdesignschool.io/blog/reverse-proxy)[systemdesignschool](https://systemdesignschool.io/blog/reverse-proxy)​

1. [https://systemdesignschool.io/blog/reverse-proxy](https://systemdesignschool.io/blog/reverse-proxy)
### **Reverse Proxy - System Design School**

**Source:** System Design School | [Reverse Proxy](https://systemdesignschool.io/blog/reverse-proxy)

**Main Idea:** A **reverse proxy** is a **server-side intermediary** that sits between clients and backend servers, acting as a **gateway and performance/security layer**. It handles requests on behalf of backend servers, providing benefits like **load balancing, SSL termination, caching, and security** while abstracting backend complexity from clients.

---

#### **Notes: Core Concepts & Functions**

**1. What is a Reverse Proxy?**
*   **Position:** Between **clients and origin servers**.
*   **Role:** **Represents the server** to clients (vs. forward proxy which represents clients).
*   **Client Perception:** Clients communicate with reverse proxy thinking it's the actual server.
*   **Key Purpose:** **Abstract, protect, and optimize** backend infrastructure.

**2. Primary Functions & Benefits**

**A. Load Balancing & High Availability**
*   Distributes traffic across **multiple backend servers**.
*   Performs **health checks** to remove unhealthy servers.
*   Enables **horizontal scaling** by adding more backend servers.
*   Provides **failover** capabilities.

**B. SSL/TLS Termination**
*   Handles **HTTPS encryption/decryption** at the proxy.
*   **Offloads CPU-intensive cryptography** from backend servers.
*   Allows backend servers to communicate in **plain HTTP** (simpler, faster).
*   Centralizes **certificate management**.

**C. Caching**
*   **Caches static content** (images, CSS, JS, HTML) at edge.
*   **Reduces load** on origin servers.
*   **Improves response times** for cached content.
*   Configurable via `Cache-Control` headers.

**D. Security & Protection**
*   **Hides backend servers** (origin IPs not exposed).
*   **DDoS protection** by absorbing attack traffic.
*   **Web Application Firewall (WAF)** capabilities.
*   **Rate limiting** to prevent abuse.
*   **IP filtering/whitelisting**.

**E. Compression**
*   **Gzip/Brotli compression** of responses.
*   Reduces **bandwidth usage** and **improves load times**.

**F. Request/Response Transformation**
*   **Header manipulation** (add/remove/modify).
*   **URL rewriting/redirects**.
*   **Protocol translation** (HTTP/1.1 to HTTP/2, WebSocket support).

**3. How Reverse Proxy Works**
1.  **Client** sends request to reverse proxy (thinks it's the real server).
2.  **Reverse Proxy** receives request and decides how to handle it:
    *   Serve from **cache** (if cached and valid).
    *   **Route** to appropriate backend server.
    *   Apply **security rules** (block if malicious).
3.  **Backend Server** processes request (if needed).
4.  **Reverse Proxy** receives response, may **cache, compress, transform**.
5.  **Client** receives response from reverse proxy.

**4. Common Reverse Proxy Software**
*   **Nginx:** Most popular, high performance, rich feature set.
*   **Apache HTTP Server** (with `mod_proxy`): Mature, flexible.
*   **HAProxy:** Specialized for load balancing, very fast.
*   **Traefik:** Cloud-native, automatic service discovery.
*   **Caddy:** Automatic HTTPS, simple configuration.

**5. Reverse Proxy vs. Related Concepts**
*   **vs. Forward Proxy:**
    *   **Reverse:** Client-side unknown, server-side known.
    *   **Forward:** Client-side known, server-side unknown.
*   **vs. Load Balancer:**
    *   **Load Balancer** is a **subset** of reverse proxy functionality.
    *   All L7 load balancers are reverse proxies, but not all reverse proxies are load balancers.
*   **vs. API Gateway:**
    *   **API Gateway** is a **specialized reverse proxy** for APIs.
    *   Adds API-specific features: composition, transformation, rate limiting.

**6. Deployment Architectures**

**A. Single Reverse Proxy**
*   Simple but creates **single point of failure**.
*   Suitable for small applications.

**B. Multiple Reverse Proxies with Load Balancer**
*   Reverse proxies behind a **load balancer** (L4 or L7).
*   Provides **high availability and scalability**.

**C. Edge Reverse Proxy (CDN)**
*   Reverse proxies at **edge locations globally**.
*   Combines with **CDN** for static content delivery.

**7. Configuration Considerations**
*   **Timeouts:** Connection, read, write timeouts to prevent resource exhaustion.
*   **Buffer Sizes:** Optimize for expected request/response sizes.
*   **Connection Pooling:** Reuse connections to backend servers.
*   **Logging & Monitoring:** Centralized logs, metrics collection.
*   **SSL/TLS Configuration:** Cipher suites, protocols, certificate management.

**8. Use Cases**
*   **Web Applications:** Serve dynamic content with caching/compression.
*   **Microservices:** Route requests to appropriate services.
*   **Legacy Application Modernization:** Add HTTPS, compression to old apps.
*   **A/B Testing:** Route traffic to different backend versions.
*   **Blue-Green Deployments:** Switch traffic between versions.

**9. Performance Impact**
*   **Adds Latency:** Extra network hop and processing.
*   **Reduces Backend Load:** Offloads SSL, compression, caching.
*   **Net Positive:** Typically improves **overall performance** despite added latency.

---

#### **Summary / Key Takeaways for Interview:**

*   **Server-Side Intermediary:** Reverse proxy sits **in front of backend servers**, representing them to clients. Clients are **unaware** of backend infrastructure.
*   **Multifunctional Tool:** Provides **load balancing, SSL termination, caching, security, compression, and transformation** in one component.
*   **Performance vs. Complexity Trade-off:** Adds **slight latency** but provides **significant performance benefits** through caching, compression, and connection pooling.
*   **Critical for Modern Web Architecture:** Essential for **scalability, security, and maintainability** of web applications.
*   **Foundation for Advanced Patterns:** Basis for **API Gateways, Service Meshes, and CDNs**.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"Explain what a reverse proxy is and its main benefits."** → A **server-side intermediary** that sits between clients and backend servers. **Benefits:** 1) **Load balancing** across servers, 2) **SSL termination** (offloads crypto), 3) **Caching** static content, 4) **Security** (hides servers, DDoS protection), 5) **Compression** (Gzip/Brotli), 6) **Request transformation**.
*   **"How does SSL termination work in a reverse proxy?"** → The reverse proxy **terminates TLS/SSL connections** from clients: 1) Client establishes **HTTPS connection** with reverse proxy, 2) Proxy **decrypts** traffic, 3) Forwards **plain HTTP** to backend servers, 4) Receives HTTP response, **encrypts**, sends back to client. **Benefits:** Centralized cert management, backend simplicity, performance offload.
*   **"What's the difference between a reverse proxy and a load balancer?"** → **Load balancer** is primarily for **distributing traffic** across servers. **Reverse proxy** includes load balancing **plus additional features**: SSL termination, caching, compression, security, URL rewriting. **All L7 load balancers are reverse proxies**, but reverse proxies can do more than just load balancing.
*   **"When would you use multiple reverse proxies?"** → For: 1) **High availability** (avoid SPOF), 2) **Geographic distribution** (edge locations), 3) **Scalability** (handle more traffic), 4) **Separation of concerns** (different proxies for different services). Typically deployed behind a **load balancer** that distributes traffic to them.
*   **"How does a reverse proxy improve security?"** → 1) **Hides backend servers** (origin IPs not exposed), 2) **DDoS protection** (absorbs attack traffic), 3) **WAF capabilities** (filter malicious requests), 4) **Rate limiting** (prevent abuse), 5) **IP filtering** (allow/deny lists), 6) **SSL/TLS best practices** centralized enforcement.

### **Reverse Proxy vs. Load Balancer**

**Source:** UpGuard Blog | [Reverse Proxy vs. Load Balancer](https://www.upguard.com/blog/reverse-proxy-vs-load-balancer)

**Main Idea:** While **reverse proxies** and **load balancers** both sit between clients and servers and share overlapping functionality, they have **distinct primary purposes**. A **reverse proxy** focuses on **securing and optimizing traffic** for backend servers, while a **load balancer** focuses on **distributing traffic** across multiple servers for scalability and availability.

---

#### **Notes: Core Definitions & Comparisons**

**1. Reverse Proxy: The Security & Optimization Gateway**
*   **Primary Role:** **Represents backend servers** to clients, acting as an **intermediary for security and performance**.
*   **Key Functions:**
    *   **Security:** Hides backend servers, DDoS protection, WAF.
    *   **SSL Termination:** Handles HTTPS encryption/decryption.
    *   **Caching:** Stores static content to reduce backend load.
    *   **Compression:** Gzip/Brotli compression of responses.
    *   **Content Routing:** Can route based on URL paths.
    *   **Protocol Translation:** HTTP/1.1 to HTTP/2, WebSocket support.
*   **Client Perspective:** Clients think they're communicating directly with the application.
*   **Typical Use:** **Single backend** or **multiple backends with basic routing**.

**2. Load Balancer: The Traffic Distributor**
*   **Primary Role:** **Distributes client requests** across multiple backend servers to optimize resource utilization.
*   **Key Functions:**
    *   **Traffic Distribution:** Using algorithms (round robin, least connections).
    *   **Health Checking:** Monitor servers and remove unhealthy ones.
    *   **Session Persistence:** Maintain sticky sessions.
    *   **High Availability:** Failover between servers.
*   **Focus:** **Scalability** and **availability** through distribution.
*   **Typical Use:** **Multiple identical backend servers** that need balanced load.

**3. Key Differences at a Glance**
| **Aspect** | **Reverse Proxy** | **Load Balancer** |
|------------|-------------------|-------------------|
| **Primary Goal** | Security, optimization, abstraction | Distribution, scalability, availability |
| **Backend Servers** | Can be single or multiple, heterogeneous | Typically multiple, identical/homogeneous |
| **Content Awareness** | Layer 7 (application layer) | Layer 4 (transport) or Layer 7 |
| **SSL Handling** | Terminates SSL (decrypts traffic) | May pass through SSL or terminate |
| **Caching** | Yes (static content) | Usually no (focus is distribution) |
| **Typical Deployment** | In front of application server(s) | In front of server farm/cluster |

**4. Overlap & Confusion Areas**
*   **Modern Tools Blur the Lines:** Many tools (Nginx, HAProxy) perform **both functions**.
*   **Layer 7 Load Balancers** are essentially **reverse proxies with load balancing**.
*   **Reverse Proxy as Simple Load Balancer:** Can do basic round-robin distribution.
*   **Key Distinction:** **Intent and feature emphasis** rather than absolute capability.

**5. When to Use Each (Decision Guide)**

**Use a Reverse Proxy When:**
*   You need **SSL termination** for HTTPS.
*   **Caching static content** is important.
*   **Security features** (WAF, DDoS protection) are needed.
*   You want to **hide backend server details**.
*   **Compression or content transformation** is required.
*   You have a **single backend** but need these features.

**Use a Load Balancer When:**
*   **Horizontal scaling** across multiple servers is the primary need.
*   **High availability** through failover is critical.
*   **Traffic distribution algorithms** (least connections, etc.) are needed.
*   You have **identical backend servers** requiring balanced load.
*   **Session persistence** across requests is needed.

**Use Both When:**
*   You need **security/optimization + distribution**.
*   Typical architecture: **Load balancer → Multiple reverse proxies → Backend servers**.

**6. Technical Implementation Differences**
*   **Reverse Proxy Configuration:** Focus on **caching rules, SSL certificates, security policies, rewrite rules**.
*   **Load Balancer Configuration:** Focus on **server pools, health check parameters, distribution algorithms, session persistence methods**.
*   **Performance Characteristics:** Reverse proxies add **more processing** (SSL, caching, compression) but reduce backend load. Load balancers focus on **efficient distribution** with minimal overhead.

**7. Real-World Scenarios**

**Scenario 1: E-commerce Website**
*   **Reverse Proxy:** Handles SSL, caches product images, compresses responses, provides WAF.
*   **Load Balancer:** Distributes traffic across multiple application servers.

**Scenario 2: API Service**
*   **Reverse Proxy:** Rate limiting, authentication, request logging.
*   **Load Balancer:** Distributes across API server instances.

**Scenario 3: Legacy Application Modernization**
*   **Reverse Proxy:** Adds HTTPS to HTTP-only legacy app, provides caching.
*   (No load balancer needed if single server).

**8. Security Implications**
*   **Reverse Proxy Security Benefits:**
    *   Hides backend infrastructure.
    *   Centralizes security policies.
    *   Provides DDoS mitigation.
    *   Enables WAF capabilities.
*   **Load Balancer Security:**
    *   Can provide **DDoS protection** at network layer.
    *   **SSL passthrough** keeps encryption end-to-end.
    *   **Health checks** prevent routing to compromised servers.

**9. Common Tools & Their Primary Orientation**
*   **Primarily Reverse Proxy:** Nginx (though does LB), Apache `mod_proxy`, Caddy.
*   **Primarily Load Balancer:** F5 BIG-IP LTM, Citrix ADC, AWS Classic Load Balancer.
*   **Hybrid/Both:** HAProxy, Nginx Plus, Traefik, Envoy.

---

#### **Summary / Key Takeaways for Interview:**

*   **Purpose Distinction:** **Reverse proxy = security/optimization**, **Load balancer = distribution/availability**. This is the **key differentiator**.
*   **Not Mutually Exclusive:** Modern tools often provide **both capabilities**. The distinction is about **primary design intent** and **feature emphasis**.
*   **Layer Awareness:** Reverse proxies operate at **Layer 7** (application). Load balancers can be **Layer 4** (faster, simpler) or **Layer 7** (more intelligent).
*   **Backend Requirements:** Load balancers typically require **identical servers**; reverse proxies can front **heterogeneous or single backends**.
*   **Common Pattern:** **Load balancer distributes to multiple reverse proxies** which then handle security/optimization before reaching application servers.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"What's the main difference between a reverse proxy and a load balancer?"** → **Reverse proxy** focuses on **security, optimization, and abstraction** (SSL termination, caching, WAF). **Load balancer** focuses on **traffic distribution and availability** (spreading load across servers, health checks, failover). A Layer 7 load balancer **includes** reverse proxy capabilities.
*   **"When would you use a reverse proxy without a load balancer?"** → When you have a **single backend server** but need: 1) **SSL termination**, 2) **Caching** of static assets, 3) **Security features** (WAF, DDoS protection), 4) **Compression**, 5) **URL rewriting**. Common for legacy app modernization or small applications.
*   **"Can a single component be both a reverse proxy and load balancer?"** → **Yes, absolutely.** Tools like **Nginx and HAProxy** perform both roles. Nginx as a **reverse proxy** provides caching, SSL termination; as a **load balancer** it distributes traffic using algorithms. The distinction is often in **configuration emphasis** rather than capability.
*   **"How do SSL handling differ between the two?"** → **Reverse proxy:** Typically **terminates SSL** (decrypts, inspects content, may re-encrypt to backend). **Load balancer:** May do **SSL termination** (L7 LB) or **SSL passthrough** (L4 LB - forwards encrypted traffic unchanged). Reverse proxy uses SSL termination for content inspection; load balancer may preserve end-to-end encryption.
*   **"Describe an architecture using both components."** → **Internet → Load Balancer (AWS ALB) → Multiple Reverse Proxies (Nginx) → Application Servers**. The **load balancer** distributes traffic across availability zones, provides health checks. The **reverse proxies** handle SSL termination, static file caching, compression, security headers. This combines **distribution scalability** with **application optimization**.