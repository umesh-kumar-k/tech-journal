# **NoSQL System Design Cheat Sheet**

## **1. Essential Question**
*How do NoSQL databases function in distributed system design, and what are the key patterns, trade-offs, and architectures for scaling modern applications?*

---

## **2. Main Ideas & Key Details**

### **A. NoSQL Database Types & Characteristics**
*   **Four Primary Categories:**
    *   **Key-Value Stores:** Simple key → value mapping (Redis, DynamoDB, Riak)
        *   **Use Cases:** Caching, session storage, feature flags
        *   **Strengths:** Extremely fast reads/writes, simple API
    *   **Document Databases:** Store semi-structured documents (MongoDB, Couchbase, Firestore)
        *   **Use Cases:** Content management, user profiles, catalogs
        *   **Strengths:** Flexible schema, rich querying, horizontal scaling
    *   **Column-Family Stores:** Tabular format with dynamic columns (Cassandra, HBase, Bigtable)
        *   **Use Cases:** Time-series data, analytics, write-heavy workloads
        *   **Strengths:** Massive scalability, fast writes, efficient compression
    *   **Graph Databases:** Nodes + edges + properties (Neo4j, Amazon Neptune, JanusGraph)
        *   **Use Cases:** Social networks, recommendation engines, fraud detection
        *   **Strengths:** Relationship traversal, complex pattern matching

*   **Core NoSQL Principles:**
    *   **BASE Properties:** Basically Available, Soft state, Eventual consistency
    *   **Schema-less/Flexible Schema:** Data structure can evolve without migrations
    *   **Horizontal Scalability:** Designed to scale out across commodity hardware
    *   **Distributed-First Architecture:** Built for multi-node deployments

### **B. Data Modeling Patterns**
*   **Denormalization as Default:**
    *   Embed related data in single documents/rows to avoid joins
    *   Trade-off: Data duplication vs. read performance
    *   **Pattern:** "Read-optimized" data structures
*   **Aggregate-Oriented Design:**
    *   Group data accessed together into single aggregates
    *   **Example:** Order with all line items in one document
    *   **Boundary:** Transaction boundary = aggregate boundary
*   **Access Pattern-Driven Design:**
    *   Model data based on how it's queried, not "natural" relationships
    *   **Rule:** "Design for the queries, not the data"
    *   May require multiple representations of same data
*   **Time-Series Patterns:**
    *   **Time bucketing:** Group data by time intervals (hour/day/month)
    *   **Rollup aggregation:** Store pre-aggregated summaries
    *   **TTL (Time-To-Live):** Automatic data expiration

### **C. Distributed Architecture & Consistency**
*   **CAP Theorem Application:**
    *   **AP Systems:** Prioritize Availability + Partition Tolerance (Cassandra, DynamoDB, CouchDB)
    *   **CP Systems:** Prioritize Consistency + Partition Tolerance (MongoDB, HBase, Redis)
    *   **Note:** Many systems offer configurable consistency levels
*   **Consistency Models:**
    *   **Strong Consistency:** Linearizability, all reads see most recent write
    *   **Eventual Consistency:** Replicas converge over time
    *   **Causal Consistency:** Preserves cause-effect relationships
    *   **Read-Your-Writes Consistency:** User sees their own writes
    *   **Session Consistency:** Consistency within a user session
*   **Replication Strategies:**
    *   **Leader-Follower:** Single leader accepts writes, followers replicate (MongoDB)
    *   **Multi-Leader:** Multiple nodes accept writes (conflict resolution needed)
    *   **Leaderless:** Any node can accept reads/writes (Dynamo-style)
    *   **Quorum-based:** R + W > N for consistency (where R=read quorum, W=write quorum, N=replicas)

### **D. Partitioning/Sharding Strategies**
*   **Hash-Based Partitioning:**
    *   Hash key determines partition (consistent hashing minimizes rebalancing)
    *   **Advantages:** Even data distribution
    *   **Disadvantages:** Range queries inefficient
