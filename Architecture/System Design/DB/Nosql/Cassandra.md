# **Cornell Notes: Apache Cassandra Deep Dive**

**Source:** HelloInterview, "System Design Deep Dives: Cassandra"  
**URL:** https://www.hellointerview.com/learn/system-design/deep-dives/cassandra

## **1. Essential Question**
*What is Apache Cassandra, how does its distributed architecture enable massive scalability and fault tolerance, and what are its key design patterns and trade-offs for large-scale applications?*

---

## **2. Main Ideas & Key Details**

### **A. Cassandra Fundamentals & Origin**
*   **Historical Context:**
    *   Created at Facebook (2008) to solve inbox search problem
    *   Open-sourced in 2009, became Apache top-level project in 2010
    *   **Inspired by:** Amazon Dynamo (distribution) + Google BigTable (data model)

*   **Core Philosophy:**
    *   **"Always available"** - Prioritizes availability over strong consistency (AP system)
    *   **Masterless architecture** - All nodes equal, no single point of failure
    *   **Linear scalability** - Add nodes, get proportional capacity increase
    *   **Tunable consistency** - Choose consistency level per operation

*   **Key Use Cases:**
    *   Time-series data (IoT, metrics, logs)
    *   High-velocity write workloads
    *   Global-scale applications
    *   Product catalogs, recommendation engines
    *   Messaging systems (Facebook Messenger uses Cassandra)

### **B. Architecture & Data Distribution**
*   **Ring Architecture:**
    *   Nodes arranged in **token ring** (hash space 0 to 2^127)
    *   Each node assigned **token range(s)** (portion of hash space)
    *   Data distributed via **consistent hashing** on partition key

*   **Virtual Nodes (vnodes):**
    *   Each physical node hosts **multiple virtual nodes** (256 default)
    *   **Advantages:**
        1. Even data distribution
        2. Easier rebalancing (no manual token assignment)
        3. Better failure recovery
        4. Capacity weighting possible

*   **Replication Strategy:**
    *   **Replication Factor (RF):** How many copies of each piece of data
    *   **SimpleStrategy:** For single datacenter (RF=3 typical)
    *   **NetworkTopologyStrategy:** For multiple datacenters
        - Example: `{'DC1': 3, 'DC2': 2}` - 3 replicas in DC1, 2 in DC2
    *   **Replica Placement:** Using **snitch** (awareness of network topology)

### **C. Data Model & CQL (Cassandra Query Language)**
*   **Partition Key & Clustering Columns:**
    *   **Primary Key = (Partition Key, Clustering Columns...)**
    *   **Partition Key:** Determines which node stores data (hash distribution)
    *   **Clustering Columns:** Determines sort order within partition
    *   **Example:** `PRIMARY KEY ((user_id), created_at)` - user_id partitions, created_at sorts

*   **Table Design Principles:**
    *   **"One table per query"** philosophy
    *   Design tables based on **access patterns**, not normalization
    *   **Denormalization** is expected and encouraged
    *   **Duplicate data** across tables for different queries

*   **CQL vs. SQL Differences:**
    *   **No JOINs** - Must denormalize or handle in application
    *   **No foreign key constraints**
    *   **Limited aggregations** (no GROUP BY, only COUNT/SUM/AVG/MIN/MAX)
    *   **Secondary indexes** limited (avoid on high-cardinality columns)
    *   **Batch statements** for atomicity (but with performance implications)

*   **Data Types:**
    *   Basic: `text`, `int`, `bigint`, `float`, `double`, `boolean`, `uuid`
    *   Collection: `list`, `set`, `map`
    *   Special: `counter` (distributed counters), `blob`
    *   Time: `timestamp`, `timeuuid` (for time-series)

### **D. Write & Read Path**
*   **Write Process:**
    1. Client sends write to **coordinator node** (any node can be coordinator)
    2. Coordinator determines replica nodes using **partitioner**
    3. Write to **commit log** (for durability - append-only)
    4. Write to **memtable** (in-memory structure)
    5. **Acknowledgment** based on consistency level
    6. Periodically **flush memtable to SSTable** (immutable sorted string table)

*   **Read Process:**
    1. Client sends read to coordinator
    2. Coordinator contacts replicas based on consistency level
    3. Read from **memtable + SSTables** (merge results)
    4. **Bloom filters** check if SSTable might contain key
    5. **Partition key cache & row cache** (optional optimizations)
    6. **Read repair** - fix inconsistencies during read

*   **Compaction:**
    *   **Process:** Merge SSTables, remove tombstones (deleted data)
    *   **Strategies:** SizeTieredCompactionStrategy (STCS), LeveledCompactionStrategy (LCS), TimeWindowCompactionStrategy (TWCS)
    *   **TWCS** ideal for time-series data (expire by time windows)

