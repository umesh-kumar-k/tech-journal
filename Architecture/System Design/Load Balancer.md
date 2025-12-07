
### **Cornell Notes: Load Balancing System Design Interview**

**Source:** I Got An Offer Tech Blog | [Load Balancing System Design Interview](https://igotanoffer.com/blogs/tech/load-balancing-system-design-interview)

**Main Idea:** Load balancing is a **fundamental distributed systems concept** that distributes incoming network traffic across multiple backend servers to ensure **high availability, reliability, and scalability**. Understanding load balancing architectures, algorithms, and failure handling is essential for system design interviews.

---

#### **Notes: Core Concepts & Architectures**

**1. What is Load Balancing & Why It Matters**
*   **Definition:** A technique to distribute incoming client requests across multiple backend servers.
*   **Key Goals:**
    *   **High Availability:** No single point of failure.
    *   **Scalability:** Handle increased traffic by adding servers.
    *   **Performance:** Optimal resource utilization, reduced latency.
    *   **Maintainability:** Zero-downtime deployments, health monitoring.
*   **Without Load Balancer:** Single server becomes bottleneck/SPOF; difficult to scale.

**2. Load Balancing Architectures**
*   **Single Load Balancer:**
    *   Simple but creates **new SPOF** (load balancer itself).
    *   **Solution:** Use **floating IPs (VIP)** with failover (active-passive).
*   **Multiple Load Balancers:**
    *   **Active-Passive:** One primary, one standby (failover).
    *   **Active-Active:** All handle traffic (better utilization).
    *   Requires **session synchronization** for stateful LBs.
*   **Load Balancer Placement:**
    *   **On-premise:** Hardware load balancers (F5, Citrix).
    *   **Cloud:** Managed services (AWS ELB/ALB, GCP Load Balancing).
    *   **Software:** HAProxy, Nginx, Envoy (more flexible).

**3. Critical Load Balancing Algorithms**
*   **Round Robin:**
    *   Rotate requests evenly across servers.
    *   **Simple but naive** (ignores server load, capacity).
*   **Weighted Round Robin:**
    *   Assign weights based on server capacity.
    *   Higher capacity servers get more requests.
*   **Least Connections:**
    *   Route to server with **fewest active connections**.
    *   Better for uneven request durations.
*   **Weighted Least Connections:**
    *   Combines weights with connection count.
*   **Least Response Time:**
    *   Route to server with **lowest latency + active connections**.
    *   Best performance but more overhead.
*   **IP Hash / Consistent Hashing:**
    *   Client IP determines server assignment.
    *   Enables **session persistence** (sticky sessions).
    *   Critical for stateful applications.

**4. Layer 4 vs Layer 7 Load Balancing**
*   **Layer 4 (Transport Layer - TCP/UDP):**
    *   **Operation:** Forwards packets based on **IP + port**.
    *   **Pros:** Faster, lower overhead, simpler.
    *   **Cons:** No content awareness, limited routing decisions.
    *   **Use Cases:** Database load balancing, gaming, VoIP.
*   **Layer 7 (Application Layer - HTTP/HTTPS):**
    *   **Operation:** Inspects **HTTP headers/content**, makes intelligent routing.
    *   **Pros:** Content-based routing, SSL termination, caching, compression.
    *   **Cons:** Higher overhead, more complex.
    *   **Use Cases:** Web applications, API gateways, microservices.

**5. Health Checking & Failure Handling**
*   **Health Checks:** Periodic probes to determine server health.
    *   **Active:** LB sends requests (HTTP, TCP) to servers.
    *   **Passive:** Monitor response times, error rates.
*   **Failure Scenarios & Solutions:**
    *   **Server Failure:** Health check fails → remove from pool.
    *   **Load Balancer Failure:** Floating IP failover, DNS failover.
    *   **Data Center Failure:** Geo-DNS, multi-region load balancing.
*   **Graceful Degradation:** Serve reduced functionality rather than complete failure.

**6. Session Persistence (Sticky Sessions)**
*   **Problem:** User sessions stored on specific servers.
*   **Solutions:**
    *   **IP Hash:** Same client IP → same server (breaks with proxies/NAT).
    *   **Cookies:** LB injects session cookie with server ID.
    *   **Application-Level:** Store session in shared cache (Redis) → stateless servers.
*   **Trade-off:** Sticky sessions reduce load balancing flexibility.

**7. Horizontal vs. Vertical Scaling with Load Balancers**
*   **Horizontal Scaling (Scale Out):** Add more servers behind LB.
    *   **Pros:** Infinite scale, fault tolerance, cost-effective.
    *   **Cons:** Requires distributed systems design.
*   **Vertical Scaling (Scale Up):** Upgrade server resources.
    *   **Pros:** Simpler application design.
    *   **Cons:** Physical limits, single point of failure.
*   **Modern Approach:** **Horizontal scaling** with load balancers.

**8. Load Balancer as a Single Point of Failure**
*   **Problem:** The load balancer itself can fail.
*   **Solutions:**
    1.  **Active-Passive with Floating IP:** VIP moves to standby.
    2.  **Active-Active with DNS Round Robin:** Multiple LB IPs in DNS.
    3.  **Anycast:** Same IP on multiple LBs, BGP routes to nearest.
    4.  **Cloud Managed Services:** AWS ELB, GCP LB (automatically HA).

**9. Advanced Topics for Interviews**
*   **Global Server Load Balancing (GSLB):** Distribute traffic across data centers.
*   **Autoscaling Integration:** LB works with cloud autoscaling (add/remove instances).
*   **Microservices Load Balancing:** Service mesh (Istio) for east-west traffic.
*   **SSL Termination:** Offload encryption/decryption to LB.
*   **Caching:** LB can cache responses (reduce backend load).

---

#### **Summary / Key Takeaways for Interview:**

*   **Load Balancing is Fundamental Infrastructure:** Enables **horizontal scaling, high availability, and fault tolerance** by distributing traffic across servers.
*   **Algorithm Choice Depends on Use Case:** **Round robin** for simplicity, **least connections** for uneven workloads, **IP hash** for session persistence, **least response time** for performance.
*   **L4 vs L7 Trade-off:** **L4 is faster** (packet level), **L7 is smarter** (application aware). Modern web apps typically use **L7** for advanced routing.
*   **Health Checks are Non-Negotiable:** Without health monitoring, LB will route traffic to dead servers. **Active + passive** health checks recommended.
*   **Design for LB Failure:** The load balancer itself must be **highly available** (active-active, anycast, cloud managed). Don't create a new SPOF.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"Explain how you would design a load balancing system for a web application."** → 1) **Multiple web servers** behind load balancer, 2) **L7 LB** (HAProxy/Nginx) for HTTP routing, 3) **Health checks** (HTTP 200), 4) **Session persistence** via cookies or shared Redis, 5) **Auto-scaling group** integration, 6) **Multiple LB instances** active-active with floating IP/DNS.
*   **"What's the difference between Layer 4 and Layer 7 load balancing?"** → **L4:** Operates at TCP/UDP, sees only **IP/port**, faster, simpler. **L7:** Operates at HTTP, sees **URL, headers, cookies**, can do content-based routing, SSL termination, caching. L4 for raw performance, L7 for intelligent routing.
*   **"How do you handle session persistence in a load balanced environment?"** → **Three approaches:** 1) **Sticky sessions** (IP hash or cookies) - simpler but less flexible, 2) **Shared session store** (Redis/Memcached) - stateless servers, better for scaling, 3) **Client-side sessions** (JWT tokens) - most scalable. Recommend **shared store** for modern apps.
*   **"What happens when a load balancer fails?"** → **Solutions:** 1) **Active-passive failover** with floating VIP, 2) **DNS failover** to backup LB IP (slow propagation), 3) **Anycast routing** (multiple LBs share IP), 4) **Cloud managed LBs** (AWS ELB) handle HA automatically. **Best:** Active-active with health monitoring.
*   **"When would you choose round robin vs least connections?"** → **Round robin** when: servers have equal capacity, request processing time is similar, simple distribution needed. **Least connections** when: request duration varies (some long, some short), server capacities differ, you want more even load distribution based on actual utilization.

