# **Cornell Notes: Distributed Transactions in Microservices - Quick Reference**

**Sources:**
1. Red Hat, "Distributed transaction patterns for microservices compared" (2021)
2. Red Hat, "Patterns for distributed transactions within a microservices architecture" (2018)

**URLs:** 
- https://developers.redhat.com/articles/2021/09/21/distributed-transaction-patterns-microservices-compared
- https://developers.redhat.com/blog/2018/10/01/patterns-for-distributed-transactions-within-a-microservices-architecture

## **1. Essential Question**
*How do you maintain data consistency across microservices without traditional ACID transactions, and what patterns work best for distributed transactions?*

---

## **2. Main Ideas & Key Details**

### **A. The Distributed Transaction Problem**
#### **Why It's Hard:**
- **Microservices = Separate databases** (database per service)
- **No single transaction** can span multiple services
- **Network failures** can leave systems inconsistent
- **CAP theorem:** Can't have perfect consistency, availability, and partition tolerance

#### **Traditional Approach (2PC) Problems:**
- **Two-Phase Commit (2PC) doesn't scale** in microservices
- **Blocks resources** during prepare phase
- **Single point of failure** (coordinator)
- **Not cloud-native** friendly

### **B. 4 Modern Patterns**

#### **Pattern 1: Saga Pattern**
##### **What it is:**
- **Sequence of local transactions** with compensation
- **Each service performs** its local transaction
- **If any step fails,** compensating transactions roll back previous steps
- **Two types:** Choreography (events) vs Orchestration (coordinator)

##### **Choreography (Event-based):**
```
Order Service → "OrderCreated" event
Payment Service subscribes → processes payment → "PaymentProcessed"
Inventory Service subscribes → reserves stock → "InventoryReserved"
Shipping Service subscribes → ships order → "OrderShipped"

If Payment fails → "PaymentFailed" → Order service triggers compensation
```
- **Pros:** Loose coupling, simple to add new services
- **Cons:** Hard to debug (no central control), cyclic dependencies possible

##### **Orchestration (Coordinator-based):**
```
Saga Orchestrator → Order Service (create order)
                  → Payment Service (process payment)  
                  → Inventory Service (reserve stock)
                  → Shipping Service (ship order)

If any fails → orchestrator triggers compensating transactions
```
- **Pros:** Centralized control, easier to understand/debug
- **Cons:** Orchestrator becomes bottleneck, single point of failure

##### **When to use:**
- **Business transactions** spanning multiple services
- **When you need** rollback capability
- **Example:** E-commerce order processing

#### **Pattern 2: Event Sourcing**
##### **What it is:**
- **Store state changes** as immutable events
- **Replay events** to rebuild current state
- **Events = source of truth**

##### **How it works:**
```
Order State = [
  OrderCreated {order_id, customer_id},
  PaymentProcessed {amount},
  InventoryReserved {items},
  OrderShipped {tracking_number}
]

To get current state: Replay all events in sequence
```
- **Pros:** Complete audit trail, time travel queries, decoupled services
- **Cons:** Complex implementation, event schema evolution challenging

##### **When to use:**
- **Need audit trail** (compliance)
- **Complex business logic** with many state changes
- **Example:** Banking systems, insurance claims

#### **Pattern 3: CQRS (Command Query Responsibility Segregation)**
##### **What it is:**
- **Separate models** for reads and writes
- **Write side:** Processes commands, publishes events
- **Read side:** Subscribes to events, builds optimized read models

##### **How it works:**
```
Command: "Place Order" → Write Model → "OrderPlaced" event
Read Model subscribes → Updates denormalized view → Fast queries
```
- **Pros:** Scalability (scale reads/writes independently), optimized for each operation
- **Cons:** Data replication lag (eventual consistency), complexity

##### **When to use:**
- **High read/write ratio** applications
- **Different data shapes** needed for reads vs writes
- **Example:** Social media feed (writes to timeline, reads from feed)

#### **Pattern 4: Transactional Outbox**
##### **What it is:**
- **Reliable event publishing** without 2PC
- **Store events in database** as part of local transaction
- **Background process** reads and publishes events