### **E. Consistency Levels & Tunable Consistency**
*   **Consistency Level (CL):** How many replicas must respond for success
    *   `ONE` - One replica responds (fastest, weakest)
    *   `QUORUM` - Majority of replicas (`floor(RF/2) + 1`)
    *   `LOCAL_QUORUM` - Quorum within local datacenter
    *   `EACH_QUORUM` - Quorum in each datacenter
    *   `ALL` - All replicas respond (slowest, strongest)

*   **CAP Theorem Position:**
    *   **AP system** - Available and Partition-tolerant
    *   During network partition: **continues serving requests** (AP)
    *   Can achieve **eventual** or **tunable consistency**

*   **Lightweight Transactions (LWT):**
    *   **Paxos-based** for linearizable consistency
    *   **Example:** `INSERT ... IF NOT EXISTS`
    *   **Performance cost:** 4x round trips vs. normal write
    *   Use sparingly for coordination

### **F. Performance Characteristics**
*   **Write-Optimized Architecture:**
    *   **Append-only writes** (no in-place updates)
    *   **Sequential disk I/O** (optimal for HDDs/SSDs)
    *   **No read-before-write** (unlike some systems)
    *   **Writes never block reads** (different SSTables)

*   **Read Performance Factors:**
    *   **Partition size** - Keep under 100MB ideally
    *   **SSTable count** - More SSTables = slower reads
    *   **Cache hit rates** - Key cache, row cache
    *   **Bloom filter effectiveness** - False positives cause unnecessary disk reads

*   **Scaling Characteristics:**
    *   **Linear scalability** - Double nodes = ~double throughput
    *   **No degradation** at scale (unlike some distributed systems)
    *   **Predictable performance** with proper data modeling

### **G. Data Modeling Patterns**
*   **Time-Series Pattern:**
    *   **Partition by time bucket** (day, hour, etc.)
    *   **Example:** `PRIMARY KEY ((sensor_id, date), timestamp)`
    *   **Use TWCS compaction** for automatic expiration
    *   **TTL** for automatic data expiration

