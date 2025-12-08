## NGINX Performance & Scale Interview Checklist

- **Core Design Goals**
    
    |Goal|Description|
    |---|---|
    |**High Concurrency**|Support thousands of simultaneous connections|
    |**Low Latency**|Minimal delay through efficient processing|
    |**Resource Efficiency**|Low CPU & memory usage under load|
    |**Scalability**|Scale horizontally & vertically|
    
- **Architectural Highlights**
    
    |Feature|Benefit|
    |---|---|
    |**Event-Driven, Asynchronous Model**|Efficiently handles many connections without blocking|
    |**Master-Worker Process Model**|Allows seamless zero-downtime reloads and fault isolation|
    |**Non-blocking I/O**|Avoids resource depletion during high load|
    |**Modular Design**|Extensions & protocol support without performance degradation|
    
- **Performance Techniques**
    
    |Technique|Description|
    |---|---|
    |**Connection Pooling**|Reuse connections to backend servers|
    |**Caching**|Static & dynamic content caching at proxy layer|
    |**Load Balancing Algorithms**|Round robin, least connections, IP hashing|
    |**TLS Offloading**|Terminate SSL/TLS at NGINX to relieve backend|
    
- **Scalability Strategies**
    
    |Strategy|Example|
    |---|---|
    |**Horizontal Scaling**|Multiple NGINX instances with DNS or LB in front|
    |**Vertical Scaling**|Multi-worker processes matching CPU cores|
    |**Load Distribution**|Coordinated through upstream modules|
    |**Health Checks & Failover**|Dynamic routing to healthy backends|
    
- **Common Tools & Ecosystem**
    
    |Category|Tool/Feature|
    |---|---|
    |**Load Balancers**|NGINX Plus, open source NGINX|
    |**Monitoring**|NGINX Amplify, Prometheus exporters|
    |**Integration**|Lua modules, HTTP/2, gRPC support|
    |**Container-ready**|Kubernetes Ingress controllers with NGINX|
    
- **Real-World Usage**
    
    - Used by Netflix, Dropbox, Zynga (350+ million sites worldwide)
        
    - Powers content delivery, API gateways, microservice proxies
        
    - Supports both edge and internal load balancing
        

## 60-Second Recap

- **NGINX:** Event-driven, asynchronous worker model for massive concurrency.
    
- **Performance:** Non-blocking I/O + connection pooling + caching + TLS offloading.
    
- **Scaling:** Multi-worker per CPU + scale horizontally with DNS/LB.
    
- **Ecosystem:** NGINX Plus, Amplify monitoring, Lua extensibility, Kubernetes ingress.
    
- **Gold:** Efficient, modular, battle-tested at Netflix & Dropbox scale.
    

