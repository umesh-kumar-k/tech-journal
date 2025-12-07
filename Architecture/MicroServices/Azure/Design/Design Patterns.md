
**Source:** Microsoft Azure Architecture Center | [Design Patterns](https://learn.microsoft.com/en-us/azure/architecture/microservices/design/patterns)

**Main Idea:** Microservices architecture introduces specific challenges around communication, data management, and reliability. A catalog of established design patterns provides proven solutions to these recurring problems, enabling scalable, resilient, and maintainable systems.

---

#### **Notes: Key Pattern Categories & Selections**

**1. Decomposition Patterns**
*   **Decompose by Business Capability:** Define services corresponding to business functions (e.g., Shipping, Inventory, Ordering). Aligns with organizational structure.
*   **Decompose by Subdomain (DDD):** Define services based on Domain-Driven Design subdomains (Core, Supporting, Generic) and bounded contexts. Provides the most stable boundaries.

**2. Communication Patterns**
*   **API Gateway:** Single entry point for clients. Handles routing, composition, and cross-cutting concerns.
*   **Backend for Frontend (BFF):** Creates separate API gateways tailored to specific client types (mobile, web, public API).
*   **Service Mesh:** Infrastructure layer for managing service-to-service communication (security, observability, routing) within a cluster.
*   **Remote Procedure Invocation (RPI):** Synchronous communication using protocols like HTTP/REST or gRPC. Requires resilience patterns (circuit breaker, retries).
*   **Messaging:** Asynchronous communication using message brokers (Service Bus, Event Grid) for loose coupling and event-driven workflows.

**3. Data Management Patterns**
*   **Database per Service:** Each service owns its private data store. Fundamental for loose coupling.
*   **Saga:** Manages data consistency across services using a sequence of local transactions. **Choreography** (events) or **Orchestration** (central coordinator) styles.
*   **Command Query Responsibility Segregation (CQRS):** Separates read and write models. Useful for complex queries, performance optimization, and scaling reads independently.
*   **Event Sourcing:** Stores state changes as a sequence of immutable events. Reconstructs current state by replaying events. Often combined with CQRS.

**4. Reliability & Resilience Patterns**
*   **Circuit Breaker:** Prevents a service from repeatedly trying an operation that's likely to fail. Fails fast and monitors for recovery.
*   **Bulkhead:** Isolates elements to prevent cascading failures (e.g., separate thread pools, connections for different operations).
*   **Retry:** Automatically retries a failed operation with exponential backoff. Must be **idempotent**.
*   **Health Endpoint Monitoring:** Exposes a standard endpoint (`/health`) for the service to report its status to load balancers and orchestrators.

**5. Observability & Operational Patterns**
*   **Distributed Tracing:** Assigns a unique ID to each request, tracking it across service boundaries for debugging and performance analysis.
*   **Log Aggregation:** Centralizes logs from all services (e.g., using Azure Monitor, Application Insights).
*   **Application Metrics:** Collect and monitor service-level metrics (latency, error rates, traffic).
*   **Sidecar:** Deploys helper components (like proxies, monitoring agents) alongside the main service container. Fundamental to service mesh.

**6. Security Patterns**
*   **Gatekeeper / Valet Key:** The API Gateway (Gatekeeper) performs authentication and issues a short-lived, limited-scope token (Valet Key) for direct access to a resource.
*   **Service Mesh mTLS:** Automatic mutual TLS between services for authentication and encryption, handled at the infrastructure layer.

---

#### **Summary / Key Takeaways for Interview:**

*   **Patterns Solve Systemic Challenges:** These patterns are **interconnected solutions** to the inherent problems of distributed systems (latency, partial failure, data consistency, complexity).
*   **Consistency vs. Availability Trade-off:** Many patterns (Saga, CQRS, Event Sourcing) explicitly accept **eventual consistency** to achieve availability and partition tolerance (Brewer's CAP theorem).
*   **Layers of Communication:** Patterns exist at different communication layers: **Client-to-Service** (API Gateway, BFF), **Service-to-Service** (Service Mesh, RPI, Messaging).
*   **Resilience is Proactive, Not Reactive:** Patterns like **Circuit Breaker** and **Bulkhead** must be **designed in from the start**; they cannot be bolted on later.
*   **Azure Has Managed Implementations:** Most patterns map to Azure services: API Gateway → API Management, Messaging → Service Bus/Event Grid, Orchestration → Durable Functions, Observability → Application Insights.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"How do you ensure data consistency across microservices?"** → Explain the **Saga Pattern**, distinguishing between **Orchestration** (central coordinator, explicit control) and **Choreography** (event-driven, decentralized). Mention **eventual consistency** as the trade-off.
*   **"What is CQRS and when is it useful?"** → Define it as separating the **Write model** (commands, domain model) from the **Read model** (denormalized views). It's useful for: 1) Complex queries spanning services, 2) Different read/write performance/scaling needs, 3) When combined with **Event Sourcing**.
*   **"What patterns would you use to prevent a failure in one service from crashing the whole system?"** → The **Resilience Triad**: 1) **Circuit Breaker** to stop calling a failing service, 2) **Bulkheads** to isolate failures to a component, 3) **Retry with backoff** for transient failures. Also, use **async messaging** to decouple services.
*   **"Explain the difference between API Gateway and Service Mesh."** → **API Gateway** is for **north-south traffic** (client-to-service). It's an edge component handling API publishing, aggregation, and client-specific concerns. **Service Mesh** is for **east-west traffic** (service-to-service). It's an internal infrastructure layer providing transparent security (mTLS), observability, and traffic control.
*   **"What is Event Sourcing?"** → Instead of storing the current state, you store the **sequence of state-changing events**. The current state is derived by replaying events. Benefits: audit trail, temporal queries, and it naturally integrates with event-driven microservices. Often paired with CQRS (the events feed the read model).