*   **Range-Based Partitioning:**
    *   Partition by key ranges (e.g., A-F, G-M, N-Z)
    *   **Advantages:** Efficient range queries
    *   **Disadvantages:** Potential hotspots, uneven distribution
*   **Directory-Based Partitioning:**
    *   Lookup service maps keys to partitions
    *   **Advantages:** Flexible, easy rebalancing
    *   **Disadvantages:** Single point of failure (lookup service)
*   **Composite Partitioning:**
    *   Combine multiple strategies (e.g., partition by tenant, then hash by user)
*   **Hotspot Mitigation:**
    *   **Salting:** Add random prefix to keys
    *   **Split Hot Partitions:** Divide overloaded partitions
    *   **Write Buffering:** Queue writes to hot partitions

### **E. Common Design Patterns**
*   **Command Query Responsibility Segregation (CQRS):**
    *   Separate models for writes (commands) and reads (queries)
    *   **Write Model:** Normalized, transactional
    *   **Read Model:** Denormalized, optimized for queries
    *   **Sync Mechanism:** Event sourcing, change data capture
*   **Event Sourcing:**
    *   Store state changes as immutable events
    *   Rebuild state by replaying events
    *   Enables temporal queries, audit trails
*   **Materialized Views:**
    *   Pre-computed query results stored as tables/collections
    *   Updated asynchronously from source data
    *   Trade-off: Staleness vs. query performance
*   **Polyglot Persistence:**
    *   Use multiple database technologies in single system
    *   **Example:** Redis for caching + MongoDB for documents + Neo4j for relationships
    *   Each database handles what it's best at

### **F. Performance & Optimization**
*   **Indexing Strategies:**
    *   **Secondary Indexes:** Can be global or local to partition
    *   **Composite Indexes:** Multiple fields, order matters
    *   **Partial Indexes:** Index subset of documents
    *   **TTL Indexes:** Auto-expire data after time period
*   **Caching Layers:**
    *   **Application Cache:** Redis/Memcached for frequently accessed data
    *   **Database Cache:** Built-in query/result caching
    *   **CDN Cache:** For static/read-only data
*   **Write Optimization:**
    *   **Bulk Writes:** Group operations
    *   **Write Concern Levels:** Control acknowledgment requirements
    *   **Compaction:** Merge SSTables (LSM trees) or defragment collections
*   **Read Optimization:**
    *   **Read Preference:** Direct reads to secondaries
    *   **Projection:** Return only needed fields
    *   **Pagination:** Limit + skip or range-based

---

## **3. Summary / Synthesis**
NoSQL databases provide flexible, scalable alternatives to traditional RDBMS by embracing **distributed-first architectures, flexible data models, and tunable consistency**. The key insight is that **different data access patterns require different storage solutions**, leading to polyglot persistence. Successful NoSQL system design involves **choosing the right database type for each use case, modeling data based on access patterns rather than relationships, and understanding the trade-offs between consistency, availability, and partition tolerance**. Modern applications often combine multiple NoSQL databases (and sometimes SQL) to achieve optimal performance at scale, using patterns like CQRS and event sourcing to maintain consistency across bounded contexts.

---

## **4. Key Terms / Vocabulary**
*   **BASE:** Basically Available, Soft state, Eventual consistency
*   **CAP Theorem:** Consistency, Availability, Partition Tolerance trade-off
*   **Eventual Consistency:** Guarantee that all replicas converge over time
*   **Quorum:** Minimum number of nodes that must respond for operation success
*   **Consistent Hashing:** Hashing that minimizes data movement when nodes added/removed
*   **CQRS:** Command Query Responsibility Segregation
*   **Event Sourcing:** Storing state changes as immutable events
*   **Materialized View:** Pre-computed query result stored for fast access
*   **Polyglot Persistence:** Using multiple database technologies in one system
*   **Shard/Partition:** Horizontal division of data across nodes
*   **TTL:** Time-To-Live (automatic data expiration)

---

## **5. Potential Interview Questions**

### **Fundamental Concepts:**
1. "Explain the differences between the four main types of NoSQL databases and when you'd use each."
2. "How does the CAP theorem apply differently to MongoDB vs. Cassandra?"
3. "What are the trade-offs between denormalization and normalization in NoSQL?"
4. "Explain eventual consistency and when it's acceptable vs. problematic."
5. "How does quorum-based consistency work in distributed systems?"

