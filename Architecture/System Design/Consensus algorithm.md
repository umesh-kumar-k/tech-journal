## Consensus Algorithms Interview Checklist

- **Core Properties**
    
    |Property|Definition|
    |---|---|
    |**Safety**|No two correct nodes decide different values|
    |**Liveness**|Some value is eventually decided|
    |**Fault Tolerance**|Tolerates f failures in N=2f+1 nodes|
    |**Termination**|Finite steps to agreement|
    
- **Key Algorithms**
    
    |Algorithm|Phases|Leader?|Fault Tolerance|Limitations|
    |---|---|---|---|---|
    |**2PC**|Prepare/Commit|Coordinator|Coordinator failure blocks|Blocking|
    |**3PC**|Prepare/Pre-commit/Commit|Coordinator|Better than 2PC|Network partition issues|
    |**Paxos**|Prepare/Accept|No fixed|Byzantine tolerant|Complex|
    |**Raft**|Leader election/Log replication|Yes|Crash faults|Leader bottleneck|
    
- **Raft Protocol States**
    
    - **Leader:** Handles client requests, replicates to followers
        
    - **Follower:** Replicates leader logs
        
    - **Candidate:** During leader election
        
- **Real-World Applications**
    
    |System|Consensus Use|
    |---|---|
    |**etcd**|Distributed KV store coordination|
    |**Kubernetes**|Leader election for control plane|
    |**Cloud Foundry Diego**|Scheduling/management|
    |**Chubby (Google)**|Distributed locks/leases|
    
- **Decision Framework**
    
    |Scenario|Best Algorithm|
    |---|---|
    |**Transactions**|2PC/3PC|
    |**Leader Election**|Raft|
    |**High Fault Tolerance**|Paxos|
    |**Replication**|Raft/Paxos|
    
- **Tools & Implementations**
    
    |Tool|Consensus|Language|
    |---|---|---|
    |**etcd**|Raft|Go|
    |**Consul**|Raft|Go|
    |**TiKV**|Raft|Rust|
    |**CockroachDB**|Raft|Go|
    

## 60-Second Recap

- **Properties:** Safety (no conflicts), Liveness (progress), Termination (finite steps).
    
- **2PC/3PC:** Coordinator-driven txns, blocking risks.
    
- **Paxos:** Complex, leaderless, Byzantine tolerant.
    
- **Raft:** Understandable, leader-based, log replication.
    
- **Gold:** Raft for most cases (etcd, K8s), N=2f+1 quorum.
    