### **Network System Design Cheat Sheet (Load Balancer, Proxies, API Gateway)**

**Source:** HackerNoon | [The Network System Design Cheat Sheet](https://hackernoon.com/the-network-system-design-cheat-sheet-load-balancer-reverse-proxy-forward-proxy-api-gateway)

**Main Idea:** Understanding the **differences and use cases** for Load Balancer, Reverse Proxy, Forward Proxy, and API Gateway is crucial for system design. These networking components have **distinct purposes, operate at different layers, and solve different problems** in distributed systems architecture.

---

#### **Notes: Core Components & Comparisons**

**1. Forward Proxy (Client-Side Proxy)**
*   **Position:** Sits **between clients and the internet**.
*   **Primary Purpose:** **Represents clients** to external servers.
*   **Key Functions:**
    *   **Anonymity:** Hides client IP from destination.
    *   **Access Control:** Block restricted sites.
    *   **Caching:** Cache frequent requests.
    *   **Logging:** Monitor client internet usage.
    *   **Bypassing Restrictions:** Access geo-blocked content.
*   **Client Awareness:** Clients **must configure** proxy settings.
*   **Examples:** Squid, Charles Proxy, corporate firewalls, VPNs.
*   **Use Case:** Corporate networks controlling employee internet access.

**2. Reverse Proxy (Server-Side Proxy)**
*   **Position:** Sits **between clients and backend servers**.
*   **Primary Purpose:** **Represents servers** to clients.
*   **Key Functions:**
    *   **Load Distribution:** Distribute requests to backend servers.
    *   **SSL Termination:** Handle HTTPS encryption/decryption.
    *   **Caching:** Cache static content.
    *   **Compression:** Gzip/Brotli compression.
    *   **Security:** Hide backend servers, DDoS protection, WAF.
    *   **Unified Access Point:** Single entry point for multiple services.
*   **Client Awareness:** Clients **don't know** about backend servers.
*   **Examples:** Nginx, Apache HTTP Server (as reverse proxy), HAProxy.
*   **Use Case:** Web application frontend handling multiple backend services.

**3. Load Balancer**
*   **Position:** Typically sits **between clients and multiple identical servers**.
*   **Primary Purpose:** **Distribute traffic** across servers to optimize resource use.
*   **Key Functions:**
    *   **Traffic Distribution:** Using algorithms (round robin, least connections).
    *   **Health Checking:** Monitor server health, remove unhealthy.
    *   **Session Persistence:** Sticky sessions for stateful apps.
    *   **Failover:** Route away from failed servers.
*   **Types:**
    *   **Layer 4 (L4):** TCP/UDP level (faster, less intelligent).
    *   **Layer 7 (L7):** HTTP level (can inspect content).
*   **Examples:** AWS ELB/ALB, F5 BIG-IP, Citrix ADC, Nginx (as LB).
*   **Use Case:** Scaling web servers horizontally.

**4. API Gateway**
*   **Position:** Sits **between clients and backend microservices**.
*   **Primary Purpose:** **API management and composition**.
*   **Key Functions:**
    *   **Request Routing:** Route to appropriate microservice.
    *   **API Composition:** Aggregate multiple service responses.
    *   **Protocol Translation:** REST to gRPC, WebSocket to HTTP, etc.
    *   **Authentication/Authorization:** JWT validation, OAuth.
    *   **Rate Limiting & Throttling:** Protect backend services.
    *   **Monitoring & Analytics:** API usage metrics.
    *   **Versioning:** Manage multiple API versions.
*   **Client Awareness:** Clients see **unified API interface**.
*   **Examples:** Kong, Apigee, AWS API Gateway, Azure API Management.
*   **Use Case:** Microservices architecture with many backend services.

**5. Comparison Matrix**
| **Component** | **Primary Role** | **Position** | **Layer** | **Client Config** | **Key Differentiator** |
|---------------|------------------|--------------|-----------|-------------------|------------------------|
| **Forward Proxy** | Represents clients | Client-side | L7 | Required | Hides client identity |
| **Reverse Proxy** | Represents servers | Server-side | L7 | Not required | Single entry point, security |
| **Load Balancer** | Distribute traffic | Server-side | L4/L7 | Not required | Focus on equal distribution, health checks |
| **API Gateway** | API management | Server-side | L7 | Not required | API composition, transformation, rate limiting |

**6. Overlap & Confusion Areas**
*   **Reverse Proxy vs Load Balancer:**
    *   **All L7 load balancers are reverse proxies**, but not all reverse proxies are load balancers.
    *   Reverse proxy can **terminate SSL, cache, compress** (more features).
    *   Load balancer focuses on **distribution algorithms, health checks**.
*   **Reverse Proxy vs API Gateway:**
    *   API Gateway is a **specialized reverse proxy** for APIs.
    *   API Gateway has **API-specific features** (composition, transformation, versioning).
    *   Reverse proxy is more **general-purpose**.
*   **Modern Tools:** Many tools (Nginx, Envoy) can perform **multiple roles**.

**7. Real-World Architecture Example**
```
Clients → [Forward Proxy] → Internet → [Load Balancer] → [Reverse Proxy] → [API Gateway] → [Microservices]
          (Corporate)                   (AWS ELB)         (Nginx)           (Kong)          (Backend)
```
*   **Forward Proxy:** Corporate network filtering.
*   **Load Balancer:** Distributes traffic across reverse proxy instances.
*   **Reverse Proxy:** SSL termination, static file serving, compression.
*   **API Gateway:** Routes to specific microservices, handles auth.

**8. Design Considerations**
*   **Performance:** L4 load balancers faster than L7 (less inspection).
*   **Complexity:** API Gateway > Reverse Proxy > Load Balancer > Forward Proxy.
*   **Security:** Each provides different security layers (WAF, DDoS, auth).
*   **Scalability:** Load balancer enables horizontal scaling.
*   **Vendor Lock-in:** Cloud-managed vs. self-hosted trade-offs.

---

#### **Summary / Key Takeaways for Interview:**

*   **Direction Matters:** **Forward proxy** = client-side (outbound), **Reverse proxy/LB/API Gateway** = server-side (inbound).
*   **Purpose Distinction:** **Load balancer** distributes traffic, **Reverse proxy** provides features (SSL, caching), **API Gateway** manages APIs, **Forward proxy** controls client access.
*   **Layer Awareness:** **L4** (faster, simpler) vs **L7** (smarter, more features). Most modern proxies operate at L7.
*   **Evolutionary Relationship:** API Gateway evolved from reverse proxy which evolved from load balancer—**increasing specialization**.
*   **Often Combined in Practice:** A single component (like Nginx) can serve multiple roles, but understanding the conceptual differences is key for design decisions.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"What's the difference between a reverse proxy and a load balancer?"** → **Reverse proxy** is a **server-side intermediary** that can perform SSL termination, caching, compression, and security. **Load balancer** specifically **distributes traffic** across backend servers using algorithms. **All L7 load balancers are reverse proxies**, but reverse proxies may not distribute load (could front a single server).
*   **"When would you use an API Gateway vs a reverse proxy?"** → Use **API Gateway** when: 1) **Microservices architecture** with many services, 2) Need **API composition/aggregation**, 3) Require **advanced API management** (rate limiting, versioning, transformation). Use **reverse proxy** when: 1) **Simpler routing** needs, 2) **SSL termination/caching** for monolith, 3) **Basic request forwarding** without API-specific features.
*   **"Explain forward proxy with a real-world example."** → **Forward proxy** sits on **client side**, e.g., corporate network where all employee internet traffic goes through a proxy server that: 1) **Blocks inappropriate sites**, 2) **Caches frequent requests** (YouTube videos), 3) **Logs all traffic**, 4) **Hides employee IPs** from external sites. Employees must configure browser proxy settings.
*   **"How do these components work together in a microservices architecture?"** → **External clients** → **Load balancer** (distributes across API Gateway instances) → **API Gateway** (routes, auth, rate limits) → **Service mesh sidecar** (service-to-service LB) → **Microservices**. Reverse proxy might sit before API Gateway for SSL termination/static files.
*   **"What are the trade-offs between L4 and L7 load balancing?"** → **L4:** Faster (less inspection), simpler, works for non-HTTP traffic (database, gaming). **L7:** Smarter (content-aware routing), can do SSL termination, header manipulation, but slower (more CPU), more complex. Choose **L4** for raw performance, **L7** for HTTP-specific features.