### **Design Scenarios:**
1. "Design a social media feed system using NoSQL databases."
2. "How would you design an e-commerce catalog with real-time inventory?"
3. "Design a monitoring system for IoT devices sending millions of events daily."
4. "How would you implement a recommendation engine using graph databases?"
5. "Design a multi-tenant SaaS application with data isolation requirements."

### **Scaling & Performance:**
1. "How would you handle hotspots in a key-value store?"
2. "What strategies would you use for time-series data in NoSQL?"
3. "How do you implement pagination efficiently in a sharded database?"
4. "What caching strategies work best with NoSQL databases?"
5. "How would you migrate from a monolithic RDBMS to distributed NoSQL?"

### **Consistency & Transactions:**
1. "How do you implement transactional behavior in eventually consistent systems?"
2. "What patterns ensure 'read-your-writes' consistency in distributed databases?"
3. "How would you handle conflicting writes in a multi-leader replication setup?"
4. "What's the difference between strong and eventual consistency in practice?"
5. "How do you implement distributed transactions without 2PC?"

### **Real-World Problems:**
1. "How would you handle schema evolution in a document database?"
2. "What monitoring would you implement for a NoSQL cluster?"
3. "How do you backup and restore a distributed NoSQL database?"
4. "What security considerations are unique to NoSQL databases?"
5. "How would you implement full-text search alongside your NoSQL database?"

---

## **6. How to Use These Notes & Key Takeaways**

### **Interview Preparation Strategy:**
1. **Map Database Types to Use Cases** - Know exactly when to use document vs. column vs. graph databases
2. **Practice Data Modeling Exercises** - Given requirements, design the NoSQL schema
3. **Understand Consistency Trade-offs** - Be able to explain why you'd choose eventual vs. strong consistency
4. **Prepare Real Examples** - Have case studies of NoSQL implementations you've worked with
5. **Know Migration Scenarios** - Be ready to discuss moving from SQL to NoSQL or between NoSQL systems

### **Key Takeaways for Interviews:**
1. **NoSQL ≠ Anti-SQL** - They're different tools for different problems; many systems use both
2. **Consistency is Configurable** - Many NoSQL databases offer multiple consistency levels
3. **Design for Scale from Day 1** - Even if you don't need it immediately, choose scalable patterns
4. **Access Patterns Drive Design** - Always ask "How will this data be queried?" first
5. **Operations Matter** - Distributed databases have operational complexity (monitoring, backups, repairs)

### **During the Interview:**
1. **Start with Requirements Analysis:**
   - "What are the read/write patterns?"
   - "What consistency guarantees are needed?"
   - "What's the data growth projection?"
   - "What are the query patterns?"
2. **Choose Database Type Intelligently:**
   - Document DB for flexible, hierarchical data
   - Column DB for time-series, analytics
   - Key-Value for caching, sessions
   - Graph DB for relationship-heavy data
3. **Discuss Trade-offs Explicitly:**
   - "We could denormalize for faster reads, but then we have to update multiple places"
   - "Eventual consistency gives us better availability but requires handling stale reads"
   - "Sharding improves scale but makes cross-shard queries expensive"
4. **Consider the Full Stack:**
   - How does caching layer interact with database?
   - How do microservices access the data?
   - What's the backup/restore strategy?

### **Common Pitfalls to Avoid:**
- ❌ "We'll use NoSQL because it's more scalable" (without considering consistency needs)
- ❌ "Joins aren't possible" (they are, just different patterns)
- ❌ "Schemaless means no schema design" (design is actually more important)
- ❌ "Eventual consistency for everything" (financial data needs strong consistency)
- ❌ "One database to rule them all" (polyglot persistence is often better)

### **Signs of Strong Candidates:**
- ✅ Asks about access patterns before choosing database type
- ✅ Considers both read and write performance requirements
- ✅ Discusses consistency requirements for different data types
- ✅ Has a plan for schema evolution and data migration
- ✅ Understands operational aspects (monitoring, backups, scaling)

