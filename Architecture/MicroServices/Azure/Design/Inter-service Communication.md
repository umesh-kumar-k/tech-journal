
## Inter-Service Communication Interview Checklist

- **Core Challenges**
    
    - **Resiliency:** Transient failures (retries), cascading failures (circuit breakers).
        
    - **Load Balancing:** Session-aware vs random pod selection.
        
    - **Distributed Tracing:** Correlate logs/metrics across service hops.
        
    - **Versioning & Security:** API compatibility, mTLS encryption.
        
- **Communication Patterns**
    
    |Pattern|Pros|Cons|Use Case|
    |---|---|---|---|
    |**Synchronous (HTTP/gRPC)**|Request-response, simple APIs|Tight coupling, latency chains, failure propagation|Workflow coordination|
    |**Asynchronous (Events/Queues)**|Decoupling, multiple subscribers, failure isolation|Complexity, latency, de-duplication|Event sourcing, load leveling|
    
- **Resiliency Patterns**
    
    - **Retry:** Exponential backoff for transient faults; avoid non-idempotent ops (POST/PATCH).
        
    - **Circuit Breaker:** Stop calls to failing services; half-open state for recovery.
        
    - **Bulkhead:** Resource isolation (threads, connections) per service.
        
- **Service Mesh Solutions**
    
    - **Linkerd/Istio:** L7 routing, intelligent LB, auto-retry, circuit breaking, mTLS, tracing.
        
    - **Metrics:** Request volume, latency, error rates, distributed tracing (Jaeger/Zipkin).
        
    - **Trade-off:** Added complexity/latency vs centralized resiliency.
        
- **Distributed Transactions**
    
    - **Saga Pattern:** Compensating transactions for partial failures.
        
    - **Scheduler Agent Supervisor:** Dedicated service orchestrates sagas.
        
    - **Idempotency:** Safe retries via unique request IDs.
        
    - **Checkpoints:** Durable workflow state for crash recovery.
        
- **Drone Delivery Example**
    
    - Ingestion → Event Hubs (async) → Scheduler.
        
    - Scheduler → REST APIs (sync) → Account/Delivery/Package/Drone.
        
    - Delivery → Events → History service (async pub/sub).
        
- **Tools & Frameworks**
    
    |Category|Tools|
    |---|---|
    |**Messaging**|Azure Event Hubs, Kafka, RabbitMQ|
    |**Service Mesh**|Istio, Linkerd, Consul Connect|
    |**Resiliency**|Resilience4j, Hystrix, Polly|
    |**Tracing**|Jaeger, Zipkin, OpenTelemetry|
    |**API Gateway**|Spring Cloud Gateway, Kong|
    
- **Architectural Guidelines**
    
    - Prefer async for loose coupling, sync for coordination.
        
    - Service mesh for production-scale resiliency.
        
    - Design idempotent operations for safe retries.
        
    - Event schemas (Protobuf/Avro) for inter-service decoupling.
        

## 60-Second Recap

- **Sync vs Async:** Sync (HTTP/gRPC) for coordination, Async (Events) for decoupling/load-leveling.
    
- **Resiliency:** Retry + Circuit Breaker + Bulkhead; Service Mesh (Istio/Linkerd) centralizes.
    
- **Distributed Tx:** Saga + Compensating Transactions + Idempotency.
    
- **Gold:** Async-first, service mesh at scale, standardized event schemas, idempotent APIs.
    