### **Types of Load Balancing Algorithms**

**Source:** Cloudflare Learning | [Types of Load Balancing Algorithms](https://www.cloudflare.com/en-gb/learning/performance/types-of-load-balancing-algorithms/)

**Main Idea:** Load balancing algorithms determine **how traffic is distributed** across backend servers. Different algorithms optimize for different goals—**fairness, performance, session persistence, or geographic efficiency**—and the choice depends on application characteristics, traffic patterns, and infrastructure.

---

#### **Notes: Core Load Balancing Algorithms**

**1. Static Algorithms (Configuration-Based)**
*   **Definition:** Distribution decisions made **without considering current server state**.
*   **Use Cases:** Simple environments, predictable workloads.

**A. Round Robin**
*   **How it works:** Requests distributed **sequentially** across server list.
*   **Example:** Server A → B → C → A → B → C...
*   **Pros:** Simple, predictable, evenly distributed over time.
*   **Cons:** Ignores server capacity, load, connection count.
*   **Best for:** Servers with **identical specifications**, similar request processing times.

**B. Weighted Round Robin**
*   **How it works:** Each server assigned a **weight** (higher weight = more requests).
*   **Example:** Server A (weight 3), B (weight 2), C (weight 1): A-A-A-B-B-C pattern.
*   **Pros:** Accounts for differing server capacities.
*   **Cons:** Doesn't consider real-time load.
*   **Best for:** **Heterogeneous servers** (some more powerful than others).

**C. IP Hash**
*   **How it works:** Client IP determines server assignment via **hash function**.
*   **Pros:** Guarantees **session persistence** (same client → same server).
*   **Cons:** Poor load distribution if client IPs uneven (e.g., large NATs).
*   **Best for:** Stateful applications requiring **sticky sessions**.

**2. Dynamic Algorithms (State-Aware)**
*   **Definition:** Consider **current server state** (load, response time, connections).
*   **Use Cases:** Variable workloads, performance optimization.

**A. Least Connections**
*   **How it works:** Routes to server with **fewest active connections**.
*   **Pros:** Accounts for current load, good for varying request durations.
*   **Cons:** Doesn't consider server capacity or request complexity.
*   **Best for:** **Variable session lengths** (some requests take seconds, others minutes).

**B. Weighted Least Connections**
*   **How it works:** Combines **server weights** with **connection count**.
*   **Calculation:** (Active connections) ÷ (Weight) → choose lowest.
*   **Pros:** Accounts for both capacity and current load.
*   **Cons:** More complex to configure.
*   **Best for:** **Heterogeneous servers** with variable loads.

**C. Least Response Time**
*   **How it works:** Routes to server with **lowest response time + active connections**.
*   **Pros:** Optimizes for **performance** (fastest user experience).
*   **Cons:** Requires active monitoring, more overhead.
*   **Best for:** **Latency-sensitive applications** (gaming, real-time apps).

**D. Least Bandwidth**
*   **How it works:** Routes to server consuming **least bandwidth** (Mbps).
*   **Pros:** Balances network utilization.
*   **Cons:** Doesn't consider compute load.
*   **Best for:** **Bandwidth-intensive applications** (video streaming, large downloads).

**3. Geographic & Specialized Algorithms**

**A. Geographic Load Balancing (GeoDNS)**
*   **How it works:** Directs traffic based on **user's geographic location**.
*   **Implementation:** DNS-based, returns different IPs by region.
*   **Use Cases:** CDNs, global applications, data sovereignty requirements.

**B. Anycast**
*   **How it works:** Same IP announced from multiple locations; **BGP routes to nearest**.
*   **Pros:** Automatic failover, low latency.
*   **Cons:** Not true load balancing (just geographic routing).
*   **Use Cases:** DNS root servers, CDNs (Cloudflare uses anycast).

**4. Health Checking & Failover**
*   **Critical Companion:** All algorithms need **health checks** to avoid routing to failed servers.
*   **Types:** Passive (monitor responses) vs. Active (send probes).
*   **Failover:** Automatic removal/addition of servers based on health.

**5. Algorithm Selection Guide**
| **Scenario** | **Recommended Algorithm** |
|--------------|--------------------------|
| **Identical servers, simple needs** | Round Robin |
| **Different server capacities** | Weighted Round Robin |
| **Stateful applications** | IP Hash |
| **Variable request durations** | Least Connections |
| **Mixed capacity + variable load** | Weighted Least Connections |
| **Performance-critical apps** | Least Response Time |
| **Global user base** | Geographic/Anycast |
| **Bandwidth-heavy traffic** | Least Bandwidth |

**6. Advanced Considerations**
*   **Session Persistence:** Cookies can supplement IP hash for better sticky sessions.
*   **Tiered Load Balancing:** Use **different algorithms at different layers** (GeoDNS → L7 LB → L4 LB).
*   **Cloud Integration:** Auto-scaling groups often use least connections.
*   **Microservices:** Service meshes use more sophisticated algorithms with circuit breakers.

---

#### **Summary / Key Takeaways for Interview:**

*   **Static vs Dynamic:** **Static algorithms** (round robin, IP hash) are simple but ignore current state. **Dynamic algorithms** (least connections, response time) adapt to real-time conditions.
*   **Matching Algorithm to Use Case:** The "best" algorithm depends entirely on **application characteristics**—stateful vs stateless, server homogeneity, request patterns, performance requirements.
*   **Health Checks are Essential:** No algorithm works properly without **health monitoring** to avoid routing to failed servers.
*   **Hybrid Approaches are Common:** Real systems often use **multiple algorithms in combination** (GeoDNS for geographic routing → least connections within data center).
*   **Evolution:** Modern load balancers often support **multiple algorithms** and can switch based on conditions.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"Compare round robin and least connections load balancing."** → **Round robin:** Simple sequential distribution, **ignores server load**, good for identical servers. **Least connections:** Routes to server with fewest active connections, **adapts to current load**, better for variable request durations. Round robin is **fair over time**; least connections is **responsive in real-time**.
*   **"When would you use IP hash load balancing?"** → When you need **session persistence/sticky sessions**—stateful applications where user session data is stored locally on a server. Examples: Shopping carts, multi-step forms, some gaming sessions. **Warning:** IP hash performs poorly if clients are behind large NATs (many users share IP).
*   **"How does weighted least connections work with servers of different capacities?"** → Example: Server A (weight 4, 8 connections), Server B (weight 2, 3 connections). Calculation: A = 8/4 = 2.0, B = 3/2 = 1.5 → route to B (lower score). The **weight represents capacity**, so more powerful servers handle more connections proportionally.
*   **"What algorithm would you choose for a video streaming service?"** → **Least bandwidth** or **weighted least connections**. Video streaming is **bandwidth-intensive with long-lived connections**. Least bandwidth ensures no single server gets saturated. Weighted least connections accounts for different server capacities while distributing long-lived connections evenly.
*   **"How do health checks interact with load balancing algorithms?"** → **Health checks override algorithm decisions.** If algorithm selects a server but health check marks it unhealthy, the load balancer **skips it** (tries next server or removes from pool). Health checks can be: **Active** (LB probes servers) or **Passive** (monitors response times/errors). Essential for all algorithms.

### **Layer 4 Load Balancing**

**Source:** F5 Glossary | [Layer 4 Load Balancing](https://www.f5.com/glossary/layer-4-load-balancing)

**Main Idea:** **Layer 4 load balancing** operates at the **transport layer** (TCP/UDP) of the OSI model, making routing decisions based on **IP addresses and port numbers** without inspecting application content. It provides **high-performance, low-latency traffic distribution** for scenarios where simple, fast packet forwarding is required.

---

#### **Notes: Core Concepts & Operation**

**1. What is Layer 4 Load Balancing?**
*   **OSI Layer:** **Transport Layer** (Layer 4) – TCP and UDP protocols.
*   **Decision Basis:** **Source/destination IP addresses and port numbers**.
*   **Visibility:** Sees **packets and connections**, not application data (HTTP headers, cookies, etc.).
*   **Analogy:** A **mail sorter** looking only at ZIP codes (not letter content).

**2. How Layer 4 Load Balancing Works**
*   **Packet Inspection:** Examines **TCP/UDP headers** (IPs + ports).
*   **Connection Tracking:** Maintains **session tables** to ensure packets from same connection go to same backend server.
*   **Network Address Translation (NAT):** Typically rewrites **destination IP/port** (and sometimes source) to forward to backend.
*   **Flow:** Client → L4 LB → NAT translation → Backend Server → L4 LB (reverse NAT) → Client.

**3. Key Characteristics**
*   **Performance:** **Very fast** – minimal packet inspection, low CPU overhead.
*   **Transparency:** Backend servers see **client's source IP** (if using Direct Server Return) or **LB's IP** (if using NAT).
*   **Protocol Agnostic:** Works for **any TCP/UDP** application (HTTP, DNS, FTP, gaming, VoIP).
*   **Stateless Routing:** Basic L4 LBs are connection-oriented but don't understand application state.

**4. Common Use Cases**
*   **High-Performance Requirements:** Where speed is critical (gaming, financial trading).
*   **Non-HTTP Traffic:** DNS, FTP, email (SMTP/IMAP), database connections.
*   **Simple Distribution:** When application-layer intelligence isn't needed.
*   **DDoS Mitigation:** Can absorb volumetric attacks at network layer.
*   **Global Server Load Balancing (GSLB):** Often implemented at L4 for data center failover.

**5. Load Balancing Methods at L4**
*   **Round Robin:** Sequential distribution.
*   **Weighted Round Robin:** Based on server capacity.
*   **Least Connections:** Based on active connection count.
*   **Fastest Response:** Based on server response times.
*   **Source IP Hash:** For session persistence based on client IP.

**6. Health Checking at Layer 4**
*   **TCP Connection Probes:** Attempt to establish TCP connection to backend port.
*   **UDP Echo Probes:** Send UDP packets and expect response.
*   **Limited Visibility:** Can't check **application health** (e.g., HTTP 200 status).

**7. Advantages Over Layer 7 Load Balancing**
*   **Lower Latency:** Faster packet processing.
*   **Lower Resource Usage:** Less CPU/memory.
*   **Broader Protocol Support:** Any TCP/UDP application.
*   **Simplicity:** Easier configuration and troubleshooting.

**8. Limitations Compared to Layer 7**
*   **No Application Awareness:** Cannot route based on URL, cookies, headers.
*   **Limited Persistence:** Only IP-based sticky sessions (breaks behind NATs).
*   **No Content Optimization:** Cannot compress, cache, or modify payload.
*   **No SSL Termination:** Cannot decrypt HTTPS (would need L7 or SSL offload device).
*   **Limited Security:** Cannot inspect for application-layer attacks (SQLi, XSS).

**9. Implementation Examples**
*   **Hardware:** F5 BIG-IP (LTM), Citrix ADC.
*   **Software:** **Linux Virtual Server (LVS)**, IPVS, HAProxy (can operate at L4).
*   **Cloud:** AWS Network Load Balancer (NLB), Google Cloud TCP/UDP Load Balancing.

**10. When to Choose Layer 4 vs Layer 7**
*   **Choose Layer 4 When:**
    *   Need **maximum performance** (low latency, high throughput).
    *   **Non-HTTP protocols** (gaming, VoIP, databases).
    *   **Simple distribution** without content-based routing.
    *   **DDoS protection** at network layer.
*   **Choose Layer 7 When:**
    *   Need **content-based routing** (URL, headers).
    *   **HTTP/HTTPS optimization** (compression, caching).
    *   **Advanced session persistence** (cookies, not just IP).
    *   **SSL termination** at load balancer.
    *   **Web application security** (WAF).

---

#### **Summary / Key Takeaways for Interview:**

*   **L4 = Network-Level Load Balancing:** Operates at **TCP/UDP layer**, routing based on **IPs and ports only**. **Fast and simple** but **application-blind**.
*   **Performance vs. Intelligence Trade-off:** L4 offers **better performance** (lower latency, less CPU) but **lacks application awareness** that L7 provides.
*   **Protocol Agnostic:** Works for **any TCP/UDP service** (not just HTTP). Ideal for **gaming, databases, VoIP, DNS**.
*   **Session Persistence Limitations:** Can only do **IP-based sticky sessions** (problematic with NATs, mobile networks).
*   **Modern Usage:** Often used as **first-tier load balancer** (handling traffic distribution) with **L7 load balancers behind** for application intelligence.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"What's the difference between Layer 4 and Layer 7 load balancing?"** → **L4:** Operates at **transport layer** (TCP/UDP), sees only **IPs and ports**, **faster**, protocol-agnostic. **L7:** Operates at **application layer** (HTTP/HTTPS), sees **content** (URLs, headers, cookies), **smarter routing** but slower. L4 = "where to send", L7 = "what to send where".
*   **"When would you choose Layer 4 load balancing?"** → Choose L4 for: 1) **Non-HTTP protocols** (database, gaming, VoIP), 2) **Maximum performance** requirements (lowest latency), 3) **Simple round-robin distribution** without content routing, 4) **DDoS mitigation** at network layer, 5) When you need **client IP preservation** (with DSR).
*   **"How does Layer 4 load balancing handle SSL/TLS traffic?"** → **It doesn't decrypt it.** L4 LB treats SSL/TLS as **opaque TCP traffic**. It can **forward encrypted packets** to backend servers based on IP/port, but cannot inspect content, terminate SSL, or route based on URL. For SSL termination, you need **L7 LB or dedicated SSL offload**.
*   **"What are the limitations of IP-based session persistence?"** → **Problems:** 1) **NATs** (multiple users share one IP), 2) **Mobile networks** (IP changes frequently), 3) **Corporate proxies**, 4) **IPv6 privacy extensions**, 5) **Load imbalance** if clients unevenly distributed. **Solution:** L7 persistence (cookies) or shared session store.
*   **"Give an example architecture combining L4 and L7 load balancing."** → **Front:** **L4 Load Balancer** (AWS NLB) handling **DDoS protection, TCP termination, high-throughput distribution** across regions. **Middle:** **L7 Load Balancer** (Nginx/HAProxy) performing **SSL termination, URL-based routing, header manipulation, caching**. **Back:** Application servers. This combines **L4 performance** with **L7 intelligence**.

