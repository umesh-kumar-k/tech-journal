### **Domain Analysis for Microservices **

**Source:** Microsoft Azure Architecture Center | [Domain Analysis](https://learn.microsoft.com/en-us/azure/architecture/microservices/model/domain-analysis)

**Main Idea:** Before any technical design, successful microservices architecture **must begin with a deep understanding of the business domain**. Domain analysis is the foundational process of decomposing a complex business into subdomains and bounded contexts, which directly inform stable, business-aligned service boundaries.

---

#### **Notes: Core Concepts & Process**

**1. The "Why": Starting with the Domain**
*   **Problem:** Defining service boundaries based on technical layers (e.g., "API service," "data service") leads to **tightly coupled, fragile systems**.
*   **Solution:** Use **Domain-Driven Design (DDD)** principles to align services with the **business's own structure and language**.
*   **Goal:** Create a **domain model** that represents the business reality, which will be mirrored by the software architecture.

**2. Key DDD Concepts for Domain Analysis**
*   **Domain:** The specific business problem or subject area the application addresses (e.g., "E-commerce," "Insurance Underwriting").
*   **Subdomain:** A distinct, separable part of the overall domain.
    *   **Core Domain:** The unique, competitive differentiator of the business (e.g., "Recommendation Engine"). Invest most design effort here.
    *   **Supporting Subdomain:** Supports the core domain but is not unique (e.g., "Customer Notification Service").
    *   **Generic Subdomain:** Solved problems with standard solutions (e.g., "Authentication," "Payment Processing"). Consider buying over building.
*   **Bounded Context:** The **crucial pattern**. A boundary within which a particular **domain model** (definitions, rules, language) is consistent and applies. This is the **primary candidate for a microservice boundary**.
*   **Ubiquitous Language:** A shared, rigorous language between developers and domain experts, used consistently within a bounded context.

**3. The Domain Analysis Process**
*   **Step 1: Understand the Business Landscape**
    *   Engage **domain experts** (business stakeholders, subject matter experts).
    *   Identify **business capabilities** (what the business does) and **business processes** (how it does it).
*   **Step 2: Decompose into Subdomains**
    *   Break the large domain into smaller, cohesive subdomains (Core, Supporting, Generic).
    *   Use techniques like **event storming** or **process modeling** to discover these.
*   **Step 3: Define Bounded Contexts**
    *   For each subdomain, define the **boundary** where a particular model and ubiquitous language apply.
    *   **Key Heuristic:** Look for **conceptual discontinuities**—places where a term changes meaning or different business rules apply. These are boundaries.
*   **Step 4: Map Context Relationships**
    *   Define how bounded contexts interact. Common patterns:
        *   **Partnership:** Two contexts closely collaborate; their development cycles are coupled.
        *   **Shared Kernel:** A small, shared subset of the model (use cautiously).
        *   **Customer-Supplier (Upstream/Downstream):** One (upstream) defines the contract; the other (downstream) consumes it.
        *   **Conformist:** Downstream conforms to the upstream's model without modification.
        *   **Anticorruption Layer (ACL):** A downstream context creates an adapter to protect its model from changes in an upstream context.
        *   **Open Host Service / Published Language:** Upstream publishes a standardized protocol for many consumers.

**4. Output: The Strategic Design**
*   **A Bounded Context Map:** A diagram showing the identified bounded contexts and their relationships (partnership, customer-supplier, etc.).
*   **Clear Service Boundaries:** Each bounded context becomes a strong candidate for an **autonomous microservice**.
*   **Communication Patterns:** The context map informs integration patterns: synchronized APIs for close contexts, asynchronous events for decoupled ones.

**5. Impact on Microservice Design**
*   **Service Autonomy:** A bounded context's independence enables a microservice's independent development and deployment.
*   **Data Ownership:** The "database per service" rule is a direct consequence. Each bounded context owns and encapsulates its data model.
*   **Team Structure:** **Conway's Law in action.** Each bounded context/microservice can be owned by a dedicated, cross-functional team.

---

#### **Summary / Key Takeaways for Interview:**

*   **Business First, Technology Second:** The most critical microservices design work is **non-technical**. It's the business domain analysis that determines long-term architectural stability.
*   **Bounded Context = Microservice Boundary:** This is the **golden rule**. If you get the bounded contexts right, the microservice boundaries follow naturally and remain stable as the business evolves.
*   **Not All Subdomains Are Equal:** The **Core Domain** deserves the most sophisticated, custom-built microservices. **Generic Subdomains** should often be implemented via third-party SaaS or standard components.
*   **Relationships Matter as Much as Boundaries:** Defining **how contexts interact** (ACL, Published Language) is as important as defining the contexts themselves. This directly translates to your integration architecture (APIs vs. events).
*   **Prevents Distributed Monolith:** Skipping this step is the #1 reason teams end up with a **distributed monolith**—technically separated services that are tightly coupled through incorrect boundaries.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"How do you determine where to split a monolith into microservices?"** → Answer: **Through domain analysis and DDD.** You identify **bounded contexts** within the business domain, and these become your service boundaries. Do not split by technical layers.
*   **"What is a Bounded Context?"** → Define it as a **semantic boundary** where a specific domain model with its own **ubiquitous language** applies. It's a cohesive unit of business functionality, making it the perfect microservice candidate.
*   **"What's the difference between a Subdomain and a Bounded Context?"** → **Subdomain** is a **problem space** (a division of the business). **Bounded Context** is a **solution space** (a boundary for a specific model that addresses that subdomain). They often align 1:1, but not always.
*   **"How do bounded contexts communicate?"** → Explain the relationship patterns from the context map: **Synchronous APIs** for close relationships (with caution), **asynchronous events/messaging** for decoupled ones, and **Anticorruption Layers** to protect downstream models.
*   **"What is an Anti-Corruption Layer (ACL) and when is it used?"** → Describe it as an **adapter or façade microservice** that translates between two different domain models. It's used when a downstream service needs to integrate with an upstream service (often legacy or external) but must protect its own clean domain model from the upstream's complexity or changes.


### **Tactical Domain-Driven Design for Microservices**

**Source:** Microsoft Azure Architecture Center | [Tactical DDD](https://learn.microsoft.com/en-us/azure/architecture/microservices/model/tactical-ddd)

**Main Idea:** **Tactical DDD provides the concrete building blocks** to implement the domain model within a single **Bounded Context** (and thus, within a single microservice). It focuses on creating a rich, expressive object-oriented model that captures business logic, rules, and behavior, ensuring the code reflects the domain discovered during strategic analysis.

---

#### **Notes: Core Building Blocks & Patterns**

**1. The Purpose of Tactical Patterns**
*   **Context:** Applied **within** a single Bounded Context after strategic DDD defines the boundaries.
*   **Goal:** To create a **domain model** that encapsulates complex business logic, ensuring maintainability and alignment with business requirements. It prevents the service from becoming an **anemic domain model** (mere data containers with logic in separate services).

**2. Fundamental Building Blocks**
*   **Entity:** An object defined by a **thread of continuity and identity** (e.g., `Customer` with a unique `CustomerId`). Its attributes may change, but its identity persists.
    *   Contains business logic related to its own state transitions.
*   **Value Object:** An object with **no conceptual identity**, defined solely by its attributes (e.g., `Money` with amount and currency, `Address`). They are **immutable**.
    *   Used for modeling descriptive aspects of the domain.
*   **Aggregate:** **The most important pattern for microservices.**
    *   A **cluster of associated objects (Entities and VOs) treated as a single unit** for data changes.
    *   Has a **root entity (Aggregate Root)** that is the sole entry point for external interactions. The root enforces **invariants** (consistency rules) within the aggregate boundary.
    *   **Critical for Microservices:** An aggregate's boundary often defines a **transaction boundary** and a natural **consistency boundary** within a service.
*   **Domain Event:** An object that records something significant that happened in the domain (e.g., `OrderPlaced`, `PaymentProcessed`).
    *   Used to communicate changes **within** the bounded context or to **other bounded contexts** (asynchronously) to trigger side effects.

**3. Key Design Principles for Microservices**
*   **Aggregate as a Transaction Boundary:** All modifications to objects inside an aggregate must be performed **through the Aggregate Root**, ensuring the entire aggregate remains in a consistent state. This aligns with the microservices principle of **database-per-service**—the aggregate is the unit of persistence and consistency.
*   **Reference Other Aggregates by Identity, Not Object:** Aggregates should not hold direct object references to other aggregates. Instead, they hold the **ID** of the other aggregate's root. This enforces loose coupling between aggregates, which may even be in different microservices.
*   **Domain Events for Inter-Aggregate/Inter-Service Communication:** When an action in one aggregate affects another, publish a **Domain Event**. This enables eventual consistency and decoupling.

**4. Implementation Example & Code Structure**
*   **Domain Layer:** Contains the core business logic—Entities, Value Objects, Aggregates, Domain Events, and **Domain Services** (for logic that doesn't naturally fit inside an entity).
*   **Application Layer:** Thin layer that orchestrates use cases. It receives requests, fetches aggregates from the **Repository**, executes domain logic, and persists changes. It also publishes Domain Events.
*   **Repository Pattern:** Abstraction for persistence, providing **collection-like interfaces** for retrieving and storing aggregates. Hides database complexity from the domain layer.
*   **Typical Flow:** Controller → Application Service → Repository (gets Aggregate) → Aggregate executes business logic → Repository (saves) → Application Service publishes Domain Events.

**5. Connection to Microservices Architecture**
*   **Bounded Context ≈ Microservice:** The tactical patterns are applied *within* one microservice.
*   **Aggregate ≈ Consistency Boundary:** Within a service, aggregates define where ACID transactions are applied. Between services, use **Sagas** with Domain Events.
*   **Domain Events ≈ Integration Mechanism:** Events published by one microservice (from within its domain) become the primary way to communicate changes to other microservices asynchronously.

---

#### **Summary / Key Takeaways for Interview:**

*   **From Strategy to Code:** Tactical DDD is the **implementation bridge** between the high-level bounded context (strategic) and the actual code of a microservice. It ensures the internal design is as robust as the boundary definition.
*   **Aggregate is the Star Pattern:** For microservices, the **Aggregate** is the most critical tactical pattern. It defines **transactional boundaries, consistency guarantees, and the unit of persistence** within a service's private database.
*   **Fights the "Anemic Domain Model":** Tactical DDD insists on **behavior-rich objects**, preventing the common anti-pattern where microservices become mere "CRUD wrappers" around databases, with logic scattered in application services.
*   **Enables Clean Asynchronous Communication:** **Domain Events** generated from within the domain model become the natural source for reliable, asynchronous communication between microservices, driven by business meaning, not technical necessity.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"How do you organize the internal code of a microservice?"** → Describe applying **Tactical DDD patterns**: a **Domain Layer** with **Aggregates** (Root Entities + VOs), an **Application Layer** for orchestration, and the **Repository pattern** for persistence. Emphasize the **rich domain model**.
*   **"What is an Aggregate and why is it important?"** → Define it as a **cluster of related objects treated as one unit** with a root. Stress its importance as the **consistency and transactional boundary within a microservice**, crucial for maintaining data integrity without distributed transactions.
*   **"How do microservices within a bounded context communicate?"** → Clarify: **If within the *same* bounded context/microservice**, they are part of the same domain model (objects interact directly). **If in *different* bounded contexts/microservices**, they communicate via **published Domain Events** (asynchronous) or APIs.
*   **"What's the role of a Domain Event?"** → Two roles: 1) **Within a service:** Notify other parts of the same model about a change. 2) **Between services:** The primary integration mechanism. A domain event from one service is published (e.g., to a message bus) for other services to react to.
*   **"How does this relate to database design?"** → The **Aggregate** is often the **unit of serialization to the database**. The repository fetches and persists whole aggregates. This aligns with the "database-per-service" rule, as the schema is private and optimized for that aggregate's access patterns.

### **Identifying Microservice Boundaries**

**Source:** Microsoft Azure Architecture Center | [Microservice Boundaries](https://learn.microsoft.com/en-us/azure/architecture/microservices/model/microservice-boundaries)

**Main Idea:** Defining correct service boundaries is the most critical and difficult design task in microservices architecture. Boundaries should be derived from **business capabilities** and **domain-driven design** principles, not technical concerns, to create stable, loosely coupled, and independently evolvable services.

---

#### **Notes: Core Principles & Heuristics**

**1. Foundational Rule: Business Capabilities First**
*   **Primary Driver:** Align services with **business capabilities** (what the business *does*) and **bounded contexts** (cohesive areas of the domain with their own language and rules).
*   **Anti-Pattern to Avoid:** **Technical Partitioning** (e.g., "API layer," "data access layer," "logic service"). This creates **distributed monoliths** with high coupling.

**2. The Role of Domain-Driven Design (DDD)**
*   **Strategic DDD:** Use **bounded contexts** as the primary boundary candidates. A bounded context's natural isolation makes it an ideal microservice.
*   **Tactical DDD:** Within a bounded context, use **aggregates** as sub-boundaries for consistency and transactions, but keep them **inside a single service**. Don't split an aggregate across services.
*   **Process:** Conduct **event storming** with domain experts to discover domain events, commands, and aggregates, which reveal natural boundaries.

**3. Key Heuristics for Defining Boundaries**
*   **Single Responsibility Principle (applied to services):** A service should have one reason to change—related to a specific business capability.
*   **Common Closure Principle:** Things that change together should be packaged together. If two functions are always modified for the same business reason, they likely belong in the same service.
*   **Loose Coupling:** Aim for minimal dependencies between services. Evaluate potential boundaries by asking: "Can I change this service without impacting that one?"
*   **High Functional Cohesion:** Related behavior and data should live together. If two operations frequently need the same data, they should likely be in the same service.

**4. Practical Considerations & Trade-offs**
*   **Team Structure (Conway's Law):** Design boundaries that align with your **team organization**. A service should be owned by a single, small, cross-functional team.
*   **Granularity Spectrum:** Balance between **too coarse** (becomes a mini-monolith) and **too fine** (excessive network overhead, management complexity).
    *   **Start Slightly Coarser:** It's easier to split a service later than to merge overly fine-grained services.
*   **Data Ownership & Transactions:**
    *   **Database per Service:** Each service owns its private data store. This **forces** clear boundaries.
    *   **Distributed Transactions are an Anti-Pattern:** Avoid them. Use the **Saga pattern** with eventual consistency for operations spanning services.
*   **Communication Patterns Influence Boundaries:**
    *   **Chatty Communication** between two components suggests they might be one service.
    *   **Asynchronous, event-based communication** supports looser coupling and clearer boundaries.

**5. Iterative Refinement & Evolving Boundaries**
*   **Boundaries are Not Static:** As business understanding evolves, boundaries may need adjustment.
*   **Refactoring is Expected:** Splitting or merging services is a normal part of evolution. Design for this by minimizing coupling.
*   **Warning Signs of Poor Boundaries:**
    *   **Changes require modifying multiple services.**
    *   **Services need to know about each other's internal data models.**
    *   **Synchronous, chatty communication between services.**
    *   **Difficulty deploying services independently.**

**6. Common Boundary Patterns**
*   **Decomposition by Business Capability:** (e.g., Shipping, Inventory, Order Management)
*   **Decomposition by Subdomain:** (Core, Supporting, Generic from DDD)
*   **Strangler Fig Pattern:** For incremental migration from monolith, gradually extract services around new capabilities or distinct subdomains.

---

#### **Summary / Key Takeaways for Interview:**

*   **Business over Technology:** The single most important principle: **Business capabilities and bounded contexts dictate boundaries.** Technical convenience leads to failure.
*   **DDD as the Primary Toolset:** Both **Strategic DDD** (for finding bounded contexts) and **Tactical DDD** (for designing aggregates within them) are essential frameworks for boundary design.
*   **The Team is a First-Class Design Input:** **Conway's Law** is unavoidable. Design services that can be owned by **autonomous teams**—this often defines the practical upper size limit for a service.
*   **Data Ownership is the Ultimate Enforcer:** The rule that a service **owns its private database** is what makes boundaries real and exposes poor coupling decisions.
*   **Accept Evolution:** Getting boundaries perfect on the first try is impossible. Design for **modularity** and **low coupling** to make boundary refactoring feasible.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"How do you decide where to draw lines between microservices?"** → Outline the process: 1) Use **DDD/Event Storming** with business experts to find **bounded contexts**, 2) Apply heuristics like **Single Responsibility** and **Common Closure**, 3) Consider **team structure** (Conway's Law), 4) Validate by ensuring each service can have its **own database**.
*   **"What's the biggest mistake in defining boundaries?"** → **Splitting by technical layer** (UI, API, logic, data). This creates a distributed monolith with all the complexity of microservices and none of the benefits.
*   **"How does team size affect service boundaries?"** → A service should be **small enough to be built and maintained by a single, small team (5-9 people)**. If a service is too large for one team, its boundaries are likely wrong.
*   **"What heuristic indicates two components should be one service?"** → If they require **strong consistency (ACID transactions)**, share and frequently access the **same data**, or change together for the **same business reasons**, they are likely part of the same bounded context and should be one service.
*   **"How do you handle changes to service boundaries later?"** → Acknowledge it's normal. Use patterns like the **Strangler Fig** for extraction. Emphasize that **loose coupling** and **clear APIs/events** from the start make refactoring boundaries much less painful.