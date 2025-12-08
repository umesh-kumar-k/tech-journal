
## DB Sharding Interview Checklist

- **Sharding Fundamentals**
    
    - **Horizontal Partitioning:** Split rows across shards (vs vertical = split columns).
        
    - **Shard Key:** Field determining shard assignment (high cardinality, even distribution).
        
    - **Distribution:** Hash-based (consistent hashing), range-based, directory-based.
        
- **Shard Key Selection Criteria**
    
    |Criteria|Good|Bad|
    |---|---|---|
    |**Cardinality**|user_id (millions)|is_premium (2 values)|
    |**Distribution**|Hash(user_id)|country (90% US)|
    |**Query Alignment**|Matches access patterns|Forces cross-shard|
    
- **Sharding Strategies**
    
    |Strategy|Pros|Cons|
    |---|---|---|
    |**Hash-Based**|Even distribution|Hot keys|
    |**Range-Based**|Sequential scans|Uneven splits|
    |**Directory**|Flexible|Central lookup|
    
- **Common Challenges & Solutions**
    
    |Problem|Impact|Mitigation|
    |---|---|---|
    |**Hot Shards**|Uneven load|Compound keys, replicas|
    |**Cross-Shard**|Slow queries|Denormalize, cache aggregates|
    |**Resharding**|Data migration|Consistent hashing, gradual split|
    
- **Interview Triggers**
    
    - Storage: >1-2TB single instance.
        
    - Writes: >10K writes/sec.
        
    - Reads: Read replicas insufficient.
        
- **Real-World Examples**
    
    |System|Shard Key|Strategy|
    |---|---|---|
    |**Cassandra**|Partition key|Consistent hashing + vnodes|
    |**MongoDB**|Shard key (hashed/range)|Chunk balancer|
    |**DynamoDB**|Partition key|Internal hashing|
    |**Vitess (MySQL)**|Key range|Online resharding|
    

## 60-Second Recap

- **Shard Key:** High cardinality, even distribution, query-aligned (user_id).
    
- **Strategies:** Hash (even), Range (sequential), Directory (flexible).
    
- **Problems:** Hot shards (compound keys), cross-shard (denorm/cache).
    
- **When:** Storage >2TB, writes >10K/sec, replica reads insufficient.
    
- **Gold:** Consistent hashing + user_id + gradual resharding.
    

