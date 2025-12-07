# **Azure Architecture Patterns Catalog**

## **Reliability Patterns**

**Circuit Breaker:**  
Detect failures and prevent cascading failures by failing fast when services are unhealthy.  
*Key Point:* Implements `Closed → Open → Half-Open` states.

**Health Endpoint Monitoring:**  
Expose functional checks for external monitoring tools.  
*Key Point:* Load balancers route away from unhealthy instances.

**Queue-Based Load Leveling:**  
Use queues as buffers to smooth intermittent heavy loads.  
*Key Point:* Prevents overwhelming services during traffic spikes.

**Retry Pattern:**  
Automatically retry failed operations with exponential backoff.  
*Key Point:* Only retry transient faults, not logical errors.

**Bulkhead Pattern:**  
Isolate components to prevent cascading failures.  
*Key Point:* Critical and non-critical services in separate resource pools.

**Scheduler Agent Supervisor:**  
Coordinate distributed actions across multiple services.  
*Key Point:* `Scheduler` initiates → `Agent` performs → `Supervisor` monitors.

**Leader Election:**  
Coordinate tasks by electing a leader among distributed instances.  
*Key Point:* Uses blob leases or consensus algorithms for coordination.

---

## **Cost Optimization Patterns**

**Static Content Hosting:**  
Deploy static content to cloud storage with CDN.  
*Key Point:* Reduces compute costs, offloads traffic from servers.

**Cache-Aside (Lazy Loading):**  
Load data into cache on demand.  
*Key Point:* Reduces database costs, improves performance.

**Materialized View:**  
Generate pre-computed views optimized for queries.  
*Key Point:* Trade storage cost for compute/query cost reduction.

**Throttling Pattern:**  
Control resource consumption by users/applications.  
*Key Point:* Prevents unexpected bills, enforces quotas.

---

## **Operational Excellence Patterns**

**Ambassador Pattern:**  
Helper services that offload common client connectivity tasks.  
*Key Point:* Centralizes logging, monitoring, retry logic.

**Sidecar Pattern:**  
Deploy application components into separate processes/containers.  
*Key Point:* Enables heterogeneous components, reusable utilities.

**Strangler Fig Pattern:**  
Incrementally replace legacy systems.  
*Key Point:* `Transform → Coexist → Eliminate` phases.

**Anti-Corruption Layer:**  
Isolating layer between legacy and new systems.  
*Key Point:* Translates between different domain models.

---

## **Performance Efficiency Patterns**

**CQRS (Command Query Responsibility Segregation):**  
Separate read and write models.  
*Key Point:* Optimize each independently, scales reads separately.

**Event Sourcing:**  
Store state changes as immutable events.  
*Key Point:* Replay events to reconstruct state, enables audit trail.

**Sharding Pattern:**  
Horizontal partitioning of data stores.  
*Key Point:* `Hash`, `Range`, `Lookup`, or `Geographic` sharding strategies.

**Priority Queue Pattern:**  
Process higher priority requests first.  
*Key Point:* Multiple queues with different priorities.

**Pipes and Filters:**  
Break processing into independent steps.  
*Key Point:** Reusable components, independent scaling of filters.

**Competing Consumers:**  
Multiple consumers process messages from same queue.  
*Key Point:* Horizontal scaling, load balancing.

---

## **Security Patterns**

**Federated Identity:**  
Delegate authentication to external identity provider.  
*Key Point:* Single sign-on, uses OAuth 2.0/OpenID Connect.

**Gatekeeper Pattern:**  
Protect apps with dedicated host instance.  
*Key Point:* Validates, sanitizes, audits all requests.

**Valet Key Pattern:**  
Tokens providing restricted direct access to resources.  
*Key Point:* Reduces application load, uses SAS tokens.

---

## **Messaging & Integration Patterns**

**Publisher-Subscriber:**  
Publishers send to topics, subscribers receive based on interest.  
*Key Point:* Loose coupling, dynamic subscription.

**Choreography Pattern:**  
Services participate in workflow decisions via events.  
*Key Point:* No central coordinator, event-driven.

**Compensating Transaction:**  
Undo work using compensating transactions.  
*Key Point:** Saga pattern implementation for distributed transactions.

---

## **API & Gateway Patterns**

**Backends for Frontends (BFF):**  
Separate backend services per client type.  
*Key Point:* Mobile BFF vs Web BFF with different optimizations.

**Gateway Aggregation:**  
Aggregate multiple requests into single request.  
*Key Point:* Reduces chattiness, improves mobile performance.

**Gateway Offloading:**  
Offload shared functionality to gateway.  
*Key Point:* SSL termination, authentication, caching at edge.

**Gateway Routing:**  
Route requests to multiple services via single endpoint.  
*Key Point:** Reverse proxy based on URL, headers, or other criteria.

---

## **Quick Reference by Priority**

### **Must-Know for Interviews:**
1. **Circuit Breaker** (Resilience)
2. **CQRS + Event Sourcing** (Data Management)
3. **Retry Pattern** (Resilience)
4. **Publisher-Subscriber** (Messaging)
5. **Cache-Aside** (Performance/Cost)

### **Cloud-Native Essentials:**
1. **Sidecar/Ambassador** (Microservices)
2. **BFF** (Client Optimization)
3. **Health Endpoint** (Monitoring)
4. **Static Hosting** (Cost Optimization)

### **Migration & Integration:**
1. **Strangler Fig** (Legacy Migration)
2. **Anti-Corruption Layer** (System Integration)
3. **Sharding** (Database Scaling)

### **Azure-Specific Features:**
- Most patterns have direct Azure service equivalents
- Managed services reduce implementation complexity
- Consider lock-in vs. flexibility trade-offs

---

## **Pattern Selection Quick Guide**

```
Need fault tolerance? → Circuit Breaker + Retry
Scaling reads vs writes? → CQRS
Legacy migration? → Strangler Fig + Anti-Corruption Layer
Microservices? → Sidecar + BFF + Gateway patterns
Event-driven? → Publisher-Subscriber + Event Sourcing
Cost optimization? → Static Hosting + Cache-Aside + Throttling
```

---

**Source:** Microsoft Azure Architecture Patterns Catalog  
**URL:** https://learn.microsoft.com/en-us/azure/architecture/patterns/  
**Reading Time:** 2-3 minutes for quick revision