---

## **7. Quick Review Cheat Sheet**

### **Database Type Selection Guide:**
- **Key-Value:** Sessions, caching, user preferences, counters
- **Document:** User profiles, content management, product catalogs
- **Column-Family:** Time-series, analytics, write-heavy logging
- **Graph:** Social networks, recommendations, fraud detection, knowledge graphs

### **Consistency Decision Framework:**
1. **Can stale reads cause harm?** → Strong consistency
2. **Is write latency critical?** → Eventual consistency
3. **Do users need to see their own writes?** → Session consistency
4. **Is high availability required?** → Eventual consistency with quorum reads

### **Sharding Strategy Selection:**
- **Hash-based:** Even distribution needed, no range queries
- **Range-based:** Natural ordering, range queries needed
- **Composite:** Multi-tenant + per-tenant distribution
- **Directory:** Flexible sharding, frequent rebalancing expected

### **Essential Patterns by Problem:**
- **Joins in NoSQL:** Embedding, application-side joins, materialized views
- **Hotspots:** Salting, splitting partitions, write buffering
- **Schema Evolution:** Version documents, backward compatibility, gradual migration
- **Cross-Shard Queries:** Denormalization, query fan-out, specialized query nodes

### **Performance Optimization Checklist:**
1. **Right indexes** for query patterns
2. **Appropriate consistency levels** for use case
3. **Effective caching** strategy
4. **Proper sharding key** to avoid hotspots
5. **Query optimization** (projection, batching, pagination)
6. **Hardware/configuration tuning** (memory, disk, compression)

This comprehensive guide provides the depth needed to discuss NoSQL databases confidently in system design interviews, covering everything from fundamental concepts to advanced distributed patterns.


# **AWS NoSQL Database Types**

**Source:** AWS Whitepaper, "Choosing an AWS NoSQL Database: Types of NoSQL Databases"  
**URL:** https://docs.aws.amazon.com/whitepapers/latest/choosing-an-aws-nosql-database/types-of-nosql-databases.html

## **1. Essential Question**
*What are the different types of NoSQL databases available on AWS, and how do their data models, capabilities, and use cases differ for modern applications?*

---

## **2. Main Ideas & Key Details**

### **A. The NoSQL Spectrum on AWS**
*   **AWS Philosophy:** "Right tool for the right job" - different NoSQL databases for different use cases
*   **Core Categories:** Key-value, document, wide-column, graph, in-memory, search, time-series
*   **Managed Service Advantage:** AWS handles operational overhead (scaling, backups, patches)

### **B. Key-Value Databases**
*   **Data Model:**
    *   Simple key → value pairs
    *   Values can be strings, numbers, binaries, or semi-structured data
    *   **Key characteristics:** Simple API, fast lookups, horizontal scaling

*   **AWS Services:**
    *   **Amazon DynamoDB:** Fully managed, serverless, multi-region
        - **Features:** Auto-scaling, ACID transactions, global tables
        - **Use cases:** High-traffic web apps, gaming, IoT
    *   **Amazon S3 (as key-value store):** Object storage with key-based access
        - **Features:** Durable, cheap, versioning
        - **Use cases:** Static assets, data lakes, backups

*   **Ideal Use Cases:**
    - Session management
    - User profiles and preferences
    - Shopping carts
    - Real-time leaderboards
    - Metadata storage

*   **Advantages:**
    - Simple data model
    - High performance (O(1) lookups)
    - Easy horizontal scaling
    - Predictable performance at scale

*   **Limitations:**
    - Limited query capabilities (primary key lookups)
    - No complex data relationships
    - Simple value types only

### **C. Document Databases**
*   **Data Model:**
    *   Store semi-structured documents (JSON, BSON, XML)
    *   Documents can have nested structures
    *   **Key characteristics:** Flexible schema, rich queries, indexing

*   **AWS Services:**
    *   **Amazon DocumentDB (with MongoDB compatibility):**
        - MongoDB API compatible
        - **Features:** Fully managed, scalable, durable
        - **Use cases:** Content management, catalogs, user profiles
    *   **AWS offering note:** Also supports MongoDB Atlas on AWS Marketplace