*   **Wide Partition Pattern:**
    *   Many rows per partition (e.g., user's timeline)
    *   **Risk:** Large partitions (>100MB) cause performance issues
    *   **Solution:** Bucket by time or other dimension

*   **Materialized Views:**
    *   Automatic denormalization (pre-computed queries)
    *   **Trade-off:** Additional write overhead
    *   **Alternative:** Manual denormalization with multiple tables

*   **Secondary Indexes:**
    *   **Use for low-cardinality columns** (state, country, status)
    *   **Avoid for high-cardinality** (user_id, email) - causes fan-out
    *   **Better alternative:** Manual index table

### **H. Deployment & Operations**
*   **Cluster Sizing Guidelines:**
    *   **Minimum:** 3 nodes (for replication factor 3)
    *   **Production:** 6+ nodes per datacenter
    *   **Odd numbers preferred** for quorum calculations
    *   **Rule of thumb:** 1TB-2TB data per node maximum

*   **Multi-Datacenter Deployments:**
    *   **Active-Active** - All DCs accept reads/writes
    *   **Asynchronous replication** between DCs
    *   **NetworkTopologyStrategy** for replica placement
    *   **Consider latency** - WAN replication has delay

*   **Monitoring & Metrics:**
    *   **Critical:** Read/write latency (P95, P99), throughput
    *   **Node health:** CPU, memory, disk usage, GC pauses
    *   **Cassandra-specific:** Pending compactions, SSTable count, tombstone ratio
    *   **Tools:** nodetool, Prometheus + Grafana, DataStax OpsCenter

*   **Maintenance Operations:**
    *   **Adding nodes:** `nodetool join` (automatic token allocation with vnodes)
    *   **Removing nodes:** `nodetool decommission` or `nodetool removenode`
    *   **Repair:** `nodetool repair` (anti-entropy, read repair not sufficient)
    *   **Cleanup:** `nodetool cleanup` (remove data no longer owned)

### **I. Common Pitfalls & Best Practices**
*   **Data Modeling Anti-patterns:**
    *   **Unbounded partition growth** - Use time buckets
    *   **High-cardinality secondary indexes** - Use index tables instead
    *   **Excessive batch size** - Batches > 5KB can cause timeouts
    *   **Too many collections** - Collections have 64K item limit

*   **Performance Anti-patterns:**
    *   **ALL consistency level** in production (use QUORUM)
    *   **Unmonitored tombstone accumulation** (causes slow reads)
    *   **Inadequate compaction** strategy for workload
    *   **Too many SSTables** per read (compact regularly)

*   **Operational Best Practices:**
    *   **Regular repairs** (weekly for RF=3)
    *   **Monitor tombstone ratio** (>20% problematic)
    *   **Use prepared statements** (performance + security)
    *   **Test failure scenarios** (node loss, network partition)
    *   **Backup strategy** - Snapshot + incremental backups

### **J. Cassandra vs. Alternatives**
*   **vs. MongoDB:**
    *   **Consistency:** Eventual (Cassandra) vs. Strong (MongoDB transactions)
    *   **Architecture:** Masterless vs. Primary-Secondary
    *   **Write performance:** Better in Cassandra (append-only)
    *   **Query flexibility:** Better in MongoDB (rich queries, aggregation)

*   **vs. HBase:**
    *   **Consistency:** Eventual (Cassandra) vs. Strong (HBase)
    *   **Architecture:** P2P vs. Master-Slave
    *   **Hadoop integration:** Better in HBase
    *   **Operational complexity:** Simpler in Cassandra

*   **vs. DynamoDB:**
    *   **Managed vs. Self-managed:** DynamoDB serverless
    *   **Pricing:** Provisioned capacity vs. on-demand
    *   **Global tables:** Both support, different implementations
    *   **Feature set:** DynamoDB streams, backup, point-in-time recovery

---

## **3. Summary / Synthesis**
Apache Cassandra is a **distributed, wide-column NoSQL database** designed for **massive scalability, high availability, and write optimization**. Its **masterless ring architecture** with **virtual nodes** enables linear scalability and fault tolerance, while **tunable consistency** allows balancing availability and data freshness. Cassandra employs a **partition key + clustering column** data model that requires **denormalized, query-driven table design**, following the "one table per query" philosophy. It excels at **time-series data, high-velocity writes, and globally distributed applications**, offering **predictable performance at scale** through its append-only write path and SSTable storage. However, it requires **careful data modeling, regular maintenance operations, and acceptance of eventual consistency** trade-offs, making it best suited for specific high-scale use cases rather than general-purpose applications.

---

## **4. Key Terms / Vocabulary**
*   **Partition Key:** Determines data distribution across nodes (hash-based)
*   **Clustering Columns:** Determines sort order within partition
*   **SSTable:** Sorted String Table - immutable on-disk data structure
*   **Memtable:** In-memory write buffer
*   **Commit Log:** Write-ahead log for durability
*   **Vnode (Virtual Node):** Multiple tokens per physical node for even distribution
*   **Replication Factor (RF):** Number of copies of each piece of data
*   **Consistency Level (CL):** Number of replicas that must acknowledge operation
*   **Coordinator Node:** Node that receives client request and coordinates with replicas
*   **Bloom Filter:** Probabilistic data structure to test set membership
*   **Compaction:** Merging SSTables and removing deleted data
*   **Tombstone:** Marker for deleted data (removed during compaction)
*   **Snitch:** Determines network topology for replica placement

---

## **5. Potential Interview Questions**

### **Fundamental Concepts:**
1. "Explain how Cassandra achieves linear scalability and fault tolerance through its ring architecture."
2. "What is the difference between partition key and clustering columns, and how do they affect query patterns?"
3. "How does Cassandra's write path differ from traditional databases, and why is it write-optimized?"
4. "Explain tunable consistency in Cassandra with examples of different consistency levels."
5. "What are virtual nodes and what problems do they solve in Cassandra?"

### **Data Modeling Scenarios:**
1. "Design a time-series schema for IoT sensor data with 10,000 devices sending data every minute."
2. "How would you model a social media user's timeline in Cassandra (posts, comments, likes)?"
3. "Design an e-commerce product catalog with variant products in Cassandra."
4. "How would you implement a messaging system (like WhatsApp) using Cassandra?"
5. "Design a metrics collection system for application performance monitoring."

### **Performance & Optimization:**
1. "How would you troubleshoot slow reads in a Cassandra cluster?"
2. "What compaction strategy would you choose for different workloads and why?"
3. "How do you prevent unbounded partition growth in time-series data?"
4. "What monitoring would you implement for a production Cassandra cluster?"
5. "How would you handle hot partitions in a globally distributed application?"

### **Operational Questions:**
1. "How would you add new nodes to a Cassandra cluster without downtime?"
2. "What is the repair process and why is it necessary even with replication?"
3. "How would you implement a multi-datacenter deployment with Cassandra?"
4. "What backup and recovery strategies work best for Cassandra?"
5. "How do you handle schema changes in a production Cassandra cluster?"

### **System Design Scenarios:**
1. "Design a Netflix-like recommendation system using Cassandra."
2. "How would you build a real-time analytics platform for ad impressions?"
3. "Design a distributed session store for a global web application."
4. "How would you implement a distributed queue using Cassandra?"
5. "Design a fraud detection system processing millions of transactions daily."

---

## **6. How to Use These Notes & Key Takeaways**

### **Interview Preparation Strategy:**
1. **Master the Architecture** - Understand ring, vnodes, replication
2. **Practice Data Modeling** - Given requirements, design primary keys and tables
3. **Know Consistency Trade-offs** - Be able to explain CAP and tunable consistency
4. **Understand Performance Characteristics** - Write vs. read optimization, compaction
5. **Prepare Operational Scenarios** - Adding nodes, repairs, monitoring

### **Key Takeaways for Interviews:**
1. **Cassandra = Scalability + Availability** - Designed for massive scale and uptime
2. **Data Model is Critical** - Partition key choice determines everything
3. **Denormalization is Required** - "One table per query" philosophy
4. **Operations Matter** - Compaction, repairs, monitoring essential
5. **Know When to Use It** - Not for everything; specific high-scale use cases

### **During the Interview:**
1. **Start with Requirements Analysis:**
   - "What's the write vs. read ratio?"
   - "What are the consistency requirements?"
   - "What's the data volume and growth rate?"
   - "Is this time-series or other specific pattern?"
2. **Evaluate Cassandra Fit:**
   - High writes, time-series data → Good fit
   - Strong consistency required → Poor fit
   - Complex queries, joins → Poor fit
   - Global scale, high availability → Good fit
3. **Design Data Model:**
   - Choose partition key for even distribution
   - Add clustering columns for sort order
   - Design tables per query pattern
   - Consider TTL and compaction strategy
4. **Address Operational Concerns:**
   - Replication factor and strategy
   - Consistency level choices
   - Monitoring and alerting
   - Backup and recovery

### **Common Pitfalls to Avoid:**
- ❌ "Use Cassandra for everything" (wrong for complex queries/transactions)
- ❌ "Ignore data modeling" (performance depends on it)
- ❌ "Forget about compaction" (causes performance degradation)
- ❌ "No monitoring" (operational issues go undetected)
- ❌ "Use ALL consistency" (kills availability and performance)

### **Signs of Strong Candidates:**
- ✅ Asks about write patterns and consistency requirements first
- ✅ Designs partition keys for even distribution
- ✅ Discusses denormalization and table-per-query patterns
- ✅ Mentions operational aspects (compaction, repairs, monitoring)
- ✅ Understands when Cassandra is NOT appropriate

---

## **7. Quick Review Cheat Sheet**

### **Data Modeling Guidelines:**
1. **Partition Size:** Keep under 100MB (ideally 10-50MB)
2. **Partition Key:** High cardinality, even distribution
3. **Clustering Columns:** For sorting and filtering within partition
4. **Denormalize:** Create tables for each query pattern
5. **TTL:** Use for time-based data expiration

### **Consistency Level Selection:**
- **Development:** ONE (fastest)
- **Production reads:** LOCAL_QUORUM (balance)
- **Production writes:** LOCAL_QUORUM or QUORUM
- **Critical data:** QUORUM (strong but available)
- **Never in production:** ALL (unless absolutely required)

### **Compaction Strategy Guide:**
- **General purpose:** SizeTieredCompactionStrategy (STCS)
- **Read-intensive:** LeveledCompactionStrategy (LCS) - more disk I/O
- **Time-series:** TimeWindowCompactionStrategy (TWCS) - auto-expire
- **Mixed workload:** DateTieredCompactionStrategy (DTCS) - time-ordered

### **Monitoring Critical Metrics:**
1. **Performance:** Read/write latency (P95, P99), throughput
2. **Compaction:** Pending tasks, SSTable count
3. **Memory:** Heap usage, memtable size
4. **Disk:** Usage, I/O latency
5. **GC:** Pause time and frequency

### **When to Choose Cassandra:**
✅ **Good for:**
- Time-series data (IoT, metrics, logs)
- High-velocity writes (thousands/sec per node)
- Global-scale applications
- High availability requirements
- Simple query patterns (by primary key)

❌ **Avoid for:**
- Complex transactions (use RDBMS)
- Complex queries/joins (use document DB)
- Strong consistency requirements (use CP system)
- Small datasets (< 100GB, use simpler solution)
- Ad-hoc querying (use search engine)

### **Production Checklist:**
1. [ ] RF=3 minimum (odd number for quorum)
2. [ ] NetworkTopologyStrategy for multi-DC
3. [ ] Regular repairs (weekly for RF=3)
4. [ ] Monitor tombstone ratio (<20%)
5. [ ] Appropriate compaction strategy
6. [ ] Backup strategy (snapshot + incremental)
7. [ ] Load testing before production

### **Performance Troubleshooting Flow:**
1. Check **pending compactions** (nodetool compactionstats)
2. Check **tombstone ratio** (nodetool tablestats)
3. Check **SSTable count** (nodetool cfstats)
4. Check **GC pauses** (JVM metrics)
5. Check **partition size** (large partitions slow reads)
6. Check **consistency level** (ALL causes timeouts)

This comprehensive guide provides deep knowledge of Apache Cassandra for system design interviews, covering architecture, data modeling, operations, and practical implementation considerations.