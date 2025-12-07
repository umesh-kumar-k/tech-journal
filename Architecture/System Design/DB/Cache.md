# **System Design Cheat Sheet - Cache**

## **1. Essential Question**
*How do caching systems work in distributed architectures, and what are the key strategies, patterns, and trade-offs for implementing effective caching at scale?*

---

## **2. Main Ideas & Key Details**

### **A. Cache Fundamentals & Architecture**
*   **What is Caching?**
    *   Temporary storage of frequently accessed data closer to users
    *   **Goal:** Reduce latency, decrease backend load, improve throughput
    *   **Trade-off:** Stale data vs. performance improvement

*   **Cache Characteristics:**
    *   **Volatile:** Data lost on restart (typically)
    *   **Fast Access:** Memory-based (RAM) for microsecond access
    *   **Limited Capacity:** Must evict data when full
    *   **Key-Based Lookup:** O(1) access time with proper hashing

*   **Cache Location in Architecture:**
    *   **Client-side:** Browser cache, mobile app cache
    *   **DNS Cache:** Resolves domain names to IPs
    *   **CDN Cache:** Edge locations for static assets
    *   **Load Balancer Cache:** SSL session cache, health check cache
    *   **Application Cache:** In-memory cache (Redis, Memcached)
    *   **Database Cache:** Query cache, buffer pool, materialized views

### **B. Cache Invalidation Strategies**
*   **Write-Through Cache:**
    *   Write to cache and database simultaneously
    *   **Pros:** Data consistency, durability
    *   **Cons:** Higher write latency, both must succeed
    *   **Use case:** Critical data requiring consistency
    
*   **Write-Behind/Write-Back:**
    *   Write to cache immediately, batch writes to database later
    *   **Pros:** Low write latency, reduces database load
    *   **Cons:** Risk of data loss if cache fails, eventual consistency
    *   **Use case:** High write throughput, non-critical data

*   **Write-Around:**
    *   Write directly to database, bypassing cache
    *   **Pros:** Prevents cache pollution with write-only data
    *   **Cons:** Subsequent reads experience cache miss
    * **Use case:** Write-heavy, rarely re-read data

*   **Refresh-Ahead/Proactive Refresh:**
    *   Cache automatically refreshes before expiration
    *   **Pros:** No stale data, consistent performance
    *   **Cons:** Wasted cycles if data not accessed
    *   **Use case:** Predictable access patterns

### **C. Cache Eviction Policies**
*   **LRU (Least Recently Used):**
    *   Evict least recently accessed items first
    *   **Implementation:** Linked list + hash map
    *   **Good for:** Temporal locality patterns
    
*   **LFU (Least Frequently Used):**
    *   Evict least frequently accessed items
    *   **Implementation:** Min-heap + hash map
    *   **Good for:** Stable popularity distributions
    
*   **FIFO/LIFO:**
    *   First-In-First-Out / Last-In-First-Out
    *   Simple but often suboptimal
    
*   **Random Replacement:**
    *   Randomly select items to evict
    *   Simple, avoids scanning overhead
    
*   **TTL-based (Time To Live):**
    *   Automatic expiration after time period
    *   Often combined with other policies
    *   **Use case:** Time-sensitive data (sessions, rate limits)

### **D. Distributed Caching Patterns**
*   **Cache-Aside/Lazy Loading:**
    *   Application checks cache first, loads from DB on miss
    *   **Pros:** Simple, cache only contains requested data
    *   **Cons:** Cache miss penalty, potential for stale reads
    *   **Common pattern:** "if not in cache → load from DB → store in cache"
    
*   **Read-Through Cache:**
    *   Cache automatically loads from DB on miss
    *   **Pros:** Cleaner application code, cache manages loading
    *   **Cons:** Cache must understand data schema
    
*   **Write-Through Cache:**
    *   Write to cache and DB atomically
    *   **Pros:** Data consistency, cache always has latest
    *   **Cons:** Higher write latency
    
*   **Cache Population Strategies:**
    *   **Warm-up:** Pre-load cache before traffic
    *   **Pre-fetching:** Load predicted data before requests
    *   **Background refresh:** Update cache asynchronously

### **E. Distributed Cache Architectures**
*   **Client-Server Cache (Redis/Memcached):**
    *   Centralized cache cluster
    *   **Pros:** Simple, consistent hashing for distribution
    *   **Cons:** Single point of failure, network hops
    
*   **Replicated Cache:**
    *   Each node has full copy of cache
    *   **Pros:** Fast reads, fault tolerance
    *   **Cons:** Memory inefficient, slow writes (must replicate)
    
*   **Partitioned/Sharded Cache:**
    *   Data distributed across nodes
    *   **Pros:** Horizontal scaling, more total memory
    *   **Cons:** Node failure loses data, complex rebalancing
    
*   **Distributed Hash Table (DHT):**
    *   Peer-to-peer architecture
    *   **Pros:** No central coordination, fault tolerant
    *   **Cons:** Complex, eventual consistency
    
