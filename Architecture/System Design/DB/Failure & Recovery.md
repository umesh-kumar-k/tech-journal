***

## Database Failure & Recovery Interview Checklist

- **Failure Classification**
    
    |Type|Causes|Recovery Strategy|
    |---|---|---|
    |**Transaction**|Logic errors, constraints, deadlocks|Rollback using logs|
    |**System Crash**|Software bugs, power loss, hardware|ARIES (Analysis/Redo/Undo)|
    |**Disk/Media**|Head crash, corruption|Backups + log replay|
    |**Network**|Partitions, timeouts|2PC abort, retries|
    
- **Recovery Mechanisms**
    
    - **Logging:** Before/after images, WAL (Write-Ahead Logging).
        
    - **Checkpoints:** Periodic DB snapshots → faster recovery from last checkpoint.
        
    - **Rollback:** Undo active txns using log before-images.
        
    - **Commit:** Mark txn complete; ensure durability.
        
- **ARIES Algorithm (IBM Standard)**
    
    1. **Analysis:** Scan log → dirty pages + active txns.
        
    2. **Redo:** Replay committed txns (idempotent).
        
    3. **Undo:** Rollback losers in reverse completion order.
        
- **Recovery Techniques**
    
    |Technique|Write Behavior|Pros|Cons|
    |---|---|---|---|
    |**Immediate Update**|Write DB immediately|Real-time visibility|Complex undo/redo logs|
    |**Deferred Update**|Log only, apply on commit|Simple redo-only|Delayed visibility|
    |**Shadow Paging**|Copy-on-write pages|Clean recovery|High storage overhead|
    
- **ACID Enforcement**
    
    - **Atomicity:** Rollback ensures all-or-nothing.
        
    - **Consistency:** Constraints validated pre-commit.
        
    - **Isolation:** Locking prevents interference.
        
    - **Durability:** WAL + non-volatile storage.
        
- **Tools & Modern Recovery**
    
    |Database|Recovery Features|
    |---|---|
    |**PostgreSQL**|WAL, streaming replication|
    |**MySQL InnoDB**|Redo/undo logs|
    |**SQLite**|Rollback journal, WAL mode|
    |**CockroachDB**|Distributed Raft consensus|
    

## 60-Second Recap

- **Failures:** Txn (rollback), crash (ARIES), disk (backups+logs).
    
- **ARIES:** Analysis (dirty pages) → Redo (committed) → Undo (active).
    
- **Techniques:** Immediate (real-time), Deferred (batch), Shadow (copy-on-write).
    
- **ACID:** Logging/checkpoints ensure properties post-recovery.
    
- **Gold:** WAL everywhere + automated failover + regular backups.
    

