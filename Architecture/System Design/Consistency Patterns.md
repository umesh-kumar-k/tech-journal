### **Consistency Patterns in Distributed Systems**

**Source:** Design Gurus Blog | [Consistency Patterns in Distributed Systems](https://www.designgurus.io/blog/consistency-patterns-distributed-systems)

**Main Idea:** In distributed systems, maintaining data consistency across replicas involves fundamental trade-offs between **strong consistency** (simpler but less available) and **eventual consistency** (more available but complex). Understanding different consistency patterns—**Strong, Eventual, Causal, and Read-your-Writes**—is essential for designing systems that meet specific correctness and availability requirements.

---

#### **Notes: Core Consistency Models & Patterns**

**1. The Consistency Spectrum**
*   **Strongest → Weakest:** Linearizability > Sequential > Causal > Eventual
*   **Trade-off:** Stronger consistency = **lower availability, higher latency**. Weaker consistency = **higher availability, lower latency**.
*   **Impact:** Choice affects **system design complexity, user experience, and failure handling**.

**2. Strong Consistency (Linearizability)**
*   **Definition:** Every read receives the **most recent write** across all nodes. Gives the **illusion of a single copy**.
*   **How it Works:** Uses **synchronous replication** - write must be acknowledged by all (or a quorum of) replicas before returning success.
*   **Use Cases:** Banking systems (account balances), inventory management (prevent overselling), leader election.
*   **Drawbacks:** High latency (waits for all replicas), lower availability (if replicas fail, writes may block).
*   **Examples:** ZooKeeper, etcd, Google Spanner, traditional RDBMS with synchronous replication.

**3. Eventual Consistency**
*   **Definition:** If no new updates are made, **eventually all reads will return the same value**. Updates propagate asynchronously.
*   **How it Works:** **Asynchronous replication** - write returns success after primary, replicas sync later. May serve **stale reads**.
*   **Use Cases:** Social media feeds, DNS, CDNs, product catalogs, user profiles.
*   **Drawbacks:** **Stale reads**, requires conflict resolution, harder to reason about.
*   **Conflict Resolution Strategies:**
    *   **Last Write Wins (LWW):** Simple but can lose data.
    *   **Vector Clocks:** Track causal relationships.
    *   **CRDTs (Conflict-Free Replicated Data Types):** Mathematical guarantee of convergence.

**4. Causal Consistency**
*   **Definition:** Preserves **causal relationships** between operations. If operation A **causally affects** operation B, then all nodes must see A before B.
*   **Key Insight:** Weaker than sequential consistency but stronger than eventual consistency.
*   **How it Works:** Uses **vector clocks** or **version vectors** to track causality.
*   **Example:** Social media comment threads (replies must appear after the original post).
*   **Use Cases:** Collaborative editing (Google Docs), chat applications, comment systems.

**5. Read-Your-Writes Consistency**
*   **Definition:** After updating data, a user **always sees their own updates** in subsequent reads.
*   **Subset of Causal Consistency:** Specifically guarantees a user sees their own writes.
*   **Implementation:** Route user's requests to same replica (sticky sessions) or track write timestamps.
*   **Use Cases:** User profile updates, shopping cart, any user-specific data.

**6. Implementation Patterns & Techniques**
*   **Quorum-Based Consensus:**
    *   **Read Quorum (R) + Write Quorum (W) > N** ensures strong consistency.
    *   Allows trading off read/write performance while maintaining consistency.
*   **Leader-Follower (Primary-Secondary):**
    *   **Synchronous:** Strong consistency (CP).
    *   **Asynchronous:** Eventual consistency (AP).
*   **Multi-Leader & Leaderless:**
    *   Multiple nodes accept writes (Dynamo, Cassandra).
    *   Requires conflict resolution (LWW, CRDTs).
*   **Conflict Resolution Methods:**
    *   **Client-side resolution:** Application logic decides.
    *   **Server-side resolution:** Automated (LWW, CRDTs).
    *   **Operational Transformation:** For collaborative editing.

**7. Choosing the Right Consistency Model**
*   **Ask:** "What is the **cost of inconsistency**?" vs. "What is the **cost of unavailability**?"
*   **Strong Consistency When:**
    *   Data must be absolutely correct (financial transactions).
    *   Order matters (auction bids).
    *   System can tolerate higher latency/lower availability.
*   **Eventual Consistency When:**
    *   Availability is critical (global scale).
    * **Temporary staleness is acceptable** (social media).
    *   System can handle conflict resolution complexity.

---

#### **Summary / Key Takeaways for Interview:**

*   **Consistency is a Spectrum, Not Binary:** Systems implement varying **strengths of consistency** based on use case requirements.
*   **CAP Theorem Context:** **Strong consistency = CP** (available during partitions). **Eventual consistency = AP** (consistent after partitions heal).
*   **Strong Consistency Simpler to Reason About, Harder to Scale:** Easier programming model but sacrifices availability and performance.
*   **Eventual Consistency Scales Better, Harder to Program:** Enables global scale and high availability but requires handling stale reads and conflicts.
*   **Hybrid Approaches are Common:** Use **strong consistency for critical data** (account balances) and **eventual consistency for non-critical data** (product reviews). Many systems offer **tunable consistency**.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"Explain the difference between strong and eventual consistency."** → **Strong consistency:** Every read gets the **latest write** (synchronous replication, higher latency). **Eventual consistency:** Reads may get **stale data** temporarily; replicas converge eventually (asynchronous replication, lower latency). Strong is like reading from a single book everyone shares; eventual is like everyone having their own copy that syncs occasionally.
*   **"When would you use causal consistency vs. eventual consistency?"** → Use **causal consistency** when you need to **preserve causality** (e.g., chat messages, comments, collaborative editing). Use **eventual consistency** when causality isn't important and you just need **eventual convergence** (e.g., social media likes, CDN content, user profile pictures).
*   **"How does quorum consensus ensure strong consistency?"** → With **N replicas**, require **W replicas to acknowledge writes** and **R replicas to respond to reads**. If **W + R > N**, then the read and write sets **must overlap** by at least one replica that has the latest write, guaranteeing strong consistency. Example: N=3, W=2, R=2 ensures overlap.
*   **"What is a CRDT and how does it help with eventual consistency?"** → A **Conflict-free Replicated Data Type** is a data structure (counter, set, register) that can be **updated independently on different replicas** and will **always converge to the same state** when merged, without requiring coordination. It eliminates the need for complex conflict resolution in AP systems.
*   **"How would you design a shopping cart with consistency requirements?"** → Use **read-your-writes consistency** for the cart owner (they always see their updates). Use **eventual consistency** across replicas for availability. Critical operations like **checkout** switch to **strong consistency** (lock inventory, process payment) to prevent overselling. This is a **hybrid consistency model** based on operation criticality.

### **Consistency Patterns in System Design**

**Source:** System Design One | [Consistency Patterns](https://systemdesign.one/consistency-patterns/)

**Main Idea:** Consistency in distributed systems exists on a **spectrum** with different patterns serving different use cases. Understanding **Strong, Weak, Eventual, Causal, and Read-Your-Writes** consistency models—and their trade-offs between **correctness, availability, and performance**—is fundamental to designing scalable systems.

---

#### **Notes: Consistency Models & Their Characteristics**

**1. Strong Consistency (Linearizability)**
*   **Definition:** After a write completes, **all subsequent reads** (by any client from any node) return that value or a newer one. Gives the **illusion of a single up-to-date copy**.
*   **How it Works:**
    *   **Synchronous replication** before acknowledging write.
    *   Uses **distributed locks** or **consensus protocols** (Paxos, Raft).
    *   Often employs **leader-based architecture** with primary node.
*   **Pros:** Simple programming model, predictable behavior.
*   **Cons:** **High latency** (waits for all replicas), **lower availability** (failing nodes block writes), **poor scalability**.
*   **Use Cases:** Financial systems, inventory management, leader election.
*   **Examples:** ZooKeeper, etcd, Google Spanner (with TrueTime), traditional RDBMS.

**2. Weak Consistency**
*   **Definition:** No guarantee on when reads will see writes. The system **makes no consistency promises**.
*   **How it Works:** Replication is **completely asynchronous** with no coordination.
*   **Pros:** **Lowest latency**, highest availability, excellent scalability.
*   **Cons:** **Unpredictable stale reads**, difficult to reason about.
*   **Use Cases:** DNS (propagation delays), CDNs, real-time analytics where freshness isn't critical.
*   **Note:** Rarely used as primary model due to unpredictability.

**3. Eventual Consistency**
*   **Definition:** If no new updates are made, **eventually all replicas converge** to the same value. **Most popular weak consistency model**.
*   **How it Works:**
    *   **Asynchronous replication** with background sync.
    *   **Conflict resolution** needed (LWW, vector clocks, CRDTs).
*   **Pros:** **High availability**, good scalability, tolerant to network partitions.
*   **Cons:** **Stale reads possible**, requires conflict resolution logic.
*   **Variants:**
    *   **Monotonic Reads:** Once a client reads a value, they never see an older value.
    *   **Monotonic Writes:** Writes from a client are executed in order.
*   **Use Cases:** Social media, user profiles, product catalogs, DNS.
*   **Examples:** Amazon DynamoDB (default), Apache Cassandra, Riak.

**4. Causal Consistency**
*   **Definition:** Preserves **causal relationships** between operations. If operation A **"happens before"** operation B (causally related), then all nodes see A before B.
*   **How it Works:** Uses **vector clocks** or **Lamport timestamps** to track causality.
*   **Pros:** Stronger than eventual, preserves important ordering.
*   **Cons:** Overhead of maintaining causality metadata.
*   **Use Cases:** Social media feeds, chat applications, collaborative editing.
*   **Example:** Google's Spanner offers external consistency (stronger than causal).

**5. Read-Your-Writes Consistency**
*   **Definition:** After a client updates data, their **subsequent reads always reflect that update**.
*   **How it Works:** **Session stickiness** (route to same replica) or **track version numbers**.
*   **Pros:** Better user experience for single user.
*   **Cons:** Doesn't guarantee consistency across different users.
*   **Use Cases:** User profiles, shopping carts, any single-user data.
*   **Note:** A **subset of causal consistency**.

**6. Implementation Patterns & Techniques**
*   **Quorum-Based Replication:**
    *   **Write Quorum (W) + Read Quorum (R) > Replica Count (N)** ensures strong consistency.
    *   Allows tuning of read/write performance trade-off.
*   **Conflict-Free Replicated Data Types (CRDTs):**
    *   Data structures that **always converge** when merged.
    *   Enable **strong eventual consistency** (no conflicts).
    *   Types: **G-Counter** (increment-only), **PN-Counter** (inc/dec), **G-Set** (add-only set).
*   **Version Vectors & Vector Clocks:**
    *   Track **causality and concurrency** of updates.
    *   Enable conflict detection and intelligent resolution.

**7. Choosing the Right Consistency Model**
*   **Key Questions:**
    1.  **What's the cost of inconsistency?** (Financial loss vs. minor UX issue)
    2.  **What's the cost of unavailability?** (Lost revenue vs. user frustration)
    3.  **What are the read/write patterns?** (Read-heavy vs. write-heavy)
*   **Decision Framework:**
    *   **Critical data** (bank balances, inventory) → **Strong consistency**
    *   **User-specific data** (profiles, carts) → **Read-your-writes**
    *   **Social/content data** (feeds, comments) → **Causal or Eventual**
    *   **Global scale, high availability** → **Eventual consistency**
*   **Hybrid Approach:** Many systems use **multiple consistency models** for different operations/data types.

---

#### **Summary / Key Takeaways for Interview:**

*   **Consistency is a Spectrum:** From **Strong** (correct but slow) to **Eventual** (fast but possibly stale), with **Causal** and **Read-Your-Writes** as useful middle grounds.
*   **Trade-offs are Fundamental:** You cannot optimize for **consistency, availability, and latency** simultaneously (PACELC theorem). Each consistency model makes explicit trade-offs.
*   **Match Model to Use Case:** The "right" consistency depends entirely on **business requirements**, not technical preference. Ask: "What can the business tolerate?"
*   **Modern Systems are Sophisticated:** They use **tunable consistency** (DynamoDB's consistency levels), **CRDTs** for guaranteed convergence, and **hybrid models** to get the best of multiple worlds.
*   **Implementation Matters:** Consistency is achieved through specific patterns: **quorums, leader election, vector clocks, CRDTs**. Understanding these patterns is as important as knowing the models.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"Explain the difference between strong and eventual consistency with examples."** → **Strong:** Like a **library book**—only one person can have it at a time, everyone sees the same state. **Eventual:** Like **Wikipedia**—multiple people can edit, changes propagate, you might see stale versions temporarily. Strong for banking, eventual for social media.
*   **"What are CRDTs and why are they useful?"** → **Conflict-free Replicated Data Types** are **mathematical data structures** that guarantee convergence when independently updated and merged. They enable **strong eventual consistency** without complex conflict resolution. Example: **G-Counter** for likes count across replicas.
*   **"How does quorum replication work to ensure consistency?"** → With **N replicas**, require **W nodes to acknowledge writes** and **R nodes to respond to reads**. If **W + R > N**, the read and write sets **must overlap** by at least one node with the latest data, guaranteeing strong consistency. Tunable via W and R values.
*   **"When would you choose causal consistency over eventual consistency?"** → When you need to **preserve ordering of related events**. Example: **Social media comments**—replies must appear after the original post (causal). For **post likes count**, ordering doesn't matter (eventual). Causal adds tracking overhead but preserves important relationships.
*   **"Design a system that needs both strong and eventual consistency."** → Use a **hybrid approach**: 1) **Strong consistency** for critical operations (payment processing, inventory deduction) via a CP database. 2) **Eventual consistency** for non-critical data (product recommendations, user activity feeds) via an AP database. 3) **Bridge them** with asynchronous events (e.g., after payment confirms, emit event to update AP read models).