*   **Ideal Use Cases:**
    - Content management systems
    - E-commerce product catalogs
    - User profiles with varying attributes
    - Real-time analytics
    - Mobile and web applications

*   **Advantages:**
    - Flexible, evolving schema
    - Rich query capabilities
    - Natural mapping to object-oriented programming
    - Horizontal scalability

*   **Limitations:**
    - No native joins across documents
    - Potential data duplication
    - Consistency model trade-offs

### **D. Wide-Column (Column-Family) Databases**
*   **Data Model:**
    *   Tabular format but columns vary by row
    *   Organized by column families (logical groupings)
    *   **Key characteristics:** Excellent for write-heavy workloads, time-series data

*   **AWS Services:**
    *   **Amazon Keyspaces (for Apache Cassandra):**
        - Cassandra-compatible
        - **Features:** Serverless, auto-scaling, multi-region
        - **Use cases:** Time-series, IoT, high-velocity data
    *   **Amazon Timestream:** Purpose-built for time-series
        - **Features:** Automatic scaling, tiered storage, built-in analytics
        - **Use cases:** IoT, DevOps monitoring, industrial telemetry

*   **Ideal Use Cases:**
    - Time-series data (IoT sensors, metrics)
    - High-velocity write workloads
    - Event logging and analytics
    - Industrial telemetry
    - Financial tick data

*   **Advantages:**
    - Excellent write performance
    - Efficient storage (columnar compression)
    - Horizontal scaling
    - Time-based data management

*   **Limitations:**
    - Complex data model
    - Limited transactional support
    - Eventual consistency default

### **E. Graph Databases**
*   **Data Model:**
    *   Nodes (entities) + edges (relationships) + properties
    *   **Key characteristics:** Relationship-centric, traversal efficiency

*   **AWS Services:**
    *   **Amazon Neptune:**
        - Fully managed graph database
        - **Features:** Supports Property Graph and RDF models
        - **Query languages:** Gremlin, SPARQL, openCypher
        - **Use cases:** Social networks, recommendations, fraud detection

*   **Ideal Use Cases:**
    - Social networking (friend recommendations)
    - Fraud detection (pattern recognition)
    - Knowledge graphs
    - Recommendation engines
    - Network and IT operations

*   **Advantages:**
    - Efficient relationship traversal
    - Flexible schema for connections
    - Powerful pattern matching
    - ACID compliance (Neptune)

*   **Limitations:**
    - Not optimized for tabular data
    - Learning curve for graph queries
    - Specific to relationship-heavy use cases

### **F. In-Memory Databases**
*   **Data Model:**
    *   Data stored primarily in memory
    *   **Key characteristics:** Ultra-low latency, high throughput

*   **AWS Services:**
    *   **Amazon ElastiCache (Redis/Memcached):**
        - **Redis:** Rich data structures, persistence, replication
        - **Memcached:** Simple, multi-threaded, high performance
        - **Use cases:** Caching, session stores, real-time analytics
    *   **Amazon MemoryDB for Redis:**
        - Redis-compatible, durable
        - **Features:** Multi-AZ, microsecond latency
        - **Use cases:** Session management, real-time analytics

*   **Ideal Use Cases:**
    - Real-time leaderboards
    - Gaming session state
    - Caching layer for databases
    - Message brokering (Redis Pub/Sub)
    - Geospatial operations

*   **Advantages:**
    - Microsecond latency
    - High throughput
    - Rich data structures (Redis)
    - Persistence options

*   **Limitations:**
    - Cost (memory is expensive)
    - Data size limited by memory
    - Data volatility risk (if not persisted)

### **G. Search Databases**
*   **Data Model:**
    *   Optimized for full-text search and analytics
    *   **Key characteristics:** Text analysis, relevance ranking, faceted search

*   **AWS Services:**
    *   **Amazon OpenSearch Service (Elasticsearch):**
        - Fully managed OpenSearch/Elasticsearch
        - **Features:** Real-time search, analytics, visualization
        - **Use cases:** Log analytics, search applications, clickstream analysis