**Reference**: [Azure Inter-Service Communication](https://learn.microsoft.com/en-us/azure/architecture/microservices/design/interservice-communication)[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/microservices/design/interservice-communication)​

1. [https://learn.microsoft.com/en-us/azure/architecture/microservices/design/interservice-communication](https://learn.microsoft.com/en-us/azure/architecture/microservices/design/interservice-communication)

**Source:** Microsoft Azure Architecture Center | [Inter-Service Communication](https://learn.microsoft.com/en-us/azure/architecture/microservices/design/interservice-communication)

**Main Idea:** Communication between microservices is a fundamental design concern. The choice between **synchronous** and **asynchronous** patterns has profound implications for coupling, resilience, and scalability. A hybrid approach is typical, with careful use of sync patterns and strategic adoption of async messaging for decoupling.

---

#### **Notes: Communication Styles & Patterns**

**1. Synchronous Communication (Request/Response)**
*   **Mechanism:** Direct HTTP/REST or gRPC calls where the client expects an **immediate response**.
*   **Use Cases:**
    *   Simple queries where **data freshness** is critical.
    *   User-facing operations requiring immediate confirmation.
    *   When the caller **cannot proceed** without the response.
*   **Critical Resilience Patterns (MUST-HAVE):**
    *   **Circuit Breaker:** Prevents cascading failures by failing fast when a service is unhealthy. (Library: **Polly** for .NET)
    *   **Retries with Exponential Backoff:** For transient failures. Must be **idempotent**.
    *   **Timeouts:** Set aggressive timeouts to prevent thread/connection exhaustion.
*   **Drawbacks:** Creates **runtime coupling** (both services must be available), can lead to **deep call chains** and latency.

**2. Asynchronous Communication (Messaging/Events)**
*   **Mechanism:** Services communicate via **messages or events** through a broker, decoupling sender (producer) and receiver (consumer).
*   **Use Cases:**
    *   **Event-driven architectures** (e.g., "OrderPlaced" triggers other processes).
    *   **Background/long-running tasks**.
    *   Broadcasting state changes to multiple consumers.
    *   **Decoupling services** to improve resilience and scalability.
*   **Azure Messaging Services:**
    *   **Azure Service Bus:** **Reliable, ordered messaging** with features like queues, topics, and sessions. Good for **command-driven** communication.
    *   **Azure Event Grid:** **Event routing service** for **reactive, event-driven** architectures. Uses a publish-subscribe model. Lightweight, high-scale.
    *   **Azure Event Hubs:** For **high-throughput event streaming** (big data pipelines).
*   **Benefits:** Decouples services in **time** and **space**, improves resilience, enables scalability.

**3. API Gateways & Service Mesh**
*   **API Gateway (North-South Traffic):**
    *   **Role:** Single entry point for **external clients**. Handles routing, composition, authentication, rate limiting.
    *   **Azure Service:** **Azure API Management**.
    *   **Pattern:** **Gateway Aggregation** – aggregates calls to multiple services to fulfill a client request, reducing chattiness.
*   **Service Mesh (East-West Traffic):**
    *   **Role:** Manages **internal service-to-service** communication within a cluster (e.g., AKS).
    *   **Capabilities:** **Automatic mTLS** (encryption & identity), observability (metrics, traces), traffic shifting (canary releases), resilience (retries, timeouts).
    *   **Azure Implementation:** **Istio** deployed on AKS.

**4. Designing Resilient Communication**
*   **Idempotency:** Design operations so repeating them has the same effect (critical for retries and event processing).
*   **Compensating Transactions:** For operations spanning services, use the **Saga pattern** with compensating actions (e.g., cancel order if payment fails) instead of distributed transactions.
*   **Observability:** Use **distributed tracing** (e.g., Application Insights with OpenTelemetry) to track calls across services for debugging performance issues.

**5. Key Recommendations & Trade-offs**
*   **Favor Asynchronous over Synchronous:** Use async messaging to **decouple services** and avoid deep, brittle call chains. This is the primary pattern for achieving loose coupling.
*   **Use Sync Carefully:** When sync is necessary, **apply resilience patterns aggressively** (circuit breakers, timeouts) and keep call chains **shallow**.
*   **Hybrid is Reality:** Most systems use both. Example: Sync HTTP call to place an order, which then publishes an async "OrderPlaced" event to trigger inventory updates, notifications, etc.
*   **Protocol Choice:** **gRPC** is excellent for **internal service communication** where performance and strong contracts are needed. **REST/HTTP** is ubiquitous and better for public APIs.

---

#### **Summary / Key Takeaways for Interview:**

*   **Communication Defines Coupling:** Your communication patterns **directly determine** the level of coupling between services. Async messaging is the primary tool for achieving loose coupling.
*   **Resilience is Not Optional in Sync Calls:** Any synchronous call **must** be wrapped with circuit breakers, timeouts, and retries. The network is not reliable.
*   **Gateway vs. Mesh:** **API Gateway** is for client-to-service traffic. **Service Mesh** is for service-to-service traffic. They solve different problems and are often used together.
*   **Azure's Messaging Suite is Purpose-Built:** **Service Bus** for reliable messaging, **Event Grid** for reactive events, **Event Hubs** for streaming. Picking the right one is crucial.
*   **Events as the Core Integration Pattern:** Modeling communication around **business domain events** (e.g., `OrderPlaced`) leads to more decoupled, understandable, and evolvable systems than RPC-like calls.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"When would you use synchronous vs. asynchronous communication?"** → **Synchronous** for immediate user feedback or simple queries where the caller *must* have the result to proceed. **Asynchronous** (messaging) for decoupling, background tasks, event broadcasting, and when eventual consistency is acceptable.
*   **"What are the resilience patterns for synchronous calls?"** → The **Trinity**: 1) **Circuit Breaker** (fail fast), 2) **Retries with Exponential Backoff** (for transient faults), 3) **Timeouts** (prevent hanging). Emphasize that all three are **mandatory**.
*   **"How do API Gateways and Service Meshes differ?"** → **API Gateway** is the **front door** for external clients, handling cross-cutting concerns like auth, rate limiting, and API composition. **Service Mesh** is the **internal networking layer** that manages secure, observable, and resilient communication between services inside the cluster.
*   **"When would you choose Azure Service Bus vs. Event Grid?"** → **Service Bus** when you need **guaranteed, ordered delivery** of commands/messages to a specific processor (queue) or set of processors (topic). **Event Grid** when you need **lightweight, massive-scale event routing** to many subscribers for reactive programming (event-driven). "Commands" → Service Bus. "Events" → Event Grid.
*   **"What is the Saga pattern and when is it used?"** → A Saga is a **sequence of local transactions**, each publishing an event to trigger the next step, used to manage **data consistency across multiple services** without distributed transactions. It's used for business transactions spanning services (e.g., "Place Order" involving Inventory, Payment, and Shipping services).