**Reference**: [Consensus Algorithms System Design](https://www.educative.io/blog/consensus-algorithms-in-system-design)[educative](https://www.educative.io/blog/consensus-algorithms-in-system-design)​

1. [https://www.educative.io/blog/consensus-algorithms-in-system-design](https://www.educative.io/blog/consensus-algorithms-in-system-design)
# **Consensus Algorithms - Quick Reference**

**Source:** Educative, "Consensus Algorithms in System Design"  
**URL:** https://www.educative.io/blog/consensus-algorithms-in-system-design

## **1. Essential Question**
*What are consensus algorithms, why are they critical in distributed systems, and how do Paxos, Raft, and other algorithms achieve agreement?*

---

## **2. Main Ideas & Key Details**

### **A. What is Consensus?**
- **Definition:** Multiple distributed nodes agreeing on a single value/state
- **The Problem:** Nodes may fail, networks may partition, messages may be lost/delayed
- **Critical for:** Leader election, configuration management, replicated state machines

### **B. The Consensus Problem (Formal Requirements)**
1. **Agreement:** All non-faulty nodes decide same value
2. **Validity:** Decided value must be proposed by some node
3. **Termination:** Every non-faulty node eventually decides
4. **Integrity:** No node decides twice
5. **Fault Tolerance:** Survive f failures with 2f+1 nodes

### **C. Paxos (The Classic)**
#### **Key Concept:**
- **Quorum-based** voting protocol
- **Roles:** Proposer, Acceptor, Learner
- **Phases:** Prepare → Promise → Accept → Accepted

#### **Basic Paxos Flow:**
```
Phase 1 (Prepare):
1. Proposer sends PREPARE(n) with proposal number n
2. Acceptors respond with PROMISE(n, highest_accepted_value)
   - Promise not to accept proposals < n

Phase 2 (Accept):
3. Proposer sends ACCEPT(n, value)
   - Value = its own OR highest received from promises
4. Acceptors accept if n >= promised number
5. Once majority accept → Consensus reached
```

#### **Variants:**
- **Multi-Paxos:** Optimized for multiple consensus instances (leader election)
- **Cheap Paxos:** Reduced message complexity
- **Fast Paxos:** Fewer message delays in good conditions

#### **Pros/Cons:**
- **✓** Proven correct, widely studied
- **✗** Complex to understand/implement
- **✗** "Two-phase" but not 2PC
- **Used in:** Chubby (Google), Cassandra

### **D. Raft (Understandable Alternative)**
#### **Key Concept:**
- **Leader-based** consensus
- **Easier to understand** than Paxos
- **Roles:** Leader, Follower, Candidate

#### **Raft Components:**
1. **Leader Election:**
   - Random timeout → Candidate → Request votes
   - Majority votes → Leader
   - Term numbers increase with each election

2. **Log Replication:**
   - Only leader accepts client requests
   - Append to log → replicate to followers
   - Committed when majority have it

3. **Safety:** 
   - Election restriction: Only up-to-date logs can become leader
   - Log matching property

#### **Raft vs Paxos:**
```
Raft advantages:
- Easier to understand/implement
- Strong leader simplifies client interaction
- Better for practical implementations

Paxos advantages:
- More symmetric (no single leader bottleneck)
- Better theoretical foundations
```

#### **Used in:** etcd, Consul, Kubernetes, TiDB

### **E. Other Consensus Algorithms**

#### **1. Zab (Zookeeper Atomic Broadcast)**
- **Used by:** Apache ZooKeeper
- **Leader-based** like Raft
- **Broadcast protocol** with FIFO ordering
- **Recovery protocol** for leader failures
- **Features:** Fast leader election, version numbers

#### **2. Viewstamped Replication (VR)**
- **Predecessor** to Paxos/Raft
- **View numbers** track configuration changes
- **Primary** (leader) handles all requests
- **Similar to Raft** but older

#### **3. Gossip Protocols**
- **Epidemic spread** of information
- **Eventual consistency** (not strong consensus)
- **Used for:** Membership detection, failure detection
- **Examples:** Cassandra, Dynamo

### **F. Byzantine Fault Tolerance (BFT)**
#### **The Problem:**
- **Malicious nodes** (not just crash failures)
- **Byzantine Generals Problem:** Traitors may lie
- **Requires:** 3f+1 nodes to tolerate f faulty nodes

#### **Practical BFT (PBFT):**
```
1. Client → Primary: Request
2. Primary → All: Pre-prepare
3. All → All: Prepare
4. All → All: Commit  
5. Reply to client
```
- **Used in:** Permissioned blockchains, some financial systems
- **Challenges:** High communication overhead (O(n²) messages)

#### **Modern BFT Variants:**
- **Tendermint:** BFT for blockchains
- **HotStuff:** Linear message complexity
- **Algorand:** Proof-of-Stake with BFT

### **G. Blockchain Consensus**
#### **1. Proof of Work (PoW)**
- **Bitcoin, Ethereum (pre-merge)**
- **Miners** solve cryptographic puzzle
- **Energy intensive,** probabilistic finality
- **Longest chain** wins

#### **2. Proof of Stake (PoS)**
- **Ethereum 2.0, Cardano**
- **Validators** stake tokens
- **Energy efficient,** deterministic/probabilistic finality
- **Variants:** Delegated PoS, Liquid PoS

#### **3. Federated/Delegated**
- **Hyperledger, Ripple**
- **Pre-selected** validators
- **High throughput,** low decentralization
- **Permissioned** networks

### **H. CAP Theorem & Consensus**
#### **The Impossibility:**
- **CAP Theorem:** Can only guarantee 2 of 3:
  1. **Consistency** (all nodes see same data)
  2. **Availability** (every request gets response)
  3. **Partition Tolerance** (network splits work)
- **Consensus chooses CP** (Consistency + Partition Tolerance)
- **During partitions:** May become unavailable to preserve consistency

#### **Paxos/Raft in CAP:**
- **CP systems** (choose Consistency over Availability during partitions)
- **During network partition:** May block writes until partition heals
- **Minority partition:** Cannot make progress (needs majority)

### **I. Practical Considerations**

#### **When Consensus is Needed:**
1. **Leader election** (who's in charge?)
2. **Configuration management** (what's the config?)
3. **Distributed locks** (who has the lock?)
4. **Service discovery** (where are services?)
5. **Replicated state machines** (what's the state?)

#### **When Consensus is Overkill:**
1. **Simple replication** (use primary-secondary)
2. **Caching layers** (eventual consistency OK)
3. **Analytics/data lakes** (batch consistency fine)
4. **Content delivery** (CDN replication simpler)

#### **Performance Factors:**
1. **Latency:** RTT between nodes
2. **Throughput:** Messages per second
3. **Scalability:** How many nodes before degradation
4. **Fault tolerance:** How many failures can survive

---

## **3. Summary / Synthesis**
Consensus algorithms enable **distributed nodes to agree** on values despite failures. **Paxos** is the classic but complex; **Raft** provides similar guarantees with better understandability. **Byzantine algorithms** handle malicious nodes but are expensive. All consensus algorithms make **CAP trade-offs** (choosing consistency over availability during partitions). Choose **Raft for most practical applications**, **BFT for adversarial environments**, and consider **simpler replication** when consensus isn't truly needed.

---

## **4. Key Terms**
- **Quorum:** Majority of nodes (n/2 + 1)
- **Term/Round:** Epoch/iteration number
- **Log Entry:** Command/value to be agreed upon
- **Committed:** Agreement reached, cannot change
- **Leader/Follower:** Raft roles
- **Proposer/Acceptor/Learner:** Paxos roles
- **View:** Configuration in VR/Zab

---

## **5. Algorithm Comparison Matrix**

| **Algorithm** | **Fault Model** | **Nodes for f faults** | **Message Complexity** | **Use Cases** |
|--------------|----------------|----------------------|----------------------|--------------|
| **Paxos** | Crash failures | 2f+1 | O(n²) | Configuration, locks |
| **Raft** | Crash failures | 2f+1 | O(n) | Service discovery, replication |
| **PBFT** | Byzantine | 3f+1 | O(n²) | Financial systems, blockchains |
| **PoW** | Byzantine | 51% honest | High | Public blockchains |
| **PoS** | Byzantine | 2/3 honest | Medium | Modern blockchains |

---

## **6. Interview Quick Answers**

**Q: "Compare Paxos vs Raft"**
```
"Paxos: More symmetric, complex but proven, used in Google systems
Raft: Leader-based, easier to understand/implement, used in etcd/Consul

Both: Need majority (quorum), tolerate f failures with 2f+1 nodes
Difference: Raft has strong leader, easier for practical systems
Rule: Use Raft unless you need Paxos symmetry"
```

**Q: "How does Raft leader election work?"**
```
"1. Followers have random election timeouts (150-300ms typical)
2. Timeout → Become candidate, increment term, request votes
3. Majority votes → Become leader
4. Leader sends heartbeats to maintain authority
5. If no heartbeats, new election starts
Key: Random timeouts prevent ties, terms ensure at most one leader per term"
```

**Q: "What's the CAP trade-off in consensus?"**
```
"Consensus algorithms are CP systems:
- During network partition: Block writes to preserve consistency
- Minority partition: Cannot make progress (needs majority)
- Only majority partition: Can continue operating
- This ensures consistency but reduces availability during partitions"
```

**Q: "When do you need Byzantine consensus?"**
```
"When nodes can be malicious/Byzantine, not just crash:
1. Public blockchains (anyone can join)
2. Financial systems (insider threats)
3. Military/adversarial environments
4. Permissionless networks

Trade-off: Needs 3f+1 nodes (vs 2f+1), more messages, more complex"
```

---

## **7. Implementation Checklist**

#### **For Raft Implementation:**
✅ Persistent storage for log, term, votedFor  
✅ Leader election with random timeouts  
✅ Log replication with consistency checks  
✅ Safety: Election restriction, log matching  
✅ Client interaction handling  
✅ Membership changes (add/remove nodes)  
✅ Snapshot/compaction for log management  

#### **For Production Use:**
✅ Monitoring: Leader changes, term numbers, log growth  
✅ Health checks and alerts  
✅ Backup and restore procedures  
✅ Upgrade/migration strategy  
✅ Load testing and performance validation  
✅ Security: TLS for communication, authentication  

---

## **8. Common Pitfalls**

1. **Split Brain:** Two leaders (ensure at most one per term)
2. **Log Bloat:** Unlimited log growth (implement snapshots)
3. **Slow Followers:** Lagging behind (monitor replication lag)
4. **Network Partitions:** Minority partition blocked (design for this)
5. **Clock Skew:** Affects timeouts (use monotonic clocks)

---

## **9. Before Interview Checklist**
✅ Understand Paxos 2-phase flow  
✅ Know Raft leader election steps  
✅ Can explain quorum requirements  
✅ Remember CAP implications  
✅ Have examples of where each is used  
✅ Know BFT vs crash fault tolerance difference  

---

## **10. 60-Second Recap**
1. **Consensus = Agreement** among distributed nodes
2. **Paxos:** Classic but complex, symmetric roles
3. **Raft:** Modern alternative, leader-based, easier
4. **BFT:** For malicious nodes, needs 3f+1 nodes
5. **All need majority** (quorum) to make progress
6. **CAP trade-off:** CP systems (consistency over availability)
7. **Use when:** Need strong consistency across nodes
8. **Skip when:** Eventual consistency sufficient

**Remember:** In interviews, focus on **trade-offs** (complexity vs understandability, performance vs safety) and have **concrete use cases** ready (service discovery, configuration management).

*This quick reference covers consensus algorithm essentials for system design interviews.*
