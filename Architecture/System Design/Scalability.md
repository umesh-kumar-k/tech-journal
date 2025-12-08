## Scalability Interview Checklist

- **Core Scalability Principles**
    
    |Principle|Definition|Key Metric|
    |---|---|---|
    |**Horizontal Scaling**|Capacity ∝ added machines|Linear capacity growth|
    |**Redundancy**|No single point of failure|Graceful degradation|
    |**Vertical Scaling**|Upgrade existing machines|Limited, costly at scale|
    
- **Load Balancing Strategies**
    
    |Approach|Pros|Cons|Tools|
    |---|---|---|---|
    |**Smart Clients**|Decentralized, reusable|Complex, error-prone|Custom libs|
    |**Hardware LB**|High perf|Expensive|**Citrix NetScaler**, F5|
    |**Software LB**|Cost-effective|Local only|**HAProxy** (localhost pools)|
    
- **Caching Layers**
    
    |Type|Strategy|Tools|
    |---|---|---|
    |**App Caching**|Read/write-through|**Memcached**, **Redis**|
    |**DB Caching**|Query optimization|MySQL innodb_buffer, Cassandra row cache|
    |**CDN**|Static media|**Akamai**, CloudFront|
    |**Invalidation**|Write-through or delete|TTL + explicit invalidation|
    
- **Off-line Processing**
    
    |Pattern|Use Case|Tools|
    |---|---|---|
    |**Message Queues**|Async heavy work|**RabbitMQ**|
    |**Cron → Queue**|Scheduled tasks|Cron + consumer pools|
    |**MapReduce**|Big data analytics|**Hadoop**, Hive|
    
- **Platform Layer Benefits**
    
    |Benefit|Impact|
    |---|---|
    |**Independent Scaling**|Web vs platform capacity|
    |**Multi-product Reuse**|Web, API, mobile|
    |**Team Scaling**|Product teams + platform team|
    
- **Implementation Patterns**
    
    text
    
    `User → LB → Web → Platform → DB/Cache             ↓        Message Queue → Workers`
    

## 60-Second Recap

- **Horizontal Scale:** Linear capacity + redundancy via **load balancing** (HAProxy > smart clients).
    
- **Caching:** App (**Redis**), DB config, CDN—**write-through** invalidation.
    
- **Async:** **RabbitMQ** queues + **Hadoop** MapReduce for heavy lifting.
    
- **Platform Layer:** Decouple web/platform for independent scaling + team velocity.
    
- **Gold:** LB → Web → Platform → Cache/DB + queues for off-line work.
    