##### **How it works:**
```
1. Service performs local transaction
2. Also inserts event into outbox table (same transaction)
3. Outbox processor reads unpublished events
4. Publishes to message broker
5. Marks as published
```
- **Pros:** Atomic (event stored with data), reliable delivery
- **Cons:** Additional polling overhead, duplicate publishing possible

##### **When to use:**
- **Need reliable** event delivery
- **When you can't lose** events during failures
- **Example:** Payment processing notifications

### **C. Consistency Models**

#### **1. Strong Consistency (Hard):**
- **All nodes see** same data at same time
- **Requires coordination** (locks, 2PC)
- **Low availability** during partitions
- **Use when:** Financial transactions, inventory counts

#### **2. Eventual Consistency (Common):**
- **Data converges** over time
- **Higher availability** during partitions
- **Acceptable for** many business scenarios
- **Use when:** Social media, user profiles, product catalogs

#### **3. Causal Consistency:**
- **Preserves cause-effect** relationships
- **Weaker than strong,** stronger than eventual
- **Use when:** Chat applications, collaborative editing

### **D. Pattern Combinations**

#### **Common Combinations:**
1. **Event Sourcing + CQRS:**
   - Events stored (Event Sourcing)
   - Separate read/write models (CQRS)
   - **Example:** Banking applications

2. **Saga + Outbox:**
   - Saga steps use outbox for reliable event publishing
   - Ensures events published even if service fails after transaction
   - **Example:** Order processing with reliable notifications

3. **CQRS + Materialized Views:**
   - Read side builds materialized views from events
   - Optimized for query patterns
   - **Example:** Analytics dashboard

### **E. Technology Implementations**

#### **Saga Implementations:**
- **Orchestration:** Camunda, Zeebe, AWS Step Functions
- **Choreography:** Kafka, RabbitMQ (events)

#### **Event Sourcing/CQRS:**
- **Event Store:** EventStoreDB, Kafka (as event log)
- **Frameworks:** Axon Framework, Lagom

#### **Transactional Outbox:**
- **Debezium:** CDC for reading database logs
- **Custom:** Polling service + message broker

---

## **3. Summary / Synthesis**
Distributed transactions in microservices require **eventual consistency patterns** instead of traditional ACID. **Saga pattern** handles multi-step business transactions with compensation. **Event sourcing** provides audit trail by storing state changes. **CQRS** separates reads/writes for scalability. **Transactional outbox** ensures reliable event publishing. Choose **eventual consistency** where possible, combine patterns as needed, and accept that **distributed transactions are inherently complex**.

---

## **4. Key Terms**
- **Saga:** Sequence of local transactions with compensation
- **Event Sourcing:** Store state changes as immutable events
- **CQRS:** Command Query Responsibility Segregation
- **Outbox Pattern:** Store events in DB before publishing
- **Compensating Transaction:** Rollback action for Saga
- **Eventual Consistency:** Data converges over time

---

## **5. Pattern Selection Matrix**

| **Pattern** | **Best For** | **Consistency** | **Complexity** | **When to Avoid** |
|------------|-------------|----------------|---------------|------------------|
| **Saga** | Business workflows | Eventual | Medium-High | Simple CRUD operations |
| **Event Sourcing** | Audit trails, replay | Eventual | High | Simple data, no audit needed |
| **CQRS** | High read/write apps | Eventual | High | Simple queries, small scale |
| **Transactional Outbox** | Reliable messaging | Eventual | Medium | Direct DB integration possible |

---

## **6. Decision Framework**

#### **Ask These Questions:**
1. **Can operations be undone?** Yes → Saga with compensation
2. **Need audit trail?** Yes → Event sourcing
3. **Different read/write patterns?** Yes → CQRS
4. **Need reliable event delivery?** Yes → Transactional outbox
5. **Can accept eventual consistency?** Usually yes in microservices

#### **Default Rules:**
- **Start simple:** Avoid distributed transactions if possible
- **Use eventual consistency** where business allows
- **Implement idempotency** for retry safety
- **Monitor data consistency** gaps
- **Have reconciliation processes** for fixing inconsistencies

---

## **7. Interview Quick Answers**