**Reference**: [Hello Interview Sharding](https://www.hellointerview.com/learn/system-design/core-concepts/sharding)[hellointerview](https://www.hellointerview.com/learn/system-design/core-concepts/sharding)​

1. [https://www.hellointerview.com/learn/system-design/core-concepts/sharding](https://www.hellointerview.com/learn/system-design/core-concepts/sharding)
# **Cornell Notes: Sharding in System Design**

**Source:** HelloInterview, "System Design Core Concepts: Sharding"  
**URL:** https://www.hellointerview.com/learn/system-design/core-concepts/sharding

## **1. Essential Question**
*What is sharding, how does it enable horizontal scaling of databases, and what are the key strategies, challenges, and implementation considerations for distributed data partitioning?*

---

## **2. Main Ideas & Key Details**

### **A. What is Sharding?**
*   **Core Definition:**
    *   Horizontal partitioning of data across multiple database instances/servers
    *   Each partition called a "shard" (contains subset of total data)
    *   **Key Idea:** Distribute data and load, not just replicate

*   **Why Shard?**
    *   **Scale Beyond Single Server:** Overcome hardware limits (CPU, RAM, disk I/O)
    *   **Improve Performance:** Parallel processing, reduced index sizes
    *   **Increase Availability:** Failure isolation (one shard down ≠ whole system down)
    *   **Geographic Distribution:** Place data closer to users

*   **Sharding vs. Replication:**
    *   **Replication:** Same data on multiple nodes (for availability/read scaling)
    *   **Sharding:** Different data on different nodes (for write scaling/capacity)
    *   **Often Combined:** Sharded clusters with replication per shard

### **B. Sharding Strategies (Partitioning Schemes)**
*   **Range-Based Sharding:**
    *   **How it works:** Partition by ranges of a key (e.g., user_id 1-1000 on shard1, 1001-2000 on shard2)
    *   **Advantages:**
        - Simple to implement and understand
        - Efficient range queries (get users A-M)
        - Easy to add new shards for new ranges
    *   **Disadvantages:**
        - Potential hotspots (uneven distribution)
        - Sequential keys create imbalance
        - May need periodic rebalancing
    *   **Use Cases:** Time-series data, alphabetical ranges, naturally ordered data

*   **Hash-Based Sharding:**
    *   **How it works:** Hash shard key → determine shard (e.g., hash(user_id) % number_of_shards)
    *   **Consistent Hashing:** Special algorithm that minimizes reshuffling when adding/removing nodes
        - Hash space forms ring
        - Each node responsible for range of hashes
        - Adding node only affects adjacent ranges
    *   **Advantages:**
        - Even data distribution (avoid hotspots)
        - Minimal data movement on cluster changes (with consistent hashing)
    *   **Disadvantages:**
        - Inefficient range queries
        - Cannot easily add shards without redistributing data (without consistent hashing)
    *   **Use Cases:** User data, evenly distributed workloads

*   **Directory-Based Sharding:**
    *   **How it works:** Lookup service maps keys to shards (shard map/lookup table)
    *   **Advantages:**
        - Maximum flexibility
        - Easy to move data between shards
        - Can use any partitioning logic
    *   **Disadvantages:**
        - Single point of failure (lookup service)
        - Extra network hop for each query
        - Lookup service becomes bottleneck
    *   **Use Cases:** Complex sharding logic, frequent rebalancing needed

*   **Geo-Based Sharding:**
    *   **How it works:** Partition by geographic region (e.g., US users on shard1, EU users on shard2)
    *   **Advantages:**
        - Data locality (lower latency for local queries)
        - Compliance with data sovereignty laws
    *   **Disadvantages:**
        - Cross-region queries expensive
        - Potential imbalance (some regions larger)
    *   **Use Cases:** Global applications, regulatory compliance requirements

*   **Tenant-Based Sharding:**
    *   **How it works:** Separate shard per customer/organization (multi-tenant SaaS)
    *   **Isolation Levels:**
        - **Dedicated shard:** One tenant per shard (max isolation)
        - **Shared shard:** Multiple tenants per shard (better resource utilization)
    *   **Advantages:**
        - Strong isolation between tenants
        - Easy backup/restore per tenant
        - Customized performance per tenant
    *   **Disadvantages:**
        - Poor resource utilization (underutilized shards)
        - Operational complexity
    *   **Use Cases:** SaaS applications, B2B platforms

### **C. Shard Key Selection Criteria**
*   **Cardinality:** High cardinality preferred (many unique values = even distribution)
*   **Frequency Distribution:** Even distribution of access patterns (avoid hot keys)
*   **Query Patterns:** Align with common query access patterns
*   **Write Patterns:** Consider how data will be written/updated
*   **Joins & Relationships:** Related data should ideally be on same shard
*   **Growth Patterns:** How will data volume grow over time?

*   **Poor Shard Key Examples:**
    - Boolean fields (only 2 values = 2 shards max)
    - Incremental IDs (causes hotspots on latest shard)
    - Frequently updated fields (causes cross-shard updates)

### **D. Common Challenges & Solutions**
*   **Hotspots (Hot Shards):**
    *   **Problem:** Uneven load distribution
    *   **Solutions:**
        - Choose better shard key (higher cardinality)
        - **Salting:** Add random prefix to keys (e.g., 1_user123, 2_user123)
        - Dynamic repartitioning
        - Caching layer for hot data

*   **Cross-Shard Operations:**
    *   **JOINs Across Shards:**
        - **Solution 1:** Denormalize (duplicate data to avoid joins)
        - **Solution 2:** Application-level joins (query each shard, merge in app)
        - **Solution 3:** Global index/reference tables
        - **Solution 4:** Design schema to keep related data together
    *   **Transactions Across Shards:**
        - **Problem:** ACID transactions difficult across shards
        - **Solutions:** Two-phase commit (2PC), Saga pattern, eventual consistency

*   **Rebalancing & Resharding:**
    *   **When needed:** Adding/removing shards, uneven distribution
    *   **Approaches:**
        - **Offline:** Take system down (not ideal for production)
        - **Online:** Live migration (complex but necessary for zero downtime)
    *   **Online Techniques:**
        - **Dual writes:** Write to old and new locations during migration
        - **Read from both:** During migration
        - **Backfill:** Copy historical data
        - **Cutover:** Switch writes, then reads
        - **Cleanup:** Remove old data

*   **Query Routing:**
    *   **Problem:** How does application know which shard to query?
    *   **Solutions:**
        - **Middleware/proxy layer:** Routes queries based on shard key
        - **Client library:** Embed routing logic in application
        - **Database-aware drivers:** Some databases handle routing internally

*   **Data Skew:**
    *   **Problem:** Some shards have much more data than others
    *   **Solutions:** Monitor and rebalance, choose better shard key, split large shards

### **E. Implementation Approaches**
*   **Application-Level Sharding:**
    *   Application contains routing logic
    *   **Advantages:** Full control, can optimize for specific use case
    *   **Disadvantages:** Complex application code, tight coupling

*   **Database-Level Sharding:**
    *   Database handles partitioning internally
    *   **Advantages:** Transparent to application, simpler development
    *   **Disadvantages:** Less control, database-specific features
    *   **Examples:** MongoDB sharded clusters, MySQL partitioning

*   **Proxy/Middleware Sharding:**
    *   Separate layer handles routing
    *   **Advantages:** Decouples application from sharding logic
    *   **Disadvantages:** Extra network hop, another component to maintain
    *   **Examples:** Vitess, ProxySQL, Amazon RDS Proxy

### **F. Monitoring & Operations**
*   **Key Metrics to Monitor:**
    *   **Shard utilization:** Disk space, CPU, memory per shard
    *   **Traffic distribution:** Queries per second per shard
    *   **Latency:** Query response times per shard
    *   **Replication lag:** If shards are replicated
    *   **Error rates:** Failed queries per shard

*   **Operational Considerations:**
    *   **Backup strategy:** Per shard or coordinated
    *   **Schema changes:** Must be applied to all shards
    *   **Monitoring overhead:** N shards = N times monitoring
    *   **Connection management:** More shards = more connections
    *   **Cost:** More nodes = higher infrastructure cost

### **G. When to Shard vs. Alternatives**
*   **Consider Sharding When:**
    - Single database hitting hardware limits
    - Write throughput needs exceed single node capacity
    - Dataset too large for single server
    - Need geographic distribution for performance/compliance

*   **Consider Alternatives First:**
    - **Vertical scaling:** More powerful hardware
    - **Read replicas:** For read-heavy workloads
    - **Caching:** Reduce database load
    - **Architecture changes:** Microservices, event sourcing, CQRS
    - **Database tuning:** Indexing, query optimization

*   **Sharding as Last Resort:** Adds significant complexity; exhaust other options first

---

## **3. Summary / Synthesis**
Sharding is a **horizontal partitioning strategy** that distributes data across multiple database instances to overcome the limitations of single-server architectures. Key strategies include **range-based, hash-based, directory-based, geo-based, and tenant-based sharding**, each with different trade-offs for data distribution, query efficiency, and operational complexity. Successful sharding requires **careful shard key selection** to avoid hotspots and enable efficient queries. Major challenges include **cross-shard operations (JOINs, transactions), rebalancing, query routing, and operational overhead**. While sharding enables massive scalability, it **adds significant complexity** and should be considered after exhausting alternatives like vertical scaling, read replicas, and caching. Modern implementations often use **consistent hashing to minimize data movement** and **proxy layers to abstract complexity** from applications.

---

## **4. Key Terms / Vocabulary**
*   **Shard:** A horizontal partition of data in a database
*   **Shard Key:** The field used to determine which shard stores a record
*   **Partitioning:** Dividing data into smaller, manageable pieces
*   **Consistent Hashing:** Hashing algorithm that minimizes data movement when nodes added/removed
*   **Hotspot/Heat Skew:** Uneven load distribution across shards
*   **Cross-Shard Query:** Query that needs data from multiple shards
*   **Rebalancing:** Moving data between shards to achieve even distribution
*   **Salting:** Adding random prefix to keys to improve distribution
*   **Shard Chunk:** Subdivision of a shard (in some systems like MongoDB)
*   **Query Router:** Component that directs queries to appropriate shard

---

## **5. Potential Interview Questions**

### **Fundamental Concepts:**
1. "Explain the difference between sharding and replication."
2. "Compare range-based vs. hash-based sharding strategies."
3. "What is consistent hashing and why is it important for sharding?"
4. "How do you choose a good shard key for a users table?"
5. "What are the trade-offs of early sharding vs. delaying sharding?"

### **Design Scenarios:**
1. "Design a sharding strategy for Twitter's tweet storage."
2. "How would you shard an e-commerce product catalog database?"
3. "Design a multi-tenant SaaS application with data isolation requirements."
4. "How would you handle sharding for a global social media platform?"
5. "Design a system that needs to support rapid customer growth from 1M to 100M users."

### **Problem Solving:**
1. "How would you handle JOINs across sharded tables?"
2. "What's your approach to rebalancing shards without downtime?"
3. "How do you prevent hotspot shards in a social graph database?"
4. "What monitoring would you implement for a sharded database cluster?"
5. "How would you handle schema changes in a sharded environment?"

### **Implementation Details:**
1. "Compare application-level vs. database-level sharding implementations."
2. "How does MongoDB implement sharding internally?"
3. "What are the challenges of distributed transactions across shards?"
4. "How would you implement query routing in a sharded system?"
5. "What backup strategies work for sharded databases?"

### **Advanced Topics:**
1. "How does sharding relate to the CAP theorem?"
2. "What are the security implications of different sharding strategies?"
3. "How would you migrate from a single database to sharded architecture?"
4. "What role do proxy layers play in sharded systems?"
5. "How does geo-sharding interact with data sovereignty laws?"

---

## **6. How to Use These Notes & Key Takeaways**

### **Interview Preparation Strategy:**
1. **Memorize Sharding Strategies** - Know when to use range vs. hash vs. directory
2. **Understand Consistent Hashing** - Be able to explain it simply with diagrams
3. **Practice Shard Key Selection** - Given a schema, choose and justify shard key
4. **Prepare for Trade-off Questions** - Sharding adds complexity; know when it's worth it
5. **Know Real System Examples** - How Twitter, Facebook, etc., handle sharding

### **Key Takeaways for Interviews:**
1. **Sharding Enables Horizontal Scale** - But at cost of increased complexity
2. **Shard Key is Critical** - Poor choice leads to hotspots and performance issues
3. **Cross-Shard Operations Expensive** - Design to minimize them
4. **Rebalancing is Hard** - Plan for growth from the beginning
5. **Consider Alternatives First** - Sharding should be last resort, not first solution

### **During the Interview:**
1. **Start with Requirements Analysis:**
   - "What's the current and projected data volume?"
   - "What are the read/write patterns?"
   - "What are the query patterns (point queries vs. range scans)?"
   - "What are the availability and consistency requirements?"
2. **Evaluate Sharding Necessity:**
   - "Have we exhausted vertical scaling?"
   - "Would read replicas solve the problem?"
   - "Could caching reduce the load sufficiently?"
3. **Design Sharding Strategy:**
   - Choose appropriate sharding strategy based on access patterns
   - Select optimal shard key with justification
   - Plan for cross-shard operations
   - Design rebalancing strategy
4. **Address Operational Concerns:**
   - Monitoring and alerting strategy
   - Backup and recovery procedures
   - Failure handling and failover
   - Cost implications

### **Common Pitfalls to Avoid:**
- ❌ "Shard everything immediately" (premature optimization)
- ❌ "Use auto-increment IDs as shard key" (creates hotspots)
- ❌ "Ignore cross-shard JOIN costs" (kills performance)
- ❌ "No rebalancing plan" (leads to severe imbalance)
- ❌ "Forget about monitoring" (can't manage what you can't measure)

### **Signs of Strong Candidates:**
- ✅ Asks about data growth and access patterns first
- ✅ Considers alternatives before jumping to sharding
- ✅ Justifies shard key selection with specific reasoning
- ✅ Discusses failure scenarios and mitigation
- ✅ Mentions operational overhead and monitoring

---

## **7. Quick Review Cheat Sheet**

### **Sharding Strategy Selection Guide:**
| **Strategy** | **Best For** | **Even Distribution** | **Range Queries** | **Rebalancing** |
|-------------|-------------|----------------------|------------------|----------------|
| **Range-based** | Ordered data, time-series | ❌ Poor | ✅ Excellent | ❌ Difficult |
| **Hash-based** | Even distribution, point lookups | ✅ Excellent | ❌ Poor | ⚠️ Medium (with consistent hashing) |
| **Directory** | Complex logic, frequent changes | ⚠️ Configurable | ⚠️ Depends | ✅ Easy |
| **Geo-based** | Global apps, compliance | ❌ Poor | ⚠️ Within region | ❌ Difficult |
| **Tenant-based** | SaaS, multi-tenant | ❌ Poor | ✅ Per tenant | ⚠️ Medium |

### **Shard Key Selection Criteria:**
1. **High Cardinality** (many unique values)
2. **Even Distribution** of access patterns
3. **Aligns with Query Patterns** (common filters)
4. **Minimizes Cross-Shard Operations**
5. **Considers Data Growth** patterns

### **Hotspot Prevention Techniques:**
1. **Salting:** Add random prefix (1_user123, 2_user123)
2. **Composite Keys:** Combine fields for better distribution
3. **Consistent Hashing:** With virtual nodes
4. **Dynamic Repartitioning:** Split hot shards
5. **Write Buffering:** Queue writes to hot shards

### **Cross-Shard JOIN Strategies:**
1. **Denormalization:** Duplicate data to avoid JOINs
2. **Global Index Tables:** Maintain cross-reference
3. **Application-side JOINs:** Query each shard, merge in app
4. **Materialized Views:** Pre-compute JOIN results
5. **Design Avoidance:** Keep related data together

### **Rebalancing Checklist:**
1. **When to rebalance:** >20% size difference, performance issues
2. **Online migration:** Dual writes, read from both, cutover
3. **Monitoring:** During and after migration
4. **Rollback plan:** If migration fails
5. **Communication:** Notify stakeholders of potential impact

### **When to Shard vs. Scale Vertically:**
- **Shard when:** Data > 1TB, writes > 10K/sec, need geographic distribution
- **Scale vertically when:** Data < 1TB, budget for better hardware, simpler operations preferred
- **Hybrid approach:** Shard for scale + vertical scaling per shard

### **Monitoring Essentials for Sharded Clusters:**
1. **Per-shard metrics:** CPU, memory, disk, query rate
2. **Distribution metrics:** Data size per shard, row count per shard
3. **Performance metrics:** Latency per shard, error rates per shard
4. **Rebalancing metrics:** Data movement rates, completion estimates
5. **Business metrics:** User impact, cost per shard

This comprehensive guide covers everything from fundamental sharding concepts to advanced implementation considerations, providing the knowledge needed to handle sharding-related questions in system design interviews.
