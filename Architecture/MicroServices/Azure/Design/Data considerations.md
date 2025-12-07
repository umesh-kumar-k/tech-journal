
**Source:** Microsoft Azure Architecture Center | [Data Considerations](https://learn.microsoft.com/en-us/azure/architecture/microservices/design/data-considerations)

**Main Idea:** Data management is the most challenging aspect of microservices architecture. It requires a fundamental shift from centralized, shared databases to **decentralized data ownership**, embracing polyglot persistence and eventual consistency patterns to maintain service autonomy and loose coupling.

---

#### **Notes: Core Principles & Patterns**

**1. Foundational Rule: Database per Service**
*   **Non-Negotiable Principle:** Each microservice **must own its private data store**. The database is part of the service boundary and is inaccessible to other services directly.
*   **Why:** Enforces loose coupling. Services can't create hidden dependencies by querying each other's databases. They must communicate via well-defined APIs.
*   **Implication:** **No shared databases** between services. This is the primary rule that prevents a **distributed monolith**.

**2. Polyglot Persistence**
*   **Concept:** Each service chooses the **data storage technology best suited** to its data model and access patterns.
*   **Examples:**
    *   **Relational (Azure SQL DB, PostgreSQL):** For complex transactions and strong consistency within a service.
    *   **Document (Cosmos DB SQL API, MongoDB):** For flexible, hierarchical data.
    *   **Key-Value (Azure Cache, Cosmos DB Table API):** For fast lookups by key.
    *   **Graph (Cosmos DB Gremlin API):** For relationships and traversals.
    *   **Search (Azure Cognitive Search):** For full-text search.
*   **Benefit:** Optimizes performance and scalability per service. Empowers team autonomy.

**3. Managing Data Consistency Across Services**
*   **The Challenge:** Traditional ACID transactions are impossible across different databases/services.
*   **The Solution:** **Eventual Consistency** and the **Saga Pattern**.
    *   **Saga Pattern:** A sequence of local transactions, each within a single service. Each step **publishes a domain event** to trigger the next step. If a step fails, **compensating transactions** (rollbacks) are executed.
    *   **Two Styles:**
        *   **Choreography:** Services publish/subscribe to events directly. More decoupled.
        *   **Orchestration:** A central coordinator (orchestrator) manages the sequence.
*   **Accepting Trade-offs:** Business logic must often be redesigned to tolerate temporary data inconsistency.

**4. Querying Data Across Multiple Services**
*   **Problem:** How to implement queries that need data owned by several services (e.g., "Show me orders with customer details")?
*   **Pattern 1: API Composition (Simple Aggregation):**
    *   The caller (or an API Gateway/Aggregator service) calls multiple services and merges the results in memory.
    *   **Good for:** Simple queries, low-latency requirements, small result sets.
*   **Pattern 2: Command Query Responsibility Segregation (CQRS):**
    *   Maintain a separate, **read-optimized data store** (a denormalized view) specifically for queries.
    *   The read store is kept up-to-date by **subscribing to domain events** published by the services that own the data.
    *   **Good for:** Complex queries, reporting, dashboards, or when the read/write performance profiles differ vastly.
*   **Trade-off:** CQRS introduces complexity (data duplication, eventual consistency in the read model) but offers superior scalability and flexibility for queries.

**5. Handling Shared Reference Data**
*   **Problem:** Some data (e.g., product catalog, country codes) is needed by many services.
*   **Anti-Pattern:** A single, shared reference database.
*   **Better Patterns:**
    1.  **Reference Data as a Service:** Create a dedicated reference data service. Other services cache its data locally.
    2.  **Event-Based Propagation:** The "owner" of the reference data publishes update events. Other services maintain a local copy (cache) updated via events.
    3.  **Database per Service with Duplication:** Each service maintains its own copy of the reference data it needs, even if duplicated.

**6. Transactional Messaging & Idempotency**
*   **Ensuring Reliability:** When a service updates its database *and* publishes an event, both must succeed or fail together to avoid data inconsistency.
*   **Pattern: Transactional Outbox**
    *   Instead of publishing directly, the service writes the event to a special **outbox table** in the same database transaction.
    *   A separate **message relay process** polls this table and publishes events to the message broker. This guarantees at-least-once delivery.
*   **Idempotent Consumers:** Because events may be delivered more than once (at-least-once), consumer services must process them **idempotently** (applying the same event multiple times has the same effect as once).

---

#### **Summary / Key Takeaways for Interview:**

*   **Data Sovereignty is Key:** The **"database per service"** rule is the single most important constraint that forces good microservice design and prevents coupling.
*   **Embrace Eventual Consistency:** Cross-service data consistency is managed through **asynchronous events and compensation (Sagas)**, not distributed transactions. This is a fundamental architectural trade-off.
*   **CQRS Solves the Cross-Service Query Problem:** For complex queries spanning services, **CQRS with a separate read store** is the standard pattern, not distributed joins or shared views.
*   **Duplication is Better than Coupling:** **Data duplication is an accepted side effect** of loose coupling. It's better to duplicate reference data than to couple services via a shared database.
*   **Reliability Requires Patterns:** Reliable event publishing requires the **Transactional Outbox** pattern, and event processing requires **idempotent consumers**.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"How do you handle transactions that update multiple microservices?"** → Reject distributed transactions. Explain the **Saga Pattern**: a series of local transactions coordinated by events, with compensating actions for rollback. Emphasize **eventual consistency**.
*   **"What is CQRS and when is it used in microservices?"** → Define it as **separating the Read and Write models**. Use it when: 1) Queries need data from multiple services, 2) Read and write performance/scaling needs are very different, 3) You need complex query capabilities that don't fit the write model's structure.
*   **"How do you perform a query that needs data from three different services?"** → Present the two options: 1) **API Composition** for simpler, real-time queries (call all three and merge), 2) **CQRS** for complex, frequent, or reporting queries (maintain a denormalized read store updated by events from those services).
*   **"Isn't duplicating data across services bad?"** → **No, in microservices it's a necessary trade-off for loose coupling.** Explain that duplicating a small amount of **reference data** is preferable to the high coupling of a shared database. The duplication is managed via events/caching.
*   **"How do you ensure a database update and an event publish happen atomically?"** → Describe the **Transactional Outbox pattern**: write the event to a database table *in the same transaction* as the business data. A separate, reliable process then reads the outbox and publishes to the message broker. This guarantees the event will be published if the transaction commits.