**Q: "How do you handle distributed transactions in microservices?"**
```
"Four main patterns:
1. Saga - for business workflows with compensation
2. Event Sourcing - for audit trails and state reconstruction  
3. CQRS - for scaling reads and writes independently
4. Transactional Outbox - for reliable event delivery

We accept eventual consistency and design for idempotency and compensation."
```

**Q: "Compare Saga choreography vs orchestration"**
```
"Choreography: Services publish/consume events, no coordinator
- Pros: Loose coupling, easy to add services
- Cons: Hard to debug, cyclic dependencies possible

Orchestration: Central coordinator manages workflow
- Pros: Centralized control, easier to understand
- Cons: Single point of failure, bottleneck

Choose choreography for simple flows, orchestration for complex workflows."
```

**Q: "What's the problem with 2PC in microservices?"**
```
"2PC doesn't work well because:
1. Blocks resources during prepare phase (scalability issue)
2. Coordinator is single point of failure
3. Not cloud-native (assumes reliable network)
4. Services become tightly coupled
5. Doesn't handle long-running business transactions well

Modern patterns (Saga, etc.) are better for microservices."
```

**Q: "How do you ensure data consistency eventually?"**
```
"Four strategies:
1. Idempotent operations - safe to retry
2. Compensation transactions - undo if needed
3. Reconciliation jobs - detect and fix inconsistencies
4. Monitoring - track consistency gaps and SLA violations

Accept that 100% immediate consistency isn't possible in distributed systems."
```

---

## **8. Real-World Examples**

#### **E-commerce Order Processing (Saga + Outbox):**
1. Order service creates order → stores in DB + "OrderCreated" in outbox
2. Payment service processes → publishes "PaymentProcessed"
3. Inventory service reserves → publishes "InventoryReserved"
4. Shipping service ships → publishes "OrderShipped"
5. If payment fails → compensating transaction releases inventory

#### **Banking System (Event Sourcing + CQRS):**
1. Commands: "Deposit $100", "Withdraw $50"
2. Events stored: "Deposited $100", "Withdrew $50"
3. Read side: Current balance = sum of all deposit/withdrawal events
4. Audit trail: All transactions immutable and queryable

#### **Social Media (CQRS):**
1. Write: User posts update → stored, event published
2. Read: Timeline service builds optimized feed from events
3. Separation: Scale timeline reads independently from post writes

---

## **9. Implementation Checklist**

#### **For Saga Pattern:**
✅ Define compensation for each step  
✅ Make operations idempotent  
✅ Implement retry with exponential backoff  
✅ Add monitoring for saga completion/failure rates  
✅ Design for long-running transactions (hours/days)

#### **For Event Sourcing:**
✅ Design event schema with versioning  
✅ Implement snapshotting for performance  
✅ Handle event schema evolution  
✅ Build replay mechanism  
✅ Consider storage requirements (events can grow large)

#### **For Production:**
✅ Monitoring: Consistency gaps, compensation rates  
✅ Alerting: Failed sagas, stuck events  
✅ Testing: Failure scenarios, network partitions  
✅ Documentation: Data flow, compensation logic  
✅ Rollback plans: For when patterns fail

---

## **10. Before Interview Checklist**
✅ Know 4 main patterns (Saga, Event Sourcing, CQRS, Outbox)  
✅ Understand eventual consistency implications  
✅ Can explain Saga choreography vs orchestration  
✅ Know why 2PC doesn't work for microservices  
✅ Have real-world examples ready  
✅ Understand trade-offs between patterns

---

## **11. 60-Second Recap**
1. **Distributed transactions hard** in microservices (separate DBs)
2. **Four patterns:** Saga, Event Sourcing, CQRS, Transactional Outbox
3. **Saga:** Business workflows with compensation
4. **Event Sourcing:** Store changes, replay to get state
5. **CQRS:** Separate read/write models for scalability
6. **Accept eventual consistency** - design for it
7. **Combine patterns** as needed (e.g., Saga + Outbox)
8. **Avoid 2PC** - doesn't scale for microservices

**Remember:** In interviews, emphasize **business requirements driving technical choices** and **trade-offs you're making** (consistency vs availability, complexity vs functionality).

*This quick reference covers distributed transaction patterns for microservices.*