*   **Ideal Use Cases:**
    - Enterprise search
    - Log and event data analysis
    - Application monitoring
    - E-commerce product search
    - Security analytics

*   **Advantages:**
    - Powerful full-text search
    - Rich analytics capabilities
    - Real-time indexing
    - Integration with Kibana for visualization

*   **Limitations:**
    - Not a general-purpose database
    - Complex cluster management
    - Query performance varies with data volume

### **H. Time-Series Databases**
*   **Data Model:**
    *   Optimized for time-ordered data
    *   **Key characteristics:** Efficient time-based queries, data lifecycle management

*   **AWS Services:**
    *   **Amazon Timestream:**
        - Serverless time-series database
        - **Features:** Automatic scaling, tiered storage, SQL interface
        - **Use cases:** IoT applications, DevOps monitoring, industrial analytics

*   **Ideal Use Cases:**
    - IoT sensor data
    - Application performance monitoring
    - Financial tick data
    - Industrial equipment monitoring
    - Clickstream analysis

*   **Advantages:**
    - Optimized storage for time-series
    - Efficient time-based queries
    - Built-in data retention policies
    - Cost-effective tiered storage

*   **Limitations:**
    - Specialized for time-series only
    - Limited for non-time-based queries
    - Newer service (evolving features)

### **I. Choosing Criteria Matrix**
*   **Data Structure & Access Patterns:**
    - **Key-value:** Simple lookups by key
    - **Document:** Hierarchical, semi-structured data
    - **Wide-column:** Time-series, high-write volume
    - **Graph:** Relationship-heavy queries
    - **In-memory:** Ultra-low latency needs

*   **Performance Requirements:**
    - **Latency:** In-memory (microseconds) vs. disk-based (milliseconds)
    - **Throughput:** Wide-column for writes, key-value for reads
    - **Consistency:** Choose based on application needs

*   **Scalability Needs:**
    - **Auto-scaling:** DynamoDB, DocumentDB, Keyspaces
    - **Manual scaling:** Some require capacity planning
    - **Global distribution:** DynamoDB Global Tables, Neptune multi-AZ

*   **Operational Management:**
    - **Fully managed:** AWS handles backups, patches, scaling
    - **Serverless:** Timestream, DynamoDB on-demand
    - **Self-managed:** EC2 instances with database software

### **J. Hybrid Approaches & Polyglot Persistence**
*   **Common Patterns:**
    - **Caching layer:** ElastiCache in front of primary database
    - **Search index:** OpenSearch alongside transactional database
    - **Analytics pipeline:** Time-series + data warehouse
    - **Graph relationships:** Neptune alongside document store

*   **AWS Integration Patterns:**
    - **DynamoDB Streams + Lambda:** Event-driven architectures
    - **Kinesis Data Streams + Timestream:** Real-time ingestion
    - **AWS Glue:** ETL between different data stores
    - **Amazon Athena:** Query data across multiple sources

*   **Data Sync Strategies:**
    - Change Data Capture (CDC)
    - Event sourcing patterns
    - Dual-write strategies
    - Materialized views

---

## **3. Summary / Synthesis**
AWS offers a comprehensive suite of **purpose-built NoSQL databases**, each optimized for specific data models and use cases. **Key-value stores (DynamoDB)** excel at simple, high-scale lookups; **document databases (DocumentDB)** handle flexible, hierarchical data; **wide-column stores (Keyspaces, Timestream)** manage time-series and high-velocity writes; **graph databases (Neptune)** specialize in relationship traversal; **in-memory databases (ElastiCache)** provide ultra-low latency; **search databases (OpenSearch)** enable full-text search and analytics; and **time-series databases (Timestream)** optimize for time-ordered data. The key insight is **choosing based on access patterns rather than forcing one database to handle all workloads**, often employing **polyglot persistence** with multiple database types in a single application architecture. AWS's managed services reduce operational overhead while providing enterprise-grade features like global distribution, auto-scaling, and built-in security.

---

