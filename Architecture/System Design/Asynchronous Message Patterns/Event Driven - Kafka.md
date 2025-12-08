## Event-Driven Topic Design Interview Checklist

- **Core Topic Types**
    
    - **Entity Topics:** Current state of objects (upsert pattern); key-based compaction keeps latest version.[stackoverflow](https://stackoverflow.blog/2022/07/21/event-driven-topic-design-using-kafka/)​
        
    - **Event Topics:** Immutable "X happened" records; analytics, choreography triggers.
        
    - **Request/Response Topics:** Async APIs; orchestration pattern with correlation IDs.
        
- **Kafka-Specific Advantages**
    
    - **Key Partitioning:** Same key = same partition = ordered processing.
        
    - **Log Compaction:** Delete all but latest message per key (tombstones for deletes).
        
    - **Retention Policies:** Messages persist until TTL/compaction, not immediate deletion.
        
    - **Multiple Consumers:** Consumer groups track independent offsets.
        
- **Entity Topics Deep Dive**
    
    - Producer publishes current state; consumers upsert to local DB.
        
    - **Eventual consistency** (acceptable delay); no cross-service DB queries.
        
    - Schema evolution: Add fields easily, deletions require consumer coordination.
        
    - **Tombstones:** Key + null payload = delete record.
        
- **Event Topics Guidelines**
    
    - Capture **complete context** at event time (can't enrich later).
        
    - Partition by entity ID for related event ordering.
        
    - Multiple subscribers (pub/sub); no response expected.
        
- **Request/Response Pattern**
    
    - Multiple producers → single consumer owns schema.
        
    - Use correlation IDs to match request/response.
        
    - Fire-and-forget or hybrid (sync request → async response).
        
- **Topic Anti-Patterns & Pitfalls**
    
    - **Joining streams:** Throughput mismatch, re-partitioning costs.
        
    - **Kafka Streams pitfalls:** State in Kafka → broker overload, opaque internals.
        
    - **Over-fetching:** Prefer partial DB writes over stream joins.
        
- **Tools & Frameworks**
    
    |Category|Tools|
    |---|---|
    |**Schema**|Avro, Protobuf (strongly typed)|
    |**Stream Processing**|Kafka Streams, ksqlDB, Flink|
    |**Connectors**|Deimos, Kafka Connect|
    |**Monitoring**|Kafka Manager, Confluent Control Center|
    

## 60-Second Recap

- **3 Topic Types:** Entity (state, compaction), Event (immutable facts), Request/Response (async APIs).
    
- **Kafka Superpowers:** Keys, compaction, retention, multi-consumer offsets.
    
- **Entity Gold:** Upsert local DB copies → no cross-service queries.
    
- **Pitfalls:** Avoid stream joins; use DB partial writes instead.
    
- **Schema First:** Avro/Protobuf + consistent schemas per topic.
    

**Reference**: [Event-Driven Topic Design Using Kafka](https://stackoverflow.blog/2022/07/21/event-driven-topic-design-using-kafka/)[stackoverflow](https://stackoverflow.blog/2022/07/21/event-driven-topic-design-using-kafka/)​

1. [https://stackoverflow.blog/2022/07/21/event-driven-topic-design-using-kafka/](https://stackoverflow.blog/2022/07/21/event-driven-topic-design-using-kafka/)

# **Event-Driven Topic Design with Kafka - Quick Reference**

**Source:** Stack Overflow Blog, "Event-driven topic design using Kafka"  
**URL:** https://stackoverflow.blog/2022/07/21/event-driven-topic-design-using-kafka/

## **1. Essential Question**
*How should you design Kafka topics and events for scalable, maintainable event-driven systems?*

---

## **2. Main Ideas & Key Details**

### **A. Event Design Principles**

#### **1. Use Past Tense for Events:**
- **Good:** `OrderPlaced`, `PaymentProcessed`, `UserRegistered`
- **Bad:** `PlaceOrder`, `ProcessPayment`, `RegisterUser`
- **Why:** Events are facts about things that *have happened*

#### **2. Include Correlation IDs:**
- **Track events** across services
- **Example:** `correlation_id: "req-123-xyz"`
- **Useful for:** Debugging, tracing, auditing

#### **3. Version Your Events:**
```json
{
  "event_type": "OrderPlaced",
  "version": "1.0",
  "data": { ... }
}
```
- **Allows schema evolution** without breaking consumers
- **Strategies:** 
  - Add new fields (backward compatible)
  - Create new topic for breaking changes
  - Use schema registry

#### **4. Include Timestamps:**
- **Event time:** When it happened (`occurred_at`)
- **Processing time:** When Kafka received it
- **Use event time** for business logic

### **B. Topic Design Strategies**

#### **1. One Topic per Event Type:**
```
orders.order_placed
orders.order_cancelled  
payments.payment_processed
inventory.item_reserved
```
- **Pros:** Clear purpose, easy filtering
- **Cons:** Many topics to manage

#### **2. Domain-based Topics:**
```
orders (all order-related events)
payments (all payment events)  
users (all user events)
```
- **Pros:** Fewer topics, domain focus
- **Cons:** Consumers get unrelated events

#### **3. Hybrid Approach:**
```
orders.placed
orders.updated
orders.cancelled
payments.processed
payments.failed
```
- **Most practical** approach
- **Balance** between specificity and manageability

### **C. Partitioning Strategies**

#### **1. Key-based Partitioning:**
```java
// Messages with same key go to same partition
producer.send(new ProducerRecord<>("orders", orderId, event));
```
- **Ensures ordering** for same key
- **Example:** All events for `orderId=123` → same partition
- **Use when:** Need ordered processing per entity

#### **2. Round-robin (No Key):**
- **Even distribution** across partitions
- **No ordering guarantees**
- **Use when:** Order doesn't matter, maximize throughput

#### **3. Custom Partitioner:**
- **Implement custom logic** for partition assignment
- **Example:** Partition by region, tenant, or business unit
- **Advanced use cases only**

#### **Partition Key Selection Guidelines:**
- **High cardinality** (many distinct values)
- **Even distribution** across keys
- **Aligned with** processing needs
- **Example:** 
  - Good: `user_id`, `order_id` 
  - Bad: `country` (only ~200 values), `status` (few values)

### **D. Retention & Compaction**

#### **1. Time-based Retention:**
```properties
retention.ms=604800000  # 7 days
retention.bytes=1073741824  # 1GB
```
- **Delete messages** after time/size limit
- **Good for:** Ephemeral data, logs
- **Bad for:** State reconstruction needs

#### **2. Log Compaction:**
```properties
cleanup.policy=compact
```
- **Keeps latest value** per key
- **Removes older duplicates**
- **Good for:** 
  - Current state (latest user profile)
  - Change Data Capture (CDC)
  - **Not for:** Event sourcing (need all events)

#### **3. Infinite Retention:**
- **Keep forever** (or very long)
- **Costly** but enables replayability
- **Use for:** Audit trails, compliance

### **E. Consumer Group Design**

#### **1. Consumer Group Patterns:**
```
# Multiple consumers in same group
--group-id order-processors

Result: Each partition → one consumer
Scale by adding consumers (up to # partitions)
```

#### **2. Independent Consumers:**
```
# Each consumer has unique group ID
--group-id order-processor-1
--group-id order-processor-2
--group-id analytics-service

Result: All get all messages
Use for: Different services processing same events
```

#### **3. Rebalancing Strategies:**
- **Eager rebalancing** (default): Stop all, reassign
- **Incremental rebalancing:** Minimize disruption
- **Static membership:** Avoid unnecessary rebalances
- **Consider:** Rebalance cost vs fairness

### **F. Schema Evolution & Compatibility**

#### **1. Compatibility Types:**
- **Forward compatible:** Old consumers read new data
- **Backward compatible:** New consumers read old data
- **Full compatible:** Both directions
- **None:** Breaking changes

#### **2. Safe Changes (Backward Compatible):**
- **Add optional fields** (with defaults)
- **Remove optional fields** (if not used)
- **Change field names** (add alias)
- **Example:** Add `discount_amount` field

#### **3. Breaking Changes:**
- **Remove required fields**
- **Change data types**
- **Rename without alias**
- **Strategy:** New topic version

#### **4. Schema Registry:**
- **Central schema management**
- **Compatibility checking**
- **Version control**
- **Examples:** Confluent Schema Registry, Apicurio

### **G. Error Handling & Dead Letter Queues**

#### **1. Retry Strategies:**
- **Exponential backoff:** 1s, 2s, 4s, 8s...
- **Maximum retries:** Prevent infinite loops
- **Retry topics:** Separate topic for retries

#### **2. Dead Letter Topics:**
```
orders.processed → orders.processed.DLQ
```
- **Store failed messages** for manual inspection
- **Configure:** After N retries → DLQ
- **Monitor:** DLQ size alerts

#### **3. Poison Pill Handling:**
- **Detect patterns:** Same message failing repeatedly
- **Skip after threshold:** Continue processing others
- **Alert:** Manual intervention needed

### **H. Performance Optimization**

#### **1. Batch Size Tuning:**
```properties
batch.size=16384  # 16KB
linger.ms=5  # Wait up to 5ms for batch
```
- **Larger batches:** More throughput, higher latency
- **Smaller batches:** Lower latency, less throughput
- **Find balance** for your use case

#### **2. Compression:**
```properties
compression.type=snappy  # or gzip, lz4, zstd
```
- **Reduce network/storage** usage
- **CPU trade-off:** More compression = more CPU
- **snappy:** Good balance of speed/ratio

#### **3. Producer Acks:**
- **acks=0:** Fire and forget (fastest, may lose data)
- **acks=1:** Leader confirms (balanced)
- **acks=all:** All replicas confirm (safest, slowest)

#### **4. Consumer Fetch Size:**
```properties
fetch.min.bytes=1
fetch.max.wait.ms=500
max.partition.fetch.bytes=1048576  # 1MB
```
- **Balance latency vs throughput**
- **Larger fetches:** Better throughput, more memory
- **Smaller fetches:** Lower latency, more overhead

---

## **3. Summary / Synthesis**
Design Kafka topics with **clear naming** (domain.event), **proper partitioning** (key-based for ordering), and **appropriate retention** (time-based or compaction). Use **past tense events** with **correlation IDs** and **versioning**. Plan for **schema evolution** with backward compatibility. Implement **error handling** with DLQs. Optimize performance with **batch sizing, compression, and appropriate acks**. Consumer groups should match **processing needs** (shared for scaling, unique for broadcasting).

---

## **4. Key Terms**
- **Topic:** Category/feed name for messages
- **Partition:** Subdivision of topic for parallelism
- **Offset:** Message position within partition
- **Consumer Group:** Set of consumers sharing work
- **Compaction:** Keep only latest message per key
- **Rebalancing:** Redistribute partitions among consumers
- **Schema Registry:** Central schema management

---

## **5. Topic Naming Convention Examples**

#### **Good:**
```
# Domain.event format
orders.placed
orders.cancelled
payments.processed
users.registered

# With versioning
orders.placed.v1
orders.placed.v2  # Breaking changes
```

#### **Bad:**
```
topic1
data
events
stream  # Too generic
```

#### **With Environment:**
```
dev.orders.placed
staging.orders.placed  
prod.orders.placed
```

### **Retention Strategy by Use Case:**

| **Use Case** | **Retention** | **Compaction** | **Reason** |
|-------------|--------------|---------------|-----------|
| **Event Sourcing** | Long (years) | No | Need all events for replay |
| **Current State** | Long | Yes | Only need latest value |
| **Logging** | Short (days) | No | Ephemeral data |
| **Metrics** | Medium (weeks) | No | Historical analysis |
| **CDC** | Medium | Yes | Latest DB state |

---

## **6. Interview Quick Answers**

**Q: "How do you design Kafka topics for an e-commerce system?"**
```
"Domain.event naming:
1. orders.placed (key: order_id)
2. orders.cancelled (key: order_id)  
3. payments.processed (key: payment_id)
4. inventory.updated (key: product_id)
5. shipments.created (key: shipment_id)

Retention: 30 days for events, compaction for current state
Partitioning: Key-based for ordering per entity
Versioning: All events include version field"
```

**Q: "When to use compaction vs time-based retention?"**
```
"Use compaction when:
- Need latest state (user profile, product inventory)
- Doing Change Data Capture (CDC)
- Want to reduce storage but keep current state

Use time-based when:
- Need all events (event sourcing, audit trail)
- Events are independent (logs, metrics)
- Storage cost acceptable

Compaction = Keep latest per key
Time-based = Keep all for time period"
```

**Q: "How do you handle schema changes in Kafka?"**
```
"Three strategies:
1. Backward compatible changes (add optional fields)
2. New topic version (orders.placed.v2 for breaking changes)
3. Schema registry with compatibility rules

Always:
- Include version in events
- Test compatibility
- Deploy consumers before producers for new schemas
- Have rollback plan"
```

**Q: "What's the impact of partition key choice?"**
```
"Partition key determines:
1. Ordering: Same key → same partition → ordered
2. Distribution: Keys should be evenly distributed
3. Parallelism: # partitions = max parallel consumers

Choose keys with:
- High cardinality (user_id, order_id)
- Business relevance (all order events same partition)
- Even distribution (avoid hotspots)

Bad keys: status, country_code (low cardinality)"
```

---

## **7. Common Anti-patterns**

#### **1. Too Many/Few Partitions:**
- **Too many:** Overhead, unnecessary
- **Too few:** Limited parallelism
- **Rule of thumb:** Start with (# consumers × 2), monitor

#### **2. Generic Event Topics:**
- **Bad:** `events`, `data`
- **Good:** Domain-specific topics
- **Why:** Easier management, filtering, permissions

#### **3. Ignoring Ordering Needs:**
- **If order matters:** Use key-based partitioning
- **If order doesn't matter:** Round-robin or random
- **Mixed needs:** Separate topics

#### **4. No Schema Management:**
- **Problem:** Breaking changes break consumers
- **Solution:** Schema registry, versioning
- **Always:** Plan for evolution

#### **5. Ignoring DLQs:**
- **Problem:** Lost messages, no visibility
- **Solution:** DLQ for failed messages
- **Monitor:** DLQ size alerts

---

## **8. Monitoring & Observability**

#### **Key Metrics to Track:**
1. **Lag:** Consumer offset behind producer
2. **Throughput:** Messages per second
3. **Latency:** End-to-end processing time
4. **Error rate:** Failed message percentage
5. **Disk usage:** Topic size growth
6. **Rebalances:** Frequency and duration

#### **Alerting Rules:**
- **Consumer lag** > threshold (e.g., 1000 messages)
- **DLQ size** growing
- **Throughput** dropping significantly
- **Error rate** > 1%

#### **Tools:**
- **Kafka Manager:** Web UI
- **Confluent Control Center:** Enterprise
- **Prometheus + Grafana:** Custom dashboards
- **Burrow:** Lag monitoring

---

## **9. Before Interview Checklist**
✅ Understand topic naming best practices  
✅ Know partitioning strategies (key vs round-robin)  
✅ Can explain retention vs compaction  
✅ Understand consumer group patterns  
✅ Know schema evolution strategies  
✅ Have example topic design ready

---

## **10. 60-Second Recap**
1. **Topic naming:** `domain.event` (past tense)
2. **Events include:** Version, correlation ID, timestamp
3. **Partitioning:** Key-based for ordering, high cardinality keys
4. **Retention:** Time-based or compaction based on use case
5. **Consumer groups:** Shared for scaling, unique for broadcasting
6. **Schema evolution:** Backward compatible changes, version topics for breaking
7. **Error handling:** DLQ for failed messages, retry with backoff
8. **Monitoring:** Lag, throughput, errors

**Remember:** In interviews, emphasize **trade-offs** (ordering vs parallelism, storage vs replayability) and **business requirements driving technical choices**.

*This quick reference covers Kafka topic design essentials for system design interviews.*