*   **Consistent Hashing:**
    *   Minimal data movement when nodes added/removed
    *   **Key technique:** Virtual nodes for even distribution

### **F. Common Cache Problems & Solutions**
*   **Cache Penetration:**
    *   **Problem:** Requests for non-existent data cause cache misses
    *   **Solution:** Cache empty values (negative caching), Bloom filters
    
*   **Cache Avalanche:**
    *   **Problem:** Many cache items expire simultaneously → thundering herd
    *   **Solution:** Randomize TTLs, staggered expiration, hot cache pre-warming
    
*   **Cache Stampede/Thundering Herd:**
    *   **Problem:** Concurrent cache misses cause multiple DB loads
    *   **Solution:** Mutex/locks, single-flight requests, background refresh
    
*   **Hotspot/Cache Hot Key:**
    *   **Problem:** Single key gets disproportionate traffic
    *   **Solution:** Local cache, key replication, request coalescing
    
*   **Cache Consistency:**
    *   **Problem:** Cache data becomes stale
    *   **Solutions:** Invalidation protocols, versioning, TTL strategies
    
*   **Cache Memory Management:**
    *   **Problem:** Memory fragmentation, inefficient eviction
    *   **Solutions:** Slab allocator (Memcached), Redis data structures

### **G. Cache Monitoring & Metrics**
*   **Key Performance Indicators:**
    *   **Hit Rate:** % of requests served from cache (aim for 80-95%)
    *   **Miss Rate:** % of requests requiring backend load
    *   **Latency:** P50, P95, P99 response times
    *   **Throughput:** Requests per second
    
*   **Health Metrics:**
    *   Memory usage, eviction rate, connection count
    *   Network I/O, CPU utilization
    *   Error rates, timeout rates
    
*   **Business Metrics:**
    *   Cost savings from reduced DB load
    *   User experience improvements (reduced latency)
    *   Throughput scalability

---

## **3. Summary / Synthesis**
Caching is a critical performance optimization technique in distributed systems that trades **data freshness for reduced latency and backend load**. Effective caching requires choosing appropriate **invalidation strategies** (write-through, write-behind, cache-aside), **eviction policies** (LRU, LFU, TTL), and **distribution architectures** (replicated vs. partitioned). The most successful caching implementations address common problems like **cache penetration, thundering herd, and hotspots** while providing robust monitoring of **hit rates, latency, and system health**. Modern systems often employ **multi-level caching hierarchies** (client → CDN → application → database) with each level optimized for specific access patterns and consistency requirements.

---

## **4. Key Terms / Vocabulary**
*   **Cache Hit/Miss:** Request served from/outside cache
*   **Hit Rate:** Percentage of requests served from cache
*   **TTL:** Time To Live (cache entry expiration time)
*   **Eviction Policy:** Algorithm for removing cache entries when full
*   **Thundering Herd:** Many simultaneous requests after cache expiration
*   **Cache Penetration:** Requests for non-existent data bypassing cache
*   **Cache Stampede:** Concurrent cache misses causing redundant backend loads
*   **Write-Through/Write-Behind:** Cache write strategies
*   **Cache-Aside/Lazy Loading:** Application-managed cache pattern
*   **Consistent Hashing:** Distribution technique minimizing reshuffling
*   **Negative Caching:** Storing "not found" results to avoid repeated misses
*   **Bloom Filter:** Probabilistic data structure for membership tests

---

## **5. Potential Interview Questions**

### **Fundamental Concepts:**
1. "Explain the trade-offs between write-through and write-behind caching strategies."
2. "When would you choose LRU vs LFU eviction policies?"
3. "How does consistent hashing work in distributed caches?"
4. "What's the difference between cache-aside and read-through patterns?"
5. "How do you measure cache effectiveness in production?"

### **Design Scenarios:**
1. "Design a caching layer for a global e-commerce product catalog."
2. "How would you cache user sessions in a distributed microservices architecture?"
3. "Design a cache invalidation strategy for a social media feed."
4. "How would you implement caching for a real-time leaderboard system?"
5. "Design a multi-level cache hierarchy for a content delivery network."

### **Problem Solving:**
1. "How would you prevent cache stampede/thundering herd problems?"
2. "What strategies address cache penetration for non-existent keys?"
3. "How do you handle cache hotspots for popular items?"
4. "What's your approach to cache warming after deployment?"
5. "How would you migrate from one cache system to another with zero downtime?"

### **Advanced Topics:**
1. "How do you ensure cache consistency in a distributed system?"
2. "What are the security considerations for caching sensitive data?"
3. "How would you implement cache partitioning for a multi-tenant application?"
4. "Explain how CDN caching differs from application-level caching."
5. "How do cache systems handle failover and disaster recovery?"