## **4. Key Terms / Vocabulary**
*   **Polyglot Persistence:** Using multiple database technologies in a single system
*   **Serverless Database:** Automatically scales based on demand, no capacity planning
*   **Global Tables:** Multi-region replication with low latency access
*   **Time-Series Database:** Optimized for time-ordered data with efficient time-based queries
*   **Column-Family Database:** Tabular format with variable columns per row
*   **Graph Traversal:** Navigating relationships between nodes in a graph
*   **In-Memory Database:** Data primarily stored in RAM for ultra-low latency
*   **Document Database:** Stores semi-structured documents (JSON/BSON/XML)
*   **ACID Transactions:** Atomicity, Consistency, Isolation, Durability guarantees
*   **Eventually Consistent:** Replicas converge over time, not immediately

---

## **5. Potential Interview Questions**

### **Database Selection Scenarios:**
1. "You're building a real-time gaming platform. Which AWS NoSQL databases would you use and why?"
2. "Design an e-commerce system with product search, user sessions, and recommendation engine."
3. "How would you architect an IoT platform for millions of sensors sending data every second?"
4. "Choose databases for a social media platform with friend networks and activity feeds."
5. "Design a financial trading platform with real-time analytics and audit trails."

### **Technical Comparison Questions:**
1. "When would you choose DynamoDB over DocumentDB, and vice versa?"
2. "Compare Amazon Keyspaces vs. Amazon Timestream for time-series data."
3. "What are the trade-offs between ElastiCache Redis and MemoryDB for Redis?"
4. "When is Neptune a better choice than modeling relationships in a document database?"
5. "Compare OpenSearch as a search engine vs. as a primary data store."

### **Architecture & Integration:**
1. "How would you implement a caching layer using ElastiCache with DynamoDB?"
2. "Design an event-driven architecture using DynamoDB Streams and Lambda."
3. "How would you synchronize data between a document database and a search index?"
4. "What patterns would you use for global data distribution across regions?"
5. "How do you handle schema evolution in a polyglot persistence architecture?"

### **Performance & Scaling:**
1. "How does DynamoDB achieve single-digit millisecond latency at scale?"
2. "What are the scaling considerations when using Neptune for social graphs?"
3. "How would you optimize a time-series database for both recent and historical queries?"
4. "What monitoring would you implement for a multi-database architecture?"
5. "How do you handle hot partitions in a globally distributed key-value store?"

### **Operational Questions:**
1. "What are the backup and restore strategies for different AWS NoSQL services?"
2. "How do you implement disaster recovery across multiple database types?"
3. "What security considerations differ between document and graph databases?"
4. "How would you migrate from a self-managed MongoDB to Amazon DocumentDB?"
5. "What cost optimization strategies apply to different NoSQL services?"

---

## **6. How to Use These Notes & Key Takeaways**

### **Interview Preparation Strategy:**
1. **Memorize the Database Matrix** - Know which AWS service fits which use case
2. **Understand Trade-offs** - Be able to justify database choices with specific reasoning
3. **Know Integration Patterns** - How different databases work together
4. **Prepare Real Examples** - Have specific architectures you've designed or worked with
5. **Understand Pricing Models** - On-demand vs. provisioned capacity, storage costs

### **Key Takeaways for Interviews:**
1. **AWS Has Purpose-Built Services** - Different databases for different problems
2. **Polyglot Persistence is Common** - Real systems use multiple database types
3. **Managed Services Reduce Overhead** - Focus on application logic, not operations
4. **Global Scale is Built-in** - Many services support multi-region deployment
5. **Serverless is the Future** - Automatic scaling, pay-per-use models

### **During the Interview:**
1. **Start with Requirements Analysis:**
   - "What are the data access patterns?"
   - "What are the performance requirements (latency, throughput)?"
   - "What are the scalability needs (current and projected)?"
   - "What are the consistency requirements?"
2. **Map Requirements to Services:**
   - Simple key lookups → DynamoDB
   - Complex documents → DocumentDB
   - Relationships → Neptune
   - Time-series → Timestream or Keyspaces
   - Caching → ElastiCache
   - Search → OpenSearch