**Reference**: [Database Recovery DBMS](http://artificium.us/lessons/60.dbdesign/l-60-703-db-recovery/l-60-703.html)[hellointerview](https://www.hellointerview.com/learn/system-design/core-concepts/data-modeling)​

1. [https://www.hellointerview.com/learn/system-design/core-concepts/data-modeling](https://www.hellointerview.com/learn/system-design/core-concepts/data-modeling)
## Database Failure & Recovery Interview Checklist

- **Failure Types**
    
    |Type|Cause|Impact|
    |---|---|---|
    |**Transaction Failure**|Logic error, constraint violation|Partial txn rollback|
    |**System Crash**|Power loss, OS crash|Buffer loss, log replay|
    |**Disk Failure**|Hardware crash|Data corruption/loss|
    |**Network Partition**|Connectivity loss|Distributed txn issues|
    
- **Recovery Techniques**
    
    |Method|Mechanism|Use Case|
    |---|---|---|
    |**Log-Based (ARIES)**|Analysis/Redo/Undo phases|System crashes|
    |**Checkpointing**|Periodic consistent snapshots|Faster recovery|
    |**Shadow Paging**|Duplicate pages for writes|No undo needed|
    |**Backup + Logs**|Full/differential + txn logs|Disaster recovery|
    
- **ARIES Recovery Algorithm**
    
    1. **Analysis:** Scan log from last checkpoint → identify dirty pages/active txns.
        
    2. **Redo:** Replay committed txns from crash point (idempotent).
        
    3. **Undo:** Rollback loser txns using before-images.
        
- **Distributed Recovery**
    
    |Pattern|Description|Tools|
    |---|---|---|
    |**2PC**|Coordinator + participants vote|XA transactions|
    |**Paxos/Raft**|Leader election + log replication|CockroachDB, etcd|
    |**Saga**|Compensating txns for eventual consistency|Temporal, Axon|
    
- **RTO/RPO Metrics**
    
    - **RTO (Recovery Time Objective):** Max acceptable downtime.
        
    - **RPO (Recovery Point Objective):** Max acceptable data loss.
        
- **Prevention & Tools**
    
    |Strategy|Tools/Frameworks|
    |---|---|
    |**Replication**|PostgreSQL Streaming, MySQL Group Rep|
    |**Backups**|pg_dump, mysqldump, Velero (K8s)|
    |**Monitoring**|Prometheus + Alertmanager|
    |**HA Clusters**|Patroni (PostgreSQL), Galera (MySQL)|
    

## 60-Second Recap

- **Failures:** Txn (rollback), System (ARIES log replay), Disk (backups).
    
- **ARIES:** Analysis (dirty pages) → Redo (committed) → Undo (losers).
    
- **Distributed:** 2PC (atomic), Saga (compensating), Raft (leader election).
    
- **Metrics:** RTO (downtime), RPO (data loss).
    
- **Gold:** WAL logging + streaming replication + automated failover.
    

**Reference**: [Database Failure Recovery](https://www.sciencedirect.com/topics/computer-science/database-failure)[geeksforgeeks](https://www.geeksforgeeks.org/dbms/database-recovery-techniques-in-dbms/)​

1. [https://www.geeksforgeeks.org/dbms/database-recovery-techniques-in-dbms/](https://www.geeksforgeeks.org/dbms/database-recovery-techniques-in-dbms/)
2. [https://aerospike.com/blog/understanding-database-disaster-recovery/](https://aerospike.com/blog/understanding-database-disaster-recovery/)
3. [https://takeuforward.org/dbms/database-recovery-management](https://takeuforward.org/dbms/database-recovery-management)
4. [http://artificium.us/lessons/60.dbdesign/l-60-703-db-recovery/l-60-703.html](http://artificium.us/lessons/60.dbdesign/l-60-703-db-recovery/l-60-703.html)
5. [https://epublications.regis.edu/cgi/viewcontent.cgi?article=1055&context=theses](https://epublications.regis.edu/cgi/viewcontent.cgi?article=1055&context=theses)
6. [https://www.cs.purdue.edu/homes/bb/cs448_Fall2017/lecture-files/pdf/ch19-Database%20Recovery%20Techniques.pdf](https://www.cs.purdue.edu/homes/bb/cs448_Fall2017/lecture-files/pdf/ch19-Database%20Recovery%20Techniques.pdf)
7. [https://www.geeksforgeeks.org/operating-systems/recovery-in-distributed-systems/](https://www.geeksforgeeks.org/operating-systems/recovery-in-distributed-systems/)
8. [https://www.slideshare.net/slideshow/database-failure-and-recovery-1/251063796](https://www.slideshare.net/slideshow/database-failure-and-recovery-1/251063796)
9. [https://www.geeksforgeeks.org/dbms/failure-classification-in-dbms/](https://www.geeksforgeeks.org/dbms/failure-classification-in-dbms/)
10. [https://www.cs.rochester.edu/u/sandhya/csc258/lectures/fault_tolerance_recovery.pdf](https://www.cs.rochester.edu/u/sandhya/csc258/lectures/fault_tolerance_recovery.pdf)
11. [https://www.webdevstory.com/database-recovery-techniques/](https://www.webdevstory.com/database-recovery-techniques/)
12. [https://www.billbrown.info/post/database-failures-types-and-solutions/](https://www.billbrown.info/post/database-failures-types-and-solutions/)
13. [https://www.statsig.com/perspectives/handling-failures-in-distributed-systems-patterns-and-anti-patterns](https://www.statsig.com/perspectives/handling-failures-in-distributed-systems-patterns-and-anti-patterns)
14. [https://www.geeksforgeeks.org/system-design/failover-patterns-system-design/](https://www.geeksforgeeks.org/system-design/failover-patterns-system-design/)
15. [https://www.cs.purdue.edu/homes/bb/cs448_Spring2014/lecture-files/pdf/ch19-Database%20Recovery%20Techniques.pdf](https://www.cs.purdue.edu/homes/bb/cs448_Spring2014/lecture-files/pdf/ch19-Database%20Recovery%20Techniques.pdf)
16. [http://elearn.psgcas.ac.in/nptel/courses/video/106104182/lec15.pdf](http://elearn.psgcas.ac.in/nptel/courses/video/106104182/lec15.pdf)
17. [https://www.ibm.com/docs/en/db2/11.5.x?topic=recovery-developing-backup-strategy](https://www.ibm.com/docs/en/db2/11.5.x?topic=recovery-developing-backup-strategy)
18. [https://www.sedatasolutions.io/types-of-database-failures/](https://www.sedatasolutions.io/types-of-database-failures/)
19. [https://temporal.io/blog/error-handling-in-distributed-systems](https://temporal.io/blog/error-handling-in-distributed-systems)
20. [https://homepages.cwi.nl/~manegold/teaching/DBtech/slides/ch17-4.pdf](https://homepages.cwi.nl/~manegold/teaching/DBtech/slides/ch17-4.pdf)
### **Summary: Database Failure & Recovery**

**Sources:**
1. ScienceDirect: "Database Failure"
2. Artificium: "Database Recovery System"
**Links:**
- https://www.sciencedirect.com/topics/computer-science/database-failure
- http://artificium.us/lessons/60.dbdesign/l-60-703-db-recovery/l-60-703.html

---

#### **Key Questions / Concepts (Cue Column)**
*   **Types of Database Failures** (Classification)
*   **Transaction States & Logging Fundamentals**
*   **Recovery Components:** Log, Checkpoint, Buffer
*   **Recovery Techniques:** Deferred vs Immediate Update
*   **Advanced: ARIES Algorithm Principles**
*   **Architect's Recovery Strategy**

---

#### **Notes (Note-Taking Column)**

**1. Types of Database Failures:**
*   **Transaction Failures:**
    *   **Logical Errors:** Transaction cannot complete due to constraint violation, deadlock, or application error.
    *   **System Errors:** Transaction terminated due to internal state (e.g., overflow, resource limit).
*   **System Crashes:**
    *   Hardware/software failure causing loss of **volatile main memory**, but **non-volatile storage (disk)** remains intact.
    *   Examples: OS crash, power failure.
*   **Media Failures (Disk Failures):**
    *   Loss or corruption of **non-volatile storage** where the database resides.
    *   Examples: Disk head crash, controller failure, physical damage.
    *   **Most severe;** requires restore from archival backup.

**2. Core Concepts for Recovery:**
*   **Transaction States:** Active → Partially Committed → Committed / Failed → Aborted.
*   **ACID & Recovery:** Recovery mechanisms enforce **Atomicity** (undo failed transactions) and **Durability** (redo committed transactions).
*   **Storage Types:**
    *   **Volatile (Main Memory/Buffer):** Lost on crash.
    *   **Non-Volatile (Disk/Storage):** Survives crash.
    *   **Stable (Archive/Tape):** For long-term backup.

**3. Essential Recovery System Components:**
*   **Log (Journal):** **Sequential, append-only** file recording all actions affecting the database.
    *   **Write-Ahead Logging (WAL) Rule:** Any data page modification must be **logged to stable storage BEFORE the page itself is written to disk.** This is **non-negotiable** for correctness.
    *   **Log Records:** Include `[TID, Action, Object, Old Value (UNDO info), New Value (REDO info)]`.
*   **Checkpoint:** A periodic operation that forces all **dirty pages** (modified in buffer) to disk and writes a special `[checkpoint]` log record.
    *   **Significantly reduces recovery time** by defining a known-good starting point.
*   **Buffer Management:** DBMS buffers data pages in memory. The **steal/no-steal** and **force/no-force** policies define when dirty pages can be written to disk relative to transaction commit.

**4. Recovery Techniques & Algorithms:**
*   **Deferred Update (No-UNDO / REDO):**
    *   **"No-Steal" Buffer Policy.** Modifications are written to the log immediately but **data pages are not updated on disk until AFTER commit.**
    *   On crash: **No failed transaction data on disk, so no UNDO needed.** Must **REDO** committed transactions since their data may not be on disk.
*   **Immediate Update (UNDO/REDO):**
    *   **"Steal" Buffer Policy.** Allows writing uncommitted transaction data to disk.
    *   On crash: Must **UNDO** uncommitted changes **AND REDO** committed changes (since commit may not have forced data to disk).
    *   **Most common in modern systems** (e.g., ARIES).
*   **ARIES (Algorithm for Recovery and Isolation Exploiting Semantics) - Modern Standard:**
    1.  **Analysis:** Scan log forward from last checkpoint to identify dirty pages and active transactions at crash.
    2.  **REDO:** Replay history **forward** from checkpoint, repeating all actions (even for later-aborted transactions) to restore pre-crash state. Uses **Log Sequence Numbers (LSNs)** on pages for idempotence.
    3.  **UNDO:** Roll back **uncommitted** transactions by traversing log backwards, applying compensation log records (CLRs) to ensure undo actions are also logged and redo-able.

**5. Media Failure Recovery:**
*   Requires **full archival backups** (cold/hot) plus the **log of all subsequent transactions**.
*   **Process:** 1) Restore last backup to new disk. 2) **Replay (REDO)** the log from backup time to point of failure.

---

#### **How to Use These Notes & Key Takeaways**

**How to Use These Notes:**
*   Use the **Failure Types** to categorize any outage scenario.
*   The **WAL Rule** and **Checkpoint** are your two most important concepts to recall.
*   Understand the difference between **Deferred** (simpler, less common) and **Immediate** (complex, standard) update techniques.
*   For senior roles, mentioning **ARIES** by name and its three-phase approach demonstrates depth.

**Key Takeaways:**
1.  **WAL (Write-Ahead Logging) is the foundational rule** that makes recovery possible. Log first, data later.
2.  **Checkpoints trade-off runtime overhead for faster recovery.** They define the line between "must process" and "can ignore" in the log.
3.  **Modern DBMS use Immediate Update with Steal/No-Force policies** (like ARIES) for performance, accepting the need for both UNDO and REDO.
4.  **Recovery is about restoring two guarantees: Atomicity (UNDO) and Durability (REDO).**
5.  **Media recovery is a different beast,** relying on **archival backups + complete log replay.**

---

#### **Interview Checklist & 60-Second Recap (Bulleted Points)**

**60-Second Recap (Bullets):**
*   **Failure Types:** **Transaction** (logic/deadlock), **System Crash** (memory lost), **Media** (disk lost—most severe).
*   **Core Mechanism: Write-Ahead Logging (WAL)** – log must hit stable storage *before* the data page.
*   **Key Component: Checkpoints** – periodic sync points that limit recovery work.
*   **Two Techniques:** **Deferred Update** (No-UNDO, only REDO) vs. **Immediate Update** (UNDO+REDO, modern standard).
*   **Modern Algorithm: ARIES** – uses **Analysis, REDO (forward replay), UNDO (rollback)** with LSNs for robustness.
*   **Media Recovery:** Requires **full backups + entire transaction log replay.**

**Senior Architect Interview Checklist:**
✅ **Classify the Failure:** Begin by categorizing the failure type, as the recovery strategy differs radically (transaction abort vs. full restore).
✅ **Emphasize WAL as Non-Negotiable:** Clearly state that any production DBMS must use Write-Ahead Logging. It's the bedrock.
✅ **Explain Checkpoint Trade-off:** Discuss how checkpoint frequency is a configurable trade-off between **runtime performance** (I/O overhead) and **recovery time objective (RTO)**.
✅ **Describe ARIES at a High Level:** Be prepared to outline its three phases. This shows you understand industry-standard implementation.
✅ **Connect to Practical Policies:** Discuss how **Steal/No-Force** buffer policies enable performance but require UNDO/REDO recovery.
✅ **Plan for Media Disaster:** Stress that system crash recovery != media recovery. For the latter, you need **tested backups**, **offsite/archival storage**, and **log retention**.
✅ **Mention Beyond Single Node:** Note that in distributed databases (e.g., quorum-based replication), recovery may involve **node replacement and data stream catch-up** from peers.

**Reference Links:**
- [ScienceDirect: Database Failure](https://www.sciencedirect.com/topics/computer-science/database-failure)
- [Artificium: Database Recovery System](http://artificium.us/lessons/60.dbdesign/l-60-703-db-recovery/l-60-703.html)