### **Real-World Implementation:**
1. "When would you choose Redis vs Memcached for a project?"
2. "How do you monitor and alert on cache performance issues?"
3. "What strategies reduce cache memory fragmentation?"
4. "How do you handle cache versioning during schema changes?"
5. "What's your approach to cache key design and naming conventions?"

---

## **6. How to Use These Notes & Key Takeaways**

### **Interview Preparation Strategy:**
1. **Memorize the Patterns** - Know when to use cache-aside vs. read-through vs. write-through
2. **Understand Trade-offs** - Be able to explain consistency vs. performance trade-offs
3. **Practice Problem Solving** - Work through cache stampede, penetration, hotspot scenarios
4. **Know the Metrics** - Understand what hit rates are good, what to monitor
5. **Prepare Real Examples** - Have specific caching implementations you've worked on

### **Key Takeaways for Interviews:**
1. **Caching is About Trade-offs** - Every caching decision involves freshness vs. performance
2. **Multi-Level is Common** - Real systems use browser → CDN → app → DB caching
3. **Consistency is Hard** - Distributed cache consistency requires careful design
4. **Monitor Everything** - You can't optimize what you don't measure
5. **Design for Failure** - Caches fail; systems must degrade gracefully

### **During the Interview:**
1. **Start with Requirements Analysis:**
   - "What's the read/write ratio?"
   - "What consistency requirements exist?"
   - "What's the data access pattern (random vs. sequential)?"
   - "What are the latency requirements?"
2. **Choose Strategies Based on Use Case:**
   - **Write-heavy:** Write-around or write-behind
   - **Read-heavy:** Cache-aside with pre-warming
   - **Consistency-critical:** Write-through or strong invalidation
   - **Global distribution:** CDN + edge caching
3. **Address Problems Proactively:**
   - Mention thundering herd prevention
   - Discuss cache penetration solutions
   - Consider hotspot mitigation
4. **Discuss Monitoring & Operations:**
   - What metrics to track
   - How to set alerts
   - Cache warming procedures
   - Capacity planning

### **Common Pitfalls to Avoid:**
- ❌ "Cache everything" (causes memory bloat, stale data)
- ❌ "Never expire cache" (leads to stale data)
- ❌ "Same TTL for all items" (causes thundering herd)
- ❌ "Ignore cache misses" (monitor and optimize miss patterns)
- ❌ "No invalidation strategy" (results in inconsistent data)

### **Signs of Strong Candidates:**
- ✅ Asks about data access patterns first
- ✅ Considers multiple cache levels
- ✅ Discusses failure scenarios and graceful degradation
- ✅ Mentions monitoring and metrics from the start
- ✅ Has specific strategies for common cache problems

---

## **7. Quick Review Cheat Sheet**

### **Cache Strategy Selection Matrix:**
| **Pattern**       | **Best For**                | **Consistency** | **Complexity** |
| ----------------- | --------------------------- | --------------- | -------------- |
| **Cache-Aside**   | General purpose, read-heavy | Eventual        | Low            |
| **Read-Through**  | Simplified app logic        | Strong          | Medium         |
| **Write-Through** | Critical data, consistency  | Strong          | High           |
| **Write-Behind**  | High write throughput       | Eventual        | High           |
| **Write-Around**  | Write-heavy, rarely read    | Eventual        | Low            |

### **Eviction Policy Guide:**
- **LRU:** Temporal locality, recent accesses likely again
- **LFU:** Stable popularity, "top N" items
- **TTL:** Time-sensitive data (sessions, rate limits)
- **Random:** Simple, avoids scanning overhead
- **Hybrid:** LRU with TTL (common in practice)

### **Cache Problem Solutions:**
1. **Cache Penetration:** Negative caching + Bloom filters
2. **Thundering Herd:** Staggered TTLs + mutex locks + pre-warming
3. **Hot Keys:** Local cache + replication + request coalescing
4. **Consistency Issues:** Versioning + invalidation streams + TTL tuning

### **Monitoring Dashboard Essentials:**
1. **Performance:** Hit rate (>90% good), latency (P95 < 10ms), throughput
2. **Health:** Memory usage (<80%), eviction rate, connection count
3. **Errors:** Timeout rate, miss penalty, backend load increase
4. **Business:** Cost savings, user satisfaction (latency improvements)

### **Capacity Planning Guidelines:**
1. **Size cache** to hold working set (80/20 rule often applies)
2. **Plan for growth** - monitor eviction rates as indicator
3. **Consider replication** - more nodes for availability, not capacity
4. **Test failure scenarios** - what happens when cache dies?

### **Cache Key Design Principles:**
1. **Meaningful:** Include version, tenant, data type
2. **Consistent:** Standardized naming convention
3. **Properly Scoped:** Include enough context for uniqueness
4. **Not Too Long:** Balance readability with memory usage
5. **Versioned:** Include schema version for invalidation

This comprehensive guide provides everything needed to discuss caching confidently in system design interviews, from fundamental concepts to advanced distributed patterns and real-world implementation considerations.