3. **Design Integration Patterns:**
   - How data flows between services
   - Event-driven architectures
   - Caching strategies
   - Data synchronization
4. **Consider Operational Aspects:**
   - Monitoring and alerting
   - Backup and recovery
   - Security and compliance
   - Cost optimization

### **Common Pitfalls to Avoid:**
- ❌ "Use DynamoDB for everything" (wrong tool for graph or search)
- ❌ "Ignore cost implications" (in-memory is expensive, plan capacity)
- ❌ "Forget about global users" (consider latency and data residency)
- ❌ "No backup strategy" (even managed services need backup planning)
- ❌ "Ignore monitoring" - each database has different metrics

### **Signs of Strong Candidates:**
- ✅ Asks about access patterns and requirements first
- ✅ Considers multiple database types and their integration
- ✅ Discusses trade-offs between different AWS services
- ✅ Mentions operational considerations (monitoring, backups, security)
- ✅ Understands cost implications of different choices

---

## **7. Quick Review Cheat Sheet**

### **AWS NoSQL Service Selection Matrix:**
| **Use Case** | **Primary Service** | **Alternatives** | **Key Consideration** |
|-------------|-------------------|-----------------|----------------------|
| **Session Management** | ElastiCache (Redis) | DynamoDB | Latency vs. durability |
| **User Profiles** | DocumentDB | DynamoDB | Schema flexibility needed? |
| **E-commerce Catalog** | DocumentDB | DynamoDB + OpenSearch | Search requirements |
| **IoT Time-Series** | Timestream | Keyspaces | Built-in analytics needed? |
| **Social Graph** | Neptune | DynamoDB + application logic | Relationship complexity |
| **Real-time Analytics** | ElastiCache (Redis) | DynamoDB Streams + Lambda | Real-time processing needs |
| **Full-Text Search** | OpenSearch | DynamoDB + OpenSearch | Primary vs. secondary store |

### **Performance Characteristics:**
- **Microsecond latency:** ElastiCache, MemoryDB
- **Single-digit ms:** DynamoDB, DocumentDB (hot data)
- **Tens of ms:** Neptune, Keyspaces, Timestream
- **Variable:** OpenSearch (depends on query complexity)

### **Scaling Approaches:**
- **Auto-scaling:** DynamoDB, DocumentDB, Keyspaces
- **Manual scaling:** Neptune (instance types), ElastiCache (node types)
- **Serverless:** Timestream, DynamoDB on-demand
- **Sharding:** All support horizontal scaling with different mechanisms

### **Global Distribution Options:**
- **Global Tables:** DynamoDB (multi-region, active-active)
- **Multi-AZ:** All services (high availability within region)
- **Read replicas:** DocumentDB, Neptune (cross-region reads)
- **Custom replication:** Application-level using streams/Lambda

### **Cost Optimization Tips:**
1. **DynamoDB:** Use on-demand for spiky workloads, provisioned for predictable
2. **ElastiCache:** Right-size instances, use reserved instances for steady state
3. **Timestream:** Leverage tiered storage (memory, magnetic, object)
4. **DocumentDB:** Monitor storage growth, use appropriate instance types
5. **Neptune:** Use burstable instances for development, optimized for production

### **Monitoring Essentials per Service:**
1. **DynamoDB:** Provisioned throughput usage, throttle events, latency
2. **ElastiCache:** Cache hit rate, memory usage, evictions
3. **DocumentDB:** CPU utilization, storage space, replica lag
4. **Neptune:** Graph query latency, connection count, storage
5. **Timestream:** Ingest rate, query performance, storage tiers

### **Migration Considerations:**
1. **Assess compatibility:** API compatibility, data model mapping
2. **Plan incremental cutover:** Dual-write, read from both during migration
3. **Test thoroughly:** Performance, functionality, failover scenarios
4. **Monitor during migration:** Performance impact, data consistency
5. **Have rollback plan:** Quick revert if issues arise

This comprehensive guide provides everything needed to discuss AWS NoSQL databases confidently in system design interviews, from service selection to architecture patterns and operational considerations.