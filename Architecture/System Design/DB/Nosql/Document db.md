
## MongoDB Deep Dive Interview Checklist

- **Document Model**
    
    - **Databases → Collections → Documents:** BSON (binary JSON) storage, dynamic schema.[mongodb](https://www.mongodb.com/resources/basics/databases/document-databases)​
        
    - **No Fixed Schema:** Documents vary within collection; embed arrays/objects.
        
    - **_id Field:** Auto-generated unique identifier (ObjectId).
        
- **Schema Design Principles**
    
    - **Embedding vs Referencing:** Embed for read-heavy (no JOINs), reference for normalization.
        
    - **Denormalization:** Duplicate data for query efficiency.
        
    - **Query Patterns:** Design documents around access patterns.
        
- **Scaling Architecture**
    
    |Component|Purpose|
    |---|---|
    |**Replica Sets**|High availability, read scaling (primary/secondary)|
    |**Sharding**|Horizontal scaling via shard key|
    |**Mongos**|Query router for sharded clusters|
    |**Config Servers**|Metadata about data distribution|
    
- **Query & Indexing**
    
    - **Aggregation Pipeline:** $match, $group, $sort, $project stages.
        
    - **Indexes:** Single/multi-field, compound, TTL, geospatial, text.
        
    - **Covered Queries:** Index-only reads (no document fetch).
        
- **ACID & Transactions**
    
    - **Multi-Document ACID:** Since v4.0 (snapshot isolation).
        
    - **Write Concern/Read Preference:** Tune consistency vs performance.
        
- **When to Use MongoDB**
    
    |✅ Good Fit|❌ Avoid|
    |---|---|
    |Content management, catalogs|Complex JOIN-heavy analytics|
    |Rapid schema evolution|Strict ACID multi-table txns|
    |Semi-structured data|Highly normalized relational data|
    
- **Tools & Frameworks**
    
    |Category|Tools|
    |---|---|
    |**Drivers**|Node.js, Spring Data MongoDB, PyMongo|
    |**ORMs**|Mongoose (Node), MongoEngine (Python)|
    |**GUI**|MongoDB Compass, Studio 3T|
    |**Cloud**|MongoDB Atlas (managed)|
    

## 60-Second Recap

- **Document Store:** BSON, dynamic schema, embed/reference trade-off.
    
- **Scaling:** Replica sets (HA) + sharding (horizontal).
    
- **Query:** Aggregation pipeline + rich indexing (TTL, geospatial).
    
- **Strengths:** Flexible schema, read scaling; **Limits:** No native JOINs.
    
- **Gold:** Design for reads, use Atlas, aggregation pipelines.
    

**Reference**: [MongoDB Document Databases](https://mongodb.com/resources/basics/databases/document-databases)[mongodb](https://www.mongodb.com/resources/basics/databases/document-databases)​

1. [https://www.mongodb.com/resources/basics/databases/document-databases](https://www.mongodb.com/resources/basics/databases/document-databases)
2. [https://www.mongodb.com/resources/products/fundamentals/mongodb-architecture-guide](https://www.mongodb.com/resources/products/fundamentals/mongodb-architecture-guide)
3. [https://www.geeksforgeeks.org/mongodb/mongodb-database-collection-and-document/](https://www.geeksforgeeks.org/mongodb/mongodb-database-collection-and-document/)
4. [https://dev.to/mahmud-r-farhan/a-comprehensive-guide-to-designing-a-mongodb-database-for-intermediate-developers-2p31](https://dev.to/mahmud-r-farhan/a-comprehensive-guide-to-designing-a-mongodb-database-for-intermediate-developers-2p31)
5. [https://www.scaler.com/topics/mongodb/mongodb-basics/](https://www.scaler.com/topics/mongodb/mongodb-basics/)
6. [https://www.geeksforgeeks.org/mongodb/mongodb-interview-questions/](https://www.geeksforgeeks.org/mongodb/mongodb-interview-questions/)
7. [https://www.linkedin.com/pulse/relational-database-vs-mongodb-shaharyar-saleem](https://www.linkedin.com/pulse/relational-database-vs-mongodb-shaharyar-saleem)
8. [https://www.digitalocean.com/community/tutorials/how-to-design-a-document-schema-in-mongodb](https://www.digitalocean.com/community/tutorials/how-to-design-a-document-schema-in-mongodb)
9. [https://www.simplilearn.com/mongodb-interview-questions-and-answers-article](https://www.simplilearn.com/mongodb-interview-questions-and-answers-article)
10. [https://moldstud.com/articles/p-mongodb-vs-relational-databases-key-differences-explained](https://moldstud.com/articles/p-mongodb-vs-relational-databases-key-differences-explained)
11. [https://www.geeksforgeeks.org/mongodb/mongodb-architecture/](https://www.geeksforgeeks.org/mongodb/mongodb-architecture/)
12. [https://github.com/Devinterview-io/mongodb-interview-questions](https://github.com/Devinterview-io/mongodb-interview-questions)
13. [https://www.navisite.com/blog/mongodb-and-relational-database-schema-comparison-dynamic-vs-rigid/](https://www.navisite.com/blog/mongodb-and-relational-database-schema-comparison-dynamic-vs-rigid/)
14. [https://studio3t.com/academy/lessons/mongodb-basics/](https://studio3t.com/academy/lessons/mongodb-basics/)
15. [https://www.hirist.tech/blog/top-35-mongodb-interview-questions-and-answers/](https://www.hirist.tech/blog/top-35-mongodb-interview-questions-and-answers/)
16. [https://www.mongodb.com/resources/compare/relational-vs-non-relational-databases](https://www.mongodb.com/resources/compare/relational-vs-non-relational-databases)
17. [https://www.w3schools.com/mongodb/](https://www.w3schools.com/mongodb/)
18. [https://www.datacamp.com/blog/mongodb-interview-questions](https://www.datacamp.com/blog/mongodb-interview-questions)
19. [https://www.mongodb.com/company/blog/busting-top-myths-about-mongodb-vs-relational-databases](https://www.mongodb.com/company/blog/busting-top-myths-about-mongodb-vs-relational-databases)
20. [https://www.oracle.com/in/database/mongodb/](https://www.oracle.com/in/database/mongodb/)

## Amazon DocumentDB Interview Checklist

- **Core Architecture**
    
    - **MongoDB-Compatible:** JSON/BSON documents, same drivers/tools as MongoDB.[aws.amazon](https://aws.amazon.com/nosql/document/)​
        
    - **Managed Service:** AWS handles provisioning, patching, backups, scaling.
        
    - **Document Model:** Flexible schema, embedding/nesting, no rigid tables.
        
- **Key Advantages**
    
    |Feature|Benefit|
    |---|---|
    |**Flexible Schema**|Rapid iteration, no migrations|
    |**Horizontal Scaling**|Elastic Clusters (millions req/sec)|
    |**High Availability**|99.99% SLA, multi-AZ replication|
    |**Read Replicas**|15 replicas, shared storage|
    
- **Scaling Patterns**
    
    - **Elastic Clusters:** Independent compute/storage scaling.
        
    - **Sharding:** Automatic via shard key (hash/range).
        
    - **Global Clusters:** Multi-region replication, local reads.
        
- **Use Cases**
    
    - **Content Management:** Blogs, CMS (evolving documents).
        
    - **Catalogs:** E-commerce products (varying attributes).
        
    - **IoT/Sensors:** High-velocity semi-structured data.
        
    - **User Profiles:** Nested/hierarchical data.
        
- **Data Operations**
    
    - **CRUD:** Full MongoDB API compatibility.
        
    - **Indexing:** Compound, TTL, geospatial, text search.
        
    - **Aggregation:** Pipeline queries ($match, $group, $sort).
        
- **Production Features**
    
    - **Security:** Encryption at rest, IAM auth, VPC isolation.
        
    - **Backup:** Continuous, point-in-time recovery.
        
    - **Monitoring:** CloudWatch metrics, Performance Insights.
        
- **Tools & Frameworks**
    
    |Category|Tools|
    |---|---|
    |**Drivers**|MongoDB Node.js, PyMongo, Spring Data|
    |**ORMs**|Mongoose, MongoEngine|
    |**GUI**|MongoDB Compass, Studio 3T|
    |**AWS**|DocumentDB Workbench|
    

## 60-Second Recap

- **DocumentDB =** Managed MongoDB-compatible JSON store.
    
- **Strengths:** Schema flexibility, horizontal scaling, 99.99% HA.
    
- **Scaling:** Elastic Clusters + read replicas + global replication.
    
- **Use Cases:** Content, catalogs, IoT; **Avoid:** Complex JOINs/analytics.
    
- **Gold:** MongoDB drivers + AWS managed ops + aggregation pipelines.
    

**Reference**: [AWS Document Database](https://aws.amazon.com/nosql/document/)[aws.amazon](https://aws.amazon.com/nosql/document/)​

1. [https://aws.amazon.com/nosql/document/](https://aws.amazon.com/nosql/document/)
2. 
# **Document Databases Deep Dive**

**Sources:**
1. AWS, "Document Databases" (aws.amazon.com/nosql/document/)
2. MongoDB, "What Is a Document Database?" (mongodb.com/resources/basics/databases/document-databases)

## **1. Essential Question**
*What are document databases, how do their flexible data models and query capabilities differ from other database types, and what are their optimal use cases in modern applications?*

---

## **2. Main Ideas & Key Details**

### **A. Core Definition & Fundamental Model**
*   **Document-Oriented Data Model:**
    *   Stores data as **documents** (self-describing units)
    *   Documents typically in **JSON, BSON, or XML** format
    *   Each document contains **fields and values** (key-value pairs)
    *   Documents can have **nested/sub-documents** (hierarchical structure)

*   **Key Characteristics:**
    *   **Schema Flexibility:** No fixed schema - documents can have different structures
    *   **Self-Contained:** Related data stored together in single document
    *   **Rich Query Language:** Support for complex queries, indexing, aggregations
    *   **Horizontal Scalability:** Designed to scale out across clusters

*   **Basic Document Example (JSON):**
    ```json
    {
      "_id": "user123",
      "name": "John Doe",
      "email": "john@example.com",
      "address": {
        "street": "123 Main St",
        "city": "San Francisco",
        "zip": "94105"
      },
      "orders": [
        {"order_id": "O001", "total": 99.99},
        {"order_id": "O002", "total": 149.99}
      ],
      "preferences": {
        "theme": "dark",
        "notifications": true
      }
    }
    ```

### **B. How Document Databases Work**
*   **Collections & Documents:**
    *   **Documents** grouped into **collections** (similar to tables in RDBMS)
    *   Collections don't enforce schema - documents can vary within same collection
    *   Each document has unique identifier (`_id` field)

*   **Indexing System:**
    *   Can index **any field** in document, including nested fields
    *   **Compound indexes:** Multiple fields together
    *   **Multikey indexes:** For array fields
    *   **Geospatial indexes:** For location-based queries
    *   **Text indexes:** For full-text search

*   **Query Processing:**
    *   **Rich query languages** (MongoDB Query Language, SQL-like queries)
    *   Support for **projections, filtering, sorting, aggregations**
    *   **Embedded documents** queried using dot notation: `address.city`
    *   **Array queries** with operators: `$in`, `$all`, `$elemMatch`

*   **ACID Transactions:**
    *   Modern document databases support **multi-document ACID transactions**
    *   **MongoDB:** Since v4.0 (2018) with snapshot isolation
    *   **Amazon DocumentDB:** ACID-compliant with MongoDB compatibility

### **C. Data Modeling Approaches**
*   **Embedding (Denormalization):**
    *   Store related data within single document
    *   **Advantages:** Single read/write, data locality, atomic updates
    *   **Disadvantages:** Document size limits, data duplication
    *   **Use when:** One-to-few relationships, data read together

*   **Referencing (Normalization):**
    *   Store references (IDs) to related documents
    *   **Advantages:** Avoid duplication, smaller documents, flexible updates
    *   **Disadvantages:** Multiple queries, joins needed, not atomic
    *   **Use when:** One-to-many/many-to-many, large datasets

*   **Hybrid Approach:**
    *   Combine embedding and referencing based on access patterns
    *   **Rule of Thumb:** Embed for performance, reference for flexibility
    *   **Consider:** Read/write patterns, data volatility, growth patterns

*   **Patterns & Anti-Patterns:**
    *   **Bucket Pattern:** Group time-series data into time-based buckets
    *   **Computed Pattern:** Store pre-computed values for performance
    *   **Attribute Pattern:** Handle many similar fields efficiently
    *   **Anti-pattern:** Massive embedded arrays, unbounded document growth

### **D. Document vs. Other Database Types**
*   **vs. Relational Databases:**
    *   **Schema:** Fixed vs. flexible
    *   **Relationships:** Foreign keys/joins vs. embedding/references
    *   **Transactions:** Multi-row vs. multi-document
    *   **Scalability:** Vertical vs. horizontal
    *   **Use case:** Structured vs. semi-structured data

*   **vs. Key-Value Stores:**
    *   **Query Capability:** Limited to key lookups vs. rich queries
    *   **Data Structure:** Simple values vs. structured documents
    *   **Indexing:** Primary key only vs. any field
    *   **Use case:** Simple lookups vs. complex data models

*   **vs. Wide-Column Stores:**
    *   **Data Model:** Documents vs. column families
    *   **Query Pattern:** Flexible queries vs. column-oriented
    *   **Write Pattern:** General purpose vs. write-optimized
    *   **Use case:** General purpose vs. time-series/analytics

### **E. Major Document Database Systems**
*   **MongoDB:**
    *   **Most popular** document database
    *   **BSON format** (Binary JSON)
    *   **Features:** Auto-sharding, replication, aggregation framework
    *   **Ecosystem:** Atlas (cloud), Compass (GUI), Charts (visualization)

*   **Amazon DocumentDB:**
    *   **MongoDB-compatible** managed service
    *   **Features:** Fully managed, scalable, durable
    *   **Architecture:** Distributed storage, compute scaling
    *   **Advantage:** AWS integration, automatic failover

*   **Couchbase:**
    *   **Distributed multi-model** database
    *   **Features:** Memory-first architecture, SQL++ query language
    *   **Use cases:** Mobile, IoT, real-time analytics

*   **CouchDB:**
    *   **Apache project**, uses HTTP/REST API
    *   **Features:** Multi-master replication, incremental MapReduce
    *   **Use cases:** Offline-first applications, content management

*   **Azure Cosmos DB:**
    *   **Multi-model** with document API
    *   **Features:** Globally distributed, SLA-backed latency
    *   **APIs:** MongoDB, Cassandra, Gremlin, SQL, etc.

### **F. Core Features & Capabilities**
*   **Query Languages:**
    *   **MongoDB Query Language (MQL):** JSON-like query syntax
    *   **Aggregation Pipeline:** Multi-stage data processing
    *   **MapReduce:** For batch processing (legacy in MongoDB)
    *   **SQL-like interfaces:** Many support SQL queries

*   **Indexing Types:**
    *   **Single Field:** On any field
    *   **Compound:** Multiple fields together
    *   **Multikey:** Array elements
    *   **Geospatial:** 2d, 2dsphere for location data
    *   **Text:** Full-text search
    *   **Hashed:** For shard key distribution
    *   **Wildcard:** Dynamic field patterns

*   **Aggregation Framework:**
    *   Pipeline of operations: `$match` → `$group` → `$sort` → `$project`
    *   **Stages:** Filter, transform, group, sort, limit, lookup
    *   **Operators:** Arithmetic, array, date, string, conditional
    *   **Performance:** Can use indexes, supports sharding

*   **Change Streams (Real-time):**
    *   Subscribe to database changes
    *   **Use cases:** Real-time analytics, cache invalidation, notifications
    *   **Implementation:** Oplog tailing, triggers

### **G. Scalability & Distribution**
*   **Replication:**
    *   **Replica Sets:** Primary + secondaries for high availability
    *   **Automatic Failover:** Election of new primary if current fails
    *   **Read Preferences:** Route reads to secondaries for scaling
    *   **Write Concern:** Control acknowledgment level

*   **Sharding (Horizontal Scaling):**
    *   **Shard Key:** Determines data distribution
    *   **Chunks:** Data partitions automatically balanced
    *   **Query Router (mongos):** Routes queries to appropriate shards
    *   **Config Servers:** Store cluster metadata

*   **Consistency Models:**
    *   **Strong Consistency:** Read from primary (default for writes)
    *   **Eventual Consistency:** Read from secondaries
    *   **Causal Consistency:** Preserve causal relationships
    *   **Session Consistency:** Within user session

### **H. Optimal Use Cases**
*   **Content Management Systems:**
    *   **Why:** Flexible content schemas, hierarchical data
    *   **Examples:** Blogs, news sites, digital asset management
    *   **Features used:** Rich documents, full-text search, versioning

*   **E-commerce & Product Catalogs:**
    *   **Why:** Varying product attributes, nested specifications
    *   **Examples:** Amazon-like catalogs, inventory management
    *   **Features used:** Flexible schema, complex queries, indexing

*   **User Profiles & Personalization:**
    *   **Why:** Different user attributes, evolving requirements
    *   **Examples:** Social media, streaming services, SaaS platforms
    *   **Features used:** Schema flexibility, partial updates

*   **Real-time Analytics:**
    *   **Why:** Fast aggregations, time-series data
    *   **Examples:** IoT telemetry, application metrics, clickstream
    *   **Features used:** Aggregation framework, indexing, TTL collections

*   **Mobile & Web Applications:**
    *   **Why:** Natural JSON mapping, rapid iteration
    *   **Examples:** Social apps, gaming, productivity tools
    *   **Features used:** Flexible schema, geospatial queries, offline sync

*   **Catalogs & Listings:**
    *   **Why:** Semi-structured data, search requirements
    *   **Examples:** Real estate listings, job boards, restaurant menus
    *   **Features used:** Text search, geospatial queries, faceted search

### **I. When NOT to Use Document Databases**
*   **Complex Transactions Across Documents:**
    *   Multi-document transactions have performance overhead
    *   **Better alternatives:** Relational databases with row-level locking

*   **Highly Normalized Data:**
    *   Many-to-many relationships with frequent updates
    *   **Better alternatives:** Graph or relational databases

*   **Complex Joins Across Collections:**
    *   `$lookup` (join) operations are expensive
    *   **Better alternatives:** Pre-join in application or use relational

*   **Heavy Analytical Workloads:**
    *   Large-scale aggregations across entire dataset
    *   **Better alternatives:** Data warehouses, columnar databases

*   **Strict Schema Enforcement Required:**
    *   Regulatory compliance, data validation needs
    *   **Better alternatives:** Relational with strong constraints

### **J. Best Practices & Performance**
*   **Schema Design Principles:**
    1. **Design for application queries,** not just data structure
    2. **Prefer embedding** for one-to-few, read-together data
    3. **Use references** for many-to-many, large or volatile data
    4. **Avoid unbounded array growth** (consider bucket pattern)
    5. **Consider document size limits** (16MB in MongoDB)

*   **Indexing Strategy:**
    *   **ESR Rule:** Equality → Sort → Range in compound indexes
    *   **Covering Queries:** Include all projected fields in index
    *   **Monitor Index Usage:** Remove unused indexes
    *   **Partial Indexes:** Index subset of documents
    *   **TTL Indexes:** Auto-expire data after time period

*   **Query Optimization:**
    *   **Use projections** to return only needed fields
    *   **Limit results** with `$limit` and `$skip`
    *   **Avoid `$where`** (JavaScript evaluation is slow)
    *   **Use explain()** to analyze query plans
    *   **Monitor slow queries**

*   **Operational Excellence:**
    *   **Monitoring:** Ops Manager, Cloud Manager, AWS CloudWatch
    *   **Backup Strategies:** Point-in-time, continuous, snapshot-based
    *   **Security:** Encryption at rest/in transit, role-based access
    *   **Capacity Planning:** Monitor growth trends, plan scaling

---

## **3. Summary / Synthesis**
Document databases store data as **self-contained documents** (typically JSON/BSON) with **flexible schemas**, making them ideal for **semi-structured and hierarchical data**. They offer **rich query capabilities** through indexing of any field (including nested ones) and support **ACID transactions** in modern implementations. The core modeling decision is between **embedding** (for performance and data locality) and **referencing** (for flexibility and avoiding duplication), often using a hybrid approach based on access patterns. Document databases excel in use cases like **content management, e-commerce catalogs, user profiles, and real-time analytics** where data structures evolve rapidly. However, they are less suitable for **highly normalized data, complex joins, or heavy analytical workloads**. Successful implementation requires **thoughtful schema design, proper indexing, and monitoring** to maintain performance as applications scale.

---

## **4. Key Terms / Vocabulary**
*   **Document:** Self-contained data unit (JSON/BSON/XML) with fields and values
*   **Collection:** Group of documents (similar to table in RDBMS)
*   **Embedding:** Storing related data within single document
*   **Referencing:** Storing IDs to related documents in separate collections
*   **BSON:** Binary JSON (MongoDB's storage format)
*   **Shard Key:** Field used to distribute data across shards
*   **Replica Set:** Group of database servers for high availability
*   **Aggregation Pipeline:** Sequence of data processing operations
*   **Index:** Data structure for efficient querying
*   **Schema-less/Flexible Schema:** No predefined structure requirements

---

## **5. Potential Interview Questions**

### **Fundamental Concepts:**
1. "Explain the difference between embedding and referencing in document databases, with examples of when to use each."
2. "How do document databases handle schema evolution compared to relational databases?"
3. "What are the trade-offs between document databases and key-value stores?"
4. "How do modern document databases provide ACID transactions, and what are the limitations?"
5. "Compare MongoDB's document model with traditional relational normalization."

### **Design Scenarios:**
1. "Design a user profile system for a social media application using a document database."
2. "How would you model an e-commerce product catalog with varying attributes (electronics, clothing, books)?"
3. "Design a content management system for a news website with articles, authors, and comments."
4. "How would you implement a real-time analytics dashboard using document database features?"
5. "Design a multi-tenant SaaS application with document database as the primary store."

### **Performance & Optimization:**
1. "How would you optimize query performance for a document database with millions of documents?"
2. "What indexing strategies would you use for a document database supporting geospatial queries?"
3. "How do you handle 'hot' documents that are accessed very frequently?"
4. "What monitoring would you implement for a production document database cluster?"
5. "How would you design a schema to avoid the 16MB document size limit in MongoDB?"

### **Scalability & Operations:**
1. "Explain how sharding works in MongoDB and how to choose a good shard key."
2. "What are the considerations for global distribution of a document database?"
3. "How would you implement backup and disaster recovery for a document database?"
4. "What strategies would you use for zero-downtime schema migrations?"
5. "How do you handle data consistency in a geographically distributed document database?"

### **Advanced Topics:**
1. "How would you implement full-text search capabilities in a document database?"
2. "What patterns would you use for time-series data in a document database?"
3. "How do you handle many-to-many relationships efficiently in document databases?"
4. "What are the security considerations specific to document databases?"
5. "How would you migrate from a relational database to a document database?"

---

## **6. How to Use These Notes & Key Takeaways**

### **Interview Preparation Strategy:**
1. **Master Data Modeling** - Know when to embed vs. reference with concrete examples
2. **Understand Query Capabilities** - Be familiar with aggregation pipeline and indexing
3. **Know Scalability Patterns** - Understand sharding, replication, and consistency trade-offs
4. **Prepare Real Examples** - Have specific document database implementations you've designed
5. **Know Limitations** - Understand when NOT to use document databases

### **Key Takeaways for Interviews:**
1. **Flexibility is Key Advantage** - Schema evolution without migrations
2. **Data Model Drives Performance** - Embedding vs. referencing critical decisions
3. **Rich Query Capabilities** - Beyond simple key lookups, but with trade-offs
4. **Modern Features** - ACID transactions, change streams, aggregation framework
5. **Scalability Built-in** - Horizontal scaling through sharding

### **During the Interview:**
1. **Start with Requirements Analysis:**
   - "What is the data structure and how might it evolve?"
   - "What are the primary query patterns?"
   - "What are the consistency and availability requirements?"
   - "What is the expected data volume and growth?"
2. **Evaluate Document DB Suitability:**
   - Semi-structured, hierarchical data → Good fit
   - Complex joins across entities → Poor fit
   - Rapid iteration, changing requirements → Good fit
   - Strict schema enforcement → Poor fit
3. **Design Data Model:**
   - Decide embedding vs. referencing for each relationship
   - Design document structure based on query patterns
   - Plan indexes for common queries
   - Consider document size limits and growth
4. **Address Operational Concerns:**
   - Sharding strategy and shard key selection
   - Replication and failover configuration
   - Monitoring and alerting strategy
   - Backup and recovery procedures

### **Common Pitfalls to Avoid:**
- ❌ "Always embed everything" (leads to massive documents)
- ❌ "Ignore indexing" (performance will suffer)
- ❌ "No schema design" (flexible ≠ no design needed)
- ❌ "Forget about transactions" (multi-document updates need coordination)
- ❌ "No monitoring" - can't optimize what you can't measure

### **Signs of Strong Candidates:**
- ✅ Asks about data access patterns and evolution first
- ✅ Discusses embedding vs. referencing trade-offs specifically
- ✅ Considers document size limits and growth patterns
- ✅ Mentions indexing strategy and query optimization
- ✅ Understands when document databases are NOT appropriate

---

## **7. Quick Review Cheat Sheet**

### **Embedding vs. Referencing Decision Matrix:**
| **Factor** | **Favors Embedding** | **Favors Referencing** |
|------------|---------------------|----------------------|
| **Relationship Cardinality** | One-to-few | One-to-many/many-to-many |
| **Data Volatility** | Low | High |
| **Data Size** | Small | Large |
| **Query Pattern** | Read together | Read separately |
| **Update Frequency** | Infrequent | Frequent |

### **Common Document Patterns:**
1. **Subset Pattern:** Store most-used fields in main document, full data elsewhere
2. **Extended Reference:** Embed key fields from related document for performance
3. **Bucket Pattern:** Group time-series data into time-based buckets
4. **Computed Pattern:** Store pre-aggregated values for performance
5. **Schema Versioning:** Include version field for schema evolution

### **Indexing Best Practices:**
- **ESR Rule:** Equality fields first, then Sort fields, then Range fields
- **Covering Indexes:** Include all fields needed by query
- **Partial Indexes:** For queries on subset of documents
- **TTL Indexes:** Auto-expire old data
- **Monitor:** Index size, usage stats, storage impact

### **Query Optimization Checklist:**
1. [ ] Use projections to return only needed fields
2. [ ] Create indexes for common query patterns
3. [ ] Use `$limit` to reduce result size
4. [ ] Avoid `$where` and JavaScript evaluation
5. [ ] Use aggregation pipeline for complex operations
6. [ ] Monitor and analyze slow queries
7. [ ] Use explain() to understand query plans

### **Shard Key Selection Criteria:**
1. **High Cardinality** (many distinct values)
2. **Even Distribution** of writes and reads
3. **Matches Query Patterns** (most queries include shard key)
4. **Avoids Hotspots** (monotonic keys cause problems)
5. **Supports Growth** (won't cause imbalance over time)

### **When to Choose Specific Systems:**
- **MongoDB:** General purpose, rich ecosystem, self-managed or Atlas
- **Amazon DocumentDB:** AWS-native, MongoDB compatible, fully managed
- **Couchbase:** Memory-first, SQL++ queries, mobile sync
- **Azure Cosmos DB:** Global distribution, multi-model, SLA-backed

### **Monitoring Essentials:**
1. **Performance:** Query latency, throughput, index usage
2. **Resource:** CPU, memory, disk I/O, connection count
3. **Replication:** Lag between nodes, election events
4. **Sharding:** Chunk distribution, balancer activity
5. **Business:** Query patterns, user impact, growth trends

This comprehensive guide synthesizes insights from AWS and MongoDB documentation, providing deep knowledge of document databases for system design interviews.