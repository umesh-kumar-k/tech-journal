
## Data Modeling Interview Checklist

- **Database Selection Criteria**
    
    |DB Type|Schema|Queries|Best For|
    |---|---|---|---|
    |**SQL**|Rigid|JOINs, complex|Relationships, transactions|
    |**Document**|Flexible|Field queries|Nested data, schema evolution|
    |**Key-Value**|None|Key lookup|Caching, sessions|
    |**Wide-Column**|Query-driven|Partition scans|Time series, high writes|
    |**Graph**|Nodes/edges|Traversals|Social networks|
    
- **Schema Design Process**
    
    1. **Requirements:** Data volume, access patterns, consistency needs.
        
    2. **Entities & Keys:** Primary keys (system-generated), foreign keys.
        
    3. **Relationships:** 1:N (user→posts), N:M (likes table).
        
    4. **Indexes:** Match query patterns (user_id + created_at).
        
    5. **Normalization vs Denormalization:** Trade storage for read speed.
        
- **Normalization vs Denormalization**
    
    |Aspect|Normalized (SQL)|Denormalized (NoSQL)|
    |---|---|---|
    |**Storage**|Efficient|Duplicated|
    |**Writes**|Fast|Complex (multi-updates)|
    |**Reads**|JOIN-heavy|Single document|
    |**Consistency**|Strong|Eventual|
    
- **Access Pattern Examples**
    
    text
    
    `// Feed: recent posts by followed users INDEX posts (user_id, created_at DESC) // User timeline posts_by_user[user_id] → list of post_ids // Analytics DENORMALIZE like_count into posts table`
    
- **Scaling Considerations**
    
    - **Shard Key:** Match primary access pattern (user_id for user feeds).
        
    - **Avoid Cross-Shard:** Shard timeline by time ranges if global recent posts.
        
    - **Constraints:** Drop FKs at scale for write perf.
        
- **Tools & Frameworks**
    
    |Tool|Purpose|
    |---|---|
    |**dbdiagram.io**|Visual ER diagrams|
    |**Prisma**|ORM schema migrations|
    |**Spring Data**|Repository patterns|
    |**Mongoose**|MongoDB schemas|
    

## 60-Second Recap

- **Match DB to Workload:** SQL (relationships), Document (nested), KV (cache).
    
- **Query-Driven:** Indexes + shard keys match access patterns.
    
- **Denormalize:** Duplicate data for read perf; accept write complexity.
    
- **Scale:** Shard by primary pattern; avoid cross-shard queries.
    
- **Gold:** Access patterns → schema → indexes → denormalize → shard key.
    

**Reference**: [Hello Interview Data Modeling](https://www.hellointerview.com/learn/system-design/core-concepts/data-modeling)[hellointerview](https://www.hellointerview.com/learn/system-design/core-concepts/data-modeling)​

1. [https://www.hellointerview.com/learn/system-design/core-concepts/data-modeling](https://www.hellointerview.com/learn/system-design/core-concepts/data-modeling)
***

### **Data Modeling**

**Source:** Hello Interview - Core Concepts: Data Modeling
**Link:** https://www.hellointerview.com/learn/system-design/core-concepts/data-modeling

---

#### **Key Questions / Concepts (Cue Column)**
*   **What is Data Modeling?**
*   **Relational (SQL) Data Model:** Structure & Use Cases
*   **Document (NoSQL) Data Model:** Structure & Use Cases
*   **Wide-Column (NoSQL) Data Model:** Structure & Use Cases
*   **Graph (NoSQL) Data Model:** Structure & Use Cases
*   **Critical Decision Factors:** ACID, Scale, Relationships, Flexibility
*   **Hybrid/Polyglot Persistence Approach**

---

#### **Notes (Note-Taking Column)**

**1. Definition & Purpose:**
*   The process of defining how data is stored, organized, and related.
*   Foundation for system design; dictates how applications access and manipulate data.
*   Choice impacts scalability, performance, and complexity.

**2. Primary Data Models:**

| Model | Structure | Ideal For | Examples |
| :--- | :--- | :--- | :--- |
| **Relational (SQL)** | Tables with rows/cols, strict schema, foreign keys. | Complex queries, transactions (ACID), strong consistency, structured data with clear relationships. | Financial systems, ERP, CRM. |
| **Document (NoSQL)** | Collections of JSON-like documents (flexible schema). | Hierarchical data, rapid iteration, scalability via sharding, read-heavy workloads. | User profiles, catalogs, content mgmt. |
| **Wide-Column (NoSQL)** | Tables, rows, column families. Rows can have different columns. | Massive scale (PB), high write throughput, simple queries on primary key. | Time-series, IoT, analytics. |
| **Graph (NoSQL)** | Nodes (entities), edges (relationships), properties. | Deeply connected data, relationship-heavy queries (e.g., friends of friends). | Social networks, fraud detection, recommendation engines. |

**3. Decision Factors (The "It Depends"):**
*   **Consistency Needs:** Require **ACID** transactions? -> Lean **SQL**.
*   **Scale & Traffic:** Anticipate **massive, unpredictable scale** or simple queries? -> Consider **NoSQL (Doc/Wide-Column)**.
*   **Data Relationships:** Many complex joins? -> **SQL**. Deep relational traversals? -> **Graph**. Simple or hierarchical? -> **Document**.
*   **Flexibility & Evolution:** Schema is volatile? -> **Document**.
*   **Team Expertise:** Operational familiarity matters.

**4. Key Insight for Architects:**
*   **Polyglot Persistence:** Modern systems often use *multiple* data models together (e.g., **SQL** for transactions, **Document** for user profiles, **Graph** for recommendations). Justify your choices based on specific subsystem requirements.

---

#### **Interview Checklist & 60-Second Recap (Summary)**

**60-Second Recap:**
Data modeling is choosing how to structure and store data. The four core models are: 1) **Relational (SQL)** for ACID transactions and complex joins, 2) **Document (NoSQL)** for flexible, hierarchical data at scale, 3) **Wide-Column (NoSQL)** for massive, write-heavy workloads, and 4) **Graph (NoSQL)** for deeply connected relationships. As an architect, don't force one model for all. Prioritize requirements—consistency, scale, relationship complexity, and flexibility—and advocate for **polyglot persistence**, using the right tool for each job within a single system.

**Senior Architect Interview Checklist:**
✅ **Articulate the Trade-offs:** Be ready to contrast SQL vs. NoSQL beyond "consistency vs. scale." Discuss join complexity, schema migration, and operational overhead.
✅ **Justify Polyglot Persistence:** Frame your design with multiple data stores as a strategic choice, not complexity for its own sake. Explain how data flows between them.
✅ **Discuss Real-World Hybrids:** Example: "We'd use PostgreSQL for user transactions, MongoDB for user-generated content, and Cassandra for activity logs."
✅ **Consider the "How":** Mention patterns like CQRS or eventual consistency to sync data between different models if needed.
✅ **Ask Clarifying Questions:** Before choosing a model, explicitly state you'd clarify requirements around data volume, query patterns, consistency tolerance, and growth rate.

**Reference Link:** [Hello Interview - Data Modeling](https://www.hellointerview.com/learn/system-design/core-concepts/data-modeling)