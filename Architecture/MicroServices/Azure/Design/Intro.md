
**Source:** Microsoft Azure Architecture Center | [Designing Microservices](https://learn.microsoft.com/en-us/azure/architecture/microservices/design/)

**Main Idea:** Microservices design extends beyond code to encompass **API design, communication patterns, data management, and resilience strategies**. This section provides concrete patterns and practices for building effective microservices that are loosely coupled, independently deployable, and resilient to failure.

---

#### **Notes: Core Design Principles & Patterns**

**1. API Design (The Contract)**
*   **Fundamental:** APIs are the **public contract** of a service. Design them carefully for stability and loose coupling.
*   **Best Practices:**
    *   **Use RESTful principles or gRPC** based on needs (REST for broad compatibility, gRPC for performance/internal services).
    *   **Apply API versioning** (e.g., URL path, headers) from day one to manage breaking changes.
    *   **Design coarse-grained APIs** to avoid chatty, network-heavy interactions.
    *   **Use HATEOAS** (Hypermedia as the Engine of Application State) where appropriate to decouple clients from URI structures.
*   **Azure Tool:** **Azure API Management** for publishing, securing, and managing APIs at scale.

**2. Communication Patterns**
*   **Synchronous Communication (Request/Response):**
    *   **Use:** For immediate responses and simple queries.
    *   **Protocols:** HTTP/REST, gRPC.
    *   **Risk:** Creates runtime coupling; failure of one service can cascade. Must use **timeouts, retries with backoff, and circuit breakers**.
*   **Asynchronous Communication (Messaging/Events):**
    *   **Use:** For decoupling, background processing, and event-driven architectures.
    *   **Patterns:** **Domain Events** (tactical DDD) published to a message broker.
    *   **Azure Services:** **Azure Service Bus** (reliable messaging), **Event Grid** (event routing), **Event Hubs** (high-throughput streaming).
*   **Hybrid Approach:** Most real-world systems use both. Synchronous for frontend commands, asynchronous for backend processing and inter-service notifications.

**3. Data Management & Consistency**
*   **Database per Service:** Non-negotiable for true autonomy. Each service chooses its own data store (SQL, NoSQL). This **enforces the boundary**.
*   **Polyglot Persistence:** Use the best storage technology for each service's needs (e.g., Cosmos DB for low-latency reads, Azure SQL for complex transactions).
*   **Handling Cross-Service Transactions:**
    *   **Avoid** distributed transactions (2PC).
    *   **Use the Saga Pattern:** A sequence of local transactions, each publishing an event to trigger the next step. **Choreography** (events) or **Orchestration** (central coordinator) styles.
    *   **Embrace Eventual Consistency** where business allows.
*   **Querying Across Services:**
    *   **API Composition:** The gateway or a dedicated service calls multiple services and aggregates results (good for simple queries).
    *   **Command Query Responsibility Segregation (CQRS):** Maintain separate read models (denormalized views) optimized for queries, updated via events from the write service. Use when complex queries span services.

**4. Resilience & Fault Tolerance**
*   **Assume Failure:** Network calls will fail, services will be slow.
*   **Key Resilience Patterns:**
    *   **Circuit Breaker:** Prevent cascading failures by failing fast when a downstream service is unhealthy.
    *   **Bulkheads:** Isolate failures in one part of the system (like resource pools) to protect others.
    *   **Retries with Exponential Backoff:** For transient failures, but must be **idempotent**.
    *   **Fallbacks:** Provide degraded functionality when a service is down.
*   **Azure Integration:** **Azure Application Insights** for monitoring failure rates; libraries like **Polly** (.NET) for implementing patterns.

**5. Service Discovery & Configuration**
*   **Service Discovery:** In dynamic environments (Kubernetes), services need to find each other's network locations.
    *   **Client-Side Discovery:** Client queries a registry (e.g., Consul, Eureka).
    *   **Server-Side Discovery:** Use a load balancer or **service mesh** (Istio on AKS) that handles discovery automatically.
*   **Externalized Configuration:** Store configuration (not secrets) outside the code. Use **Azure App Configuration** or environment-specific config files.

**6. Security Design**
*   **Zero Trust Model:** Authenticate and authorize every service-to-service call.
    *   **API Gateway:** Authenticates external calls.
    *   **Service Mesh:** Handles **mutual TLS (mTLS)** for internal service communication, providing authentication and encrypted traffic.
*   **Secrets Management:** Store secrets (keys, connection strings) in **Azure Key Vault** and retrieve them at runtime using managed identities.

---

#### **Summary / Key Takeaways for Interview:**

*   **Design for Loose Coupling:** Every design decision (API granularity, async messaging, private databases) should aim to **minimize runtime and design-time coupling** between services.
*   **Embrace Asynchronous Patterns:** For true resilience and scalability, **event-driven communication** is often superior to synchronous request chains. It enables better decoupling and fault tolerance.
*   **Data Sovereignty is Paramount:** The **"database per service"** rule is the ultimate litmus test for your boundaries. If you can't give a service its own data store, your boundaries are wrong.
*   **Resilience is Not Optional:** In a distributed system, **failure is a constant**. You must design patterns (circuit breakers, retries, sagas) into the architecture from the start.
*   **Azure Provides Managed Building Blocks:** The design patterns map directly to Azure services: API Management for APIs, Service Bus/Event Grid for messaging, AKS/Istio for service mesh, and Key Vault/App Configuration for config.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"How should microservices communicate with each other?"** → Advocate for a **hybrid approach**: Synchronous (HTTP/gRPC) for simple, immediate queries where coupling is acceptable; **Asynchronous messaging (events)** for decoupling, reliability, and event-driven workflows. Critically, mention the need for **circuit breakers** on synchronous calls.
*   **"How do you handle transactions that update data in two different services?"** → Immediately reject distributed transactions. Explain the **Saga Pattern** using a sequence of local transactions coordinated by events. Mention **eventual consistency** as the trade-off.
*   **"What is CQRS and when would you use it?"** → Define it as separating the **Write model** (commands, domain model) from the **Read model** (denormalized views). Use it when you need **complex queries that span multiple services** or require vastly different data shapes/optimizations than the write model.
*   **"How do you secure communication between microservices?"** → Describe a layered approach: 1) **API Gateway** authenticates external clients, 2) **Service Mesh (e.g., Istio on AKS)** enforces **mTLS** for automatic encryption and service identity between all internal pods, 3) **Authorization** via centralized policies (e.g., OPA).
*   **"What's the role of Azure API Management in this design?"** → It's the **gatekeeper and facade**: publishes APIs, enforces security (OAuth, rate limiting), transforms requests, aggregates responses from multiple services (API composition), and provides analytics. It decouples external clients from the internal service topology.