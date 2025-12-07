
**Source:** Microsoft Azure Architecture Center | [API Design](https://learn.microsoft.com/en-us/azure/architecture/microservices/design/api-design)

**Main Idea:** In microservices, APIs are the **contracts that define service boundaries**. Good API design is critical for achieving loose coupling, enabling independent evolution, and ensuring system stability. Design must focus on the **consumer's needs**, anticipate change, and prioritize clarity and consistency.

---

#### **Notes: Core Principles & Patterns**

**1. API as a First-Class Product**
*   **Mindset Shift:** Treat each service's API as an **external-facing product**, even if initially consumed internally. Invest in its design, documentation, and stability.
*   **Consumer-Driven:** Design for the **client's use case**, not the internal data model. Ask: "What does the consumer need to accomplish?"

**2. Fundamental Design Principles**
*   **Consistency:** Adopt and enforce **organization-wide standards** for URL structure, error handling, pagination, filtering, and versioning. Use **API style guides**.
*   **Simplicity & Intuitiveness:** APIs should be self-describing and follow conventions (e.g., RESTful principles). Avoid over-engineering.
*   **Stability & Backward Compatibility:** **Never break existing consumers.** Changes must be additive or introduce new versions. Backward compatibility is paramount.
*   **Performance & Efficiency:**
    *   Design **coarse-grained APIs** to minimize chatty calls.
    *   Support **partial responses** (field selection) and **pagination** for large datasets.
    *   Consider **response caching** (HTTP caching headers) where appropriate.

**3. RESTful API Design Guidelines**
*   **Resources & URIs:**
    *   Model APIs around **resources** (nouns), not actions (verbs). (e.g., `/orders`, not `/getOrders`).
    *   Use **plural nouns** for collections (`/customers`).
    *   Use **HTTP methods** semantically: GET (retrieve), POST (create), PUT (replace), PATCH (partial update), DELETE.
    *   Use **sub-resources** for relationships (`/orders/{id}/items`).
*   **HTTP Status Codes:** Use standard codes correctly (200 OK, 201 Created, 400 Bad Request, 404 Not Found, 409 Conflict, 429 Too Many Requests, 5xx for server errors).
*   **Error Handling:** Return **consistent, structured error responses** with machine-readable codes, human-readable messages, and details.
    ```json
    {
      "error": {
        "code": "InvalidOrderAmount",
        "message": "Order amount must be positive.",
        "target": "amount"
      }
    }
    ```

**4. Advanced Patterns for Microservices**
*   **API Composition (Backend for Frontend - BFF):** Create **dedicated APIs** tailored to specific client needs (e.g., a mobile BFF, a web BFF). These sit between the client and multiple backend services, aggregating data and reducing network chattiness.
*   **API Gateway:** Use **Azure API Management** as the single entry point to:
    *   **Aggregate** multiple downstream service calls (composition).
    *   **Transform** requests/responses.
    *   **Offload** cross-cutting concerns: authentication, rate limiting, caching, logging.
*   **HATEOAS (Hypermedia as the Engine of Application State):** Include **links** in API responses to indicate possible related actions. Promotes discoverability and decouples clients from URI structures (e.g., `"links": [ { "rel": "self", "href": "/orders/123" } ]`).

**5. Versioning & Evolution Strategies**
*   **Plan for Change:** Assume the API will evolve. Versioning is essential.
*   **Versioning Approaches:**
    *   **URL Path:** Most explicit (e.g., `/v1/orders`, `/v2/orders`). Easy for routing.
    *   **Query String:** (e.g., `/orders?api-version=1.0`). Less intrusive.
    *   **HTTP Header:** (e.g., `api-version: 1.0`). Keeps URLs clean.
*   **Deprecation Policy:** Have a clear, communicated policy for retiring old versions (e.g., 6-month deprecation notice).

**6. Tooling & Documentation**
*   **API Specifications:** Use **OpenAPI (Swagger)** to define contracts in a machine-readable format. Store spec files in the service's repository.
*   **Azure API Management:** Use to import OpenAPI specs, publish APIs, generate documentation, and create a developer portal.
*   **Consumer-Driven Contract Testing:** Use tools like **Pact** to ensure API implementations match consumer expectations.

---

#### **Summary / Key Takeaways for Interview:**

*   **Contract-First Design:** APIs define the **boundaries** between services. Design them intentionally and treat them as long-term contracts.
*   **Consumer is King:** API design should be driven by **consumer use cases**, not internal implementation details. The BFF pattern exemplifies this.
*   **Stability Through Standards:** Consistency in error handling, versioning, and structure **reduces integration cost and cognitive load** for client developers.
*   **Gateway as a Strategic Enabler:** **Azure API Management** is not just a router; it's a platform for **API composition, policy enforcement, and abstraction**, crucial for managing a microservices ecosystem.
*   **Evolution, Not Revolution:** Design APIs to be **extended gracefully** through versioning and additive changes. Breaking changes should be a last resort.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"What are the most important principles for designing microservices APIs?"** → Emphasize: 1) **Consumer-centric design**, 2) **Consistency** via standards, 3) **Stability & backward compatibility**, 4) **Coarse granularity** to avoid chattiness.
*   **"How do you handle breaking changes to an API?"** → Outline the process: 1) **Version the API** (new `/v2` endpoint), 2) **Maintain the old version** during a communicated overlap period, 3) Use **analytics** (in API Management) to identify active consumers, 4) **Deprecate** the old version with clear timelines, 5) **Remove** it only after migration.
*   **"What is the Backend for Frontend (BFF) pattern and why use it?"** → It's a **purpose-built API layer** for a specific client type (e.g., mobile app, web app). It **aggregates data** from multiple microservices, **transforms** it to the client's exact needs, and **reduces network round trips**. It prevents the "one-size-fits-all" API problem.
*   **"What is the role of Azure API Management in microservices?"** → It serves as the **strategic facade and control plane**: **Security gateway** (authN/Z), **Traffic manager** (routing, composition), **Policy enforcer** (rate limits, quotas), **Developer portal** (documentation, onboarding), and **Analytics hub** for API usage insights.
*   **"How do you ensure API quality and consistency across many teams?"** → **Centralized governance** through: 1) A **shared API style guide**, 2) **Automated linting/review** of OpenAPI specs in CI/CD, 3) **API Gateway** (APIM) as the enforcement point for policies, 4) **Contract testing** (Pact) to prevent breaking changes.