**Reference**: [Architecting Systems for Scale](https://lethain.com/introduction-to-architecting-systems-for-scale/)[lethain](https://lethain.com/introduction-to-architecting-systems-for-scale/)​

1. [https://lethain.com/introduction-to-architecting-systems-for-scale/](https://lethain.com/introduction-to-architecting-systems-for-scale/)
### **Cornell Notes: Introduction to Architecting Systems for Scale**

**Source:** Ilya's Blog | [Introduction to Architecting Systems for Scale](https://lethain.com/introduction-to-architecting-systems-for-scale/)

**Main Idea:** Scaling systems requires understanding **different scaling dimensions** (load balancing, caching, database scaling) and making appropriate **architectural trade-offs**. This guide provides a structured approach to scaling through **horizontal scaling, decoupling, and appropriate data storage strategies**.

---

#### **Notes: Core Scaling Concepts & Strategies**

**1. Load Balancing: The Foundation of Horizontal Scaling**
*   **Purpose:** Distribute load across multiple servers to handle increased traffic.
*   **Implementation:**
    *   **DNS Round Robin:** Simple but limited (no failover, caching issues).
    *   **Hardware Load Balancers:** Expensive but powerful (F5, Citrix).
    *   **Software Load Balancers:** HAProxy, Nginx (flexible, cost-effective).
*   **Best Practice:** Use **health checks** to remove unhealthy servers from rotation.
*   **Load Balancer as SPOF:** Mitigate with **multiple LBs in active-passive or active-active** configuration.

**2. Caching: Reducing Load and Latency**
*   **Purpose:** Store frequently accessed data to reduce repeated expensive operations.
*   **Cache Levels:**
    *   **Client-side:** Browser cache (HTTP headers: `Cache-Control`, `ETag`).
    *   **CDN:** For static assets (images, CSS, JS).
    *   **Reverse Proxy Cache:** Nginx, Varnish (full page caching).
    *   **Application Cache:** In-memory caches (Redis, Memcached).
    *   **Database Cache:** Query cache, buffer pool.
*   **Cache Invalidation:** Hard problem. Strategies:
    *   Time-based expiration (TTL).
    *   Explicit invalidation on write.
    *   Versioned cache keys.

**3. Database Scaling Approaches**
*   **Vertical Scaling (Scale Up):** Increase server resources (CPU, RAM, disk).
    *   **Limits:** Eventually hits hardware/economic limits.
*   **Horizontal Scaling:**
    *   **Master-Slave Replication:**
        *   **Write to master**, **read from slaves**.
        *   **Challenge:** Replication lag (eventual consistency).
        *   **Use Case:** Read-heavy workloads.
    *   **Sharding (Partitioning):**
        *   Split data across multiple databases based on a **shard key**.
        *   **Challenges:** Cross-shard queries, rebalancing, complexity.
        *   **Shard Key Selection:** Critical (e.g., user_id, country).
    *   **Denormalization:** Duplicate data to avoid expensive joins.
        *   Trade-off: **Write overhead** for **read performance**.

**4. Asynchronous Processing & Queues**
*   **Purpose:** Decouple components and handle background tasks.
*   **Pattern:** User request → store in queue → worker processes.
*   **Benefits:**
    *   **Decoupling:** Producers and consumers independent.
    *   **Load leveling:** Handle traffic spikes.
    *   **Fault tolerance:** Messages persist if workers fail.
*   **Message Queues:** RabbitMQ, Kafka, AWS SQS.
*   **Use Cases:** Email sending, image processing, data aggregation.

**5. Service-Oriented Architecture (SOA) / Microservices**
*   **Decompose** monolithic application into **independent services**.
*   **Benefits:**
    *   Independent scaling of services.
    *   Technology heterogeneity (different services use different stacks).
    *   Organizational alignment (teams own services).
*   **Challenges:**
    *   **Network latency** between services.
    *   **Distributed transactions** (use Saga pattern).
    *   **Operational complexity** (monitoring, deployment).
*   **Communication:** Synchronous (HTTP/REST) or asynchronous (messaging).

**6. Planning for Failure**
*   **Assume failures will happen** and design accordingly.
*   **Strategies:**
    *   **Redundancy:** Multiple instances of critical components.
    *   **Graceful Degradation:** Return partial functionality during failures.
    *   **Circuit Breakers:** Prevent cascading failures.
    *   **Monitoring & Alerting:** Detect issues quickly.

**7. The "Scale Cube" Concept**
*   **Three dimensions of scaling:**
    1.  **X-axis:** Horizontal duplication (cloning).
    2.  **Y-axis:** Functional decomposition (services).
    3.  **Z-axis:** Data partitioning (sharding).
*   **Most applications:** Start with **X-axis**, then add **Y-axis** (services), finally **Z-axis** (sharding) when needed.

**8. Practical Scaling Progression**
1.  **Start:** Single server (everything together).
2.  **Separate:** Web server, database server.
3.  **Add Load Balancer:** Multiple web servers.
4.  **Add Read Replicas:** For database reads.
5.  **Add Caching:** Application cache (Redis).
6.  **Add CDN:** For static assets.
7.  **Shard Database:** When write limits reached.
8.  **Decompose to Services:** When monolith becomes unwieldy.

**9. Key Trade-offs & Decisions**
*   **Consistency vs. Availability:** Choose based on use case (CAP theorem).
*   **Latency vs. Throughput:** Optimize for one.
*   **Complexity vs. Performance:** More scaling = more complexity.
*   **Cost vs. Scale:** Scaling costs money; optimize for actual needs.

---

#### **Summary / Key Takeaways for Interview:**

*   **Scaling is Multi-Dimensional:** Use the **scale cube** framework—**X-axis (cloning), Y-axis (services), Z-axis (sharding)**. Most systems evolve through these dimensions.
*   **Caching is Critical:** Implement **multiple cache levels** (client, CDN, reverse proxy, application) to reduce load and latency. Cache invalidation is hard but manageable with TTLs and versioning.
*   **Database Scaling Evolves:** **Vertical scaling → read replicas → sharding**. Sharding is complex but necessary for massive scale. Choose shard key carefully.
*   **Decouple with Queues:** **Asynchronous processing** via message queues provides **decoupling, load leveling, and fault tolerance** for background tasks.
*   **Design for Failure:** Assume **everything fails**. Implement **redundancy, circuit breakers, graceful degradation, and monitoring**.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"How would you scale a web application from 1 to 1 million users?"** → Follow the **progression**: 1) Separate web/db servers, 2) **Load balancer + multiple web servers**, 3) **Database read replicas**, 4) **Caching** (Redis for hot data), 5) **CDN** for static assets, 6) **Database sharding** for writes, 7) **Microservices** decomposition for independent scaling. Emphasize **measuring bottlenecks** before scaling.
*   **"What are the trade-offs between vertical and horizontal scaling?"** → **Vertical scaling:** Simpler (no code changes), but has **hard limits** and creates **SPOF**. **Horizontal scaling:** **Infinite scale** theoretically, **fault-tolerant**, but requires **distributed systems design** (load balancing, data consistency, monitoring). Start vertical, then go horizontal when needed.
*   **"How do you decide when to shard a database?"** → When: 1) **Write throughput** exceeds single server capacity, 2) **Data size** exceeds storage limits, 3) **Regulatory requirements** demand geographic distribution. **Alternatives to try first:** Read replicas, caching, query optimization, archiving old data. Sharding is a **last resort** due to complexity.
*   **"Explain the scale cube concept with examples."** → **X-axis:** Clone entire application (load balancing). **Y-axis:** Split by function (microservices—user service, payment service). **Z-axis:** Split by data (sharding—users A-M on shard1, N-Z on shard2). Most apps scale: **X → Y → Z** as needed.
*   **"What cache strategies would you implement for an e-commerce site?"** → **Layered approach:** 1) **Browser cache** for static assets (via Cache-Control headers), 2) **CDN** for product images, CSS, JS, 3) **Reverse proxy cache** for product pages (with short TTL for price/inventory), 4) **Application cache** (Redis) for user sessions, product metadata, 5) **Database cache** for frequent queries. **Invalidation:** Version URLs for static assets, purge cache on product updates, use TTL for semi-static data.