**Reference**: [Inside NGINX: Performance & Scale](https://blog.nginx.org/blog/inside-nginx-how-we-designed-for-performance-scale/)[aws.amazon](https://aws.amazon.com/compare/the-difference-between-throughput-and-latency/)​

1. [https://aws.amazon.com/compare/the-difference-between-throughput-and-latency/](https://aws.amazon.com/compare/the-difference-between-throughput-and-latency/)
### **Inside NGINX: How We Designed for Performance & Scale**

**Source:** NGINX Blog | [Inside NGINX: How We Designed for Performance & Scale](https://blog.nginx.org/blog/inside-nginx-how-we-designed-for-performance-scale/)

**Main Idea:** NGINX achieves **exceptional performance and scalability** through an **event-driven, asynchronous, non-blocking architecture** that fundamentally differs from traditional thread/process-per-connection models. This design enables handling **millions of concurrent connections** with minimal memory and CPU overhead.

---

#### **Notes: Core Architectural Principles**

**1. The Problem with Traditional Web Servers**
*   **Apache-like Models:** Use **thread-per-connection** or **process-per-connection**.
*   **Drawbacks:**
    *   **High Memory Overhead:** Each thread/process requires its own stack (MBs).
    *   **Context Switching Overhead:** OS scheduler switches between many threads.
    *   **Poor CPU Cache Utilization:** Frequent context switching flushes caches.
    *   **Limited Scalability:** Max connections = max threads/processes.

**2. NGINX's Event-Driven Architecture**
*   **Master-Worker Process Model:**
    *   **Master Process:** Privileged, manages configuration, worker processes.
    *   **Worker Processes:** Unprivileged, handle actual connections (run as user).
    *   **Worker Count:** Typically matches **CPU cores** (no context switching overhead).
*   **Asynchronous, Non-blocking I/O:**
    *   Workers handle **thousands of connections simultaneously**.
    *   Never block waiting for I/O (network, disk, database).
    *   Use **event notification systems** (epoll on Linux, kqueue on BSD).

**3. The Event Loop (Heart of NGINX)**
*   **Single-threaded per Worker:** Each worker runs a **single event loop**.
*   **Event Loop Flow:**
    1.  Check for new events (connections, I/O ready).
    2.  Process events (read/write data).
    3.  Return to step 1.
*   **No Blocking:** If data isn't ready, worker moves to next event (doesn't wait).
*   **Efficient State Management:** Each connection is a **small state machine**, not a thread.

**4. Connection Processing Model**
*   **Worker Processes** listen on shared sockets (Linux `SO_REUSEPORT`).
*   **Event Notifications** indicate when:
    *   New connection is ready to accept.
    *   Data is ready to read from socket.
    *   Socket is ready to write to.
*   **Request Processing Phases:**
    *   **Read Request:** Parse headers, validate.
    *   **Process Request:** Apply configuration, execute logic.
    *   **Send Response:** Write to socket.
*   Each phase is **non-blocking** and yields if I/O not ready.

**5. Memory Management & Optimization**
*   **Predictable Memory Usage:** Each connection uses **~200 bytes** (vs. MBs for threads).
*   **Smart Buffering:** Only allocate buffers when needed, reuse buffers.
*   **Zero Copy:** Use `sendfile()` to serve static files directly from disk to network.
*   **Memory Pools:** Allocate/deallocate memory in chunks, not per allocation.

**6. Caching Architecture**
*   **Proxy Cache:** Caches backend responses.
*   **Shared Memory Zones:** Cache metadata shared across workers.
*   **Cache Loader:** Background process loads cache from disk.
*   **Cache Manager:** Expires old cache entries.
*   **Efficient Cache Key Design:** Customizable cache keys (URL, headers, etc.).

**7. Load Balancing Capabilities**
*   **Multiple Algorithms:** Round robin, least connections, IP hash, generic hash, random.
*   **Health Checks:** Active (periodic probes) and passive (monitor responses).
*   **Session Persistence:** IP hash, cookie-based stickiness.
*   **Slow Start:** Gradually increase traffic to recovered servers.

**8. Performance Comparison**
*   **Apache vs NGINX:**
    *   **Apache:** ~150 concurrent connections per GB RAM.
    *   **NGINX:** ~50,000+ concurrent connections per GB RAM.
*   **Why NGINX Wins:**
    *   No thread/process per connection.
    *   Minimal context switching.
    *   Efficient memory usage.
    *   Non-blocking I/O.

**9. Scalability Patterns**
*   **Vertical Scaling:** Add CPU cores → add workers (linear scaling).
*   **Horizontal Scaling:** Multiple NGINX instances behind DNS/L4 LB.
*   **Microservices Ready:** Can route to hundreds of backend services efficiently.

**10. Modern Extensions**
*   **NGINX Plus:** Commercial version with advanced features (active health checks, dynamic reconfiguration).
*   **NGINX Unit:** Application server (supports multiple languages).
*   **NGINX Amplify:** Monitoring and analytics.

---

#### **Summary / Key Takeaways for Interview:**

*   **Event-Driven > Thread-Per-Connection:** NGINX's **asynchronous, non-blocking architecture** is fundamentally more efficient than traditional models for I/O-bound workloads (web serving).
*   **One Worker per CPU Core:** Matches **concurrency to CPU parallelism**, avoiding context switching overhead within workers.
*   **Minimal Per-Connection State:** Each connection is a **small state machine**, not a full thread/process stack, enabling **massive connection counts**.
*   **Smart Use of OS Facilities:** Leverages **epoll/kqueue** for efficient event notification, `sendfile()` for zero-copy file transfers.
*   **Predictable Resource Usage:** Memory and CPU scale linearly with active connections, not total connections.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"Why is NGINX more performant than Apache for static file serving?"** → Apache uses **thread/process per connection** (high memory, context switching). NGINX uses **event-driven, non-blocking workers** (one per CPU core) handling thousands of connections each. NGINX also uses **zero-copy `sendfile()`** to serve files directly from disk to network without copying to user space.
*   **"Explain NGINX's master-worker architecture."** → **Master process:** Privileged, reads config, manages workers, handles signals. **Worker processes:** Unprivileged (run as www-data), handle connections, one per CPU core. Workers **share listening sockets**, use **non-blocking I/O with event loop** to handle thousands of connections concurrently without threads.
*   **"How does NGINX handle 10,000 concurrent connections?"** → With **4 workers** (4 CPU cores), each worker handles **~2,500 connections** in its **event loop**. Each connection is a **small state machine** (few hundred bytes). Worker uses **epoll** to be notified when sockets are ready for I/O, processes each ready socket, then continues loop. No blocking, no threads, minimal context switching.
*   **"What's the difference between blocking and non-blocking I/O in this context?"** → **Blocking:** Thread waits for I/O operation to complete (e.g., `read()` waits for data). **Non-blocking:** If I/O not ready, returns immediately with "would block", worker moves to next connection. NGINX uses **non-blocking I/O with event notifications** (epoll) to know when I/O is ready, enabling single thread to manage many connections.
*   **"How would you scale NGINX beyond a single server?"** → 1) **Vertical:** Add CPU cores, increase workers. 2) **Horizontal:** Multiple NGINX instances behind **DNS round-robin** or **L4 load balancer**. 3) **Global:** **Anycast** or **GeoDNS** to direct to nearest NGINX cluster. 4) **Caching layer** (Varnish) before NGINX for static content. NGINX itself can scale to **hundreds of thousands** of connections per server.