REST API best practices from this article focus on resource-centric design, correct use of HTTP, scalability, evolvability, and operability for long-lived, multitenant systems.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​

---

## REST API

|**Topic / Question column**|**Notes column (detailed points)**|
|---|---|
|What is a RESTful web API?|RESTful APIs use HTTP to expose resources via URIs, return representations (usually JSON/XML), and rely on stateless, loosely coupled interactions with hypermedia links and standard status codes.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Key principles: platform independence, loose coupling, standard protocols, and agreed data formats.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​|
|Core REST concepts|Resources are domain entities exposed by stable URIs, not RPC-style operations; each URI uniquely identifies a resource.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Resource representations are transport formats (JSON, XML) decoupled from internal models; clients operate on representations, not database structures.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Uniform interface is achieved by consistent use of HTTP methods (GET, POST, PUT, PATCH, DELETE) and status codes.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Statelessness means no server-side session per client; each request is self-contained, which aids scalability but pushes state into resources and storage.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Hypermedia links (HATEOAS) embed navigable links and allowed operations into responses so clients can discover workflows at runtime.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​|
|Designing resource URIs|Model the business, not implementation: URIs represent business entities & collections (customers, orders), not actions.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Use nouns, not verbs: `/orders` not `/create-order`; HTTP methods convey the verb semantics.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Use plural nouns for collections and hierarchical paths for items, e.g. `/customers` and `/customers/{id}`.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Model relationships simply: use short chains like `/customers/{id}/orders` and `/orders/{id}/customer`; avoid deep nesting beyond collection/item/collection.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Avoid chatty APIs with too many tiny resources; consider denormalizing response shapes to reduce round‑trips while balancing payload size.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Do not mirror database tables directly; hide internal schemas behind an abstraction layer to reduce leakage and allow schema evolution.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​|
|Handling non-resource operations|Some operations (e.g., calculator, reports) don’t map cleanly to resources; expose them as function-like URIs with query parameters.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Use such RPC-like endpoints sparingly; favor resource-based design whenever possible.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​|
|Correct use of HTTP methods|GET retrieves representations and must be safe and idempotent; return 200 when found, 204 for no content, 404 when missing.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ POST is for creating new resources under a collection or submitting data for processing; server assigns the URI; typical statuses: 201 (Created), 200 (processed), 204 (no content), 400/405 for misuse.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ PUT is full replacement (or sometimes creation) of a specific resource, must be idempotent; usually used for item URIs, not collections; statuses 200/204 on update, 201 on create, 409 on conflict.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ PATCH performs partial updates using patch documents (JSON Merge Patch or JSON Patch); statuses 200, 400, 409, 415 depending on validity and media type.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ DELETE removes resources by URI and should return 204 if deleted or 404 if not found.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​|
|Representations & content negotiation|Use standard media types (JSON as `application/json`, optionally XML) with `Content-Type` for request/response format.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Honor `Accept` headers for content negotiation; return 406 if none of the requested types are supported or fall back to a documented default.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Return 415 when a provided content type isn’t supported.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​|
|Async operations|For long-running POST/PUT/PATCH/DELETE, use asynchronous patterns: respond with 202 (Accepted) and a status URI in `Location` header.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Provide a status endpoint that returns progress, optional ETA, and cancellation links; when done and a resource is created, use 303 (See Other) pointing to the resource location.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​|
|Pagination, filtering, sorting, projections|Implement pagination with `limit` and `offset` query parameters, with sensible defaults and upper bounds to protect against abuse (e.g., max limit).[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Support filtering via query parameters (e.g., `minCost`, `status`) to let clients narrow data sets.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Offer sorting with a `sort` parameter (e.g., `sort=price`), but understand that query-string-based sorting can reduce cache hit rates because it changes resource identifiers.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Allow field selection (`fields=id,name`) to enable client-defined projections; validate requested fields to avoid exposing sensitive or unsupported data.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​|
|Partial responses & large binaries|For large binary resources (images, files), support partial GET via `Accept-Ranges` and byte `Range` headers, returning 206 (Partial Content) and `Content-Range` details.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Provide HEAD endpoints so clients can inspect size and headers before fetching and to decide whether to use partial retrieval.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​|
|HATEOAS in practice|Include a `links` collection in representations containing relation type (`rel`), URI (`href`), HTTP action, and supported media types.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Use HATEOAS links for both related resources (e.g., customer from order) and self links, and vary links by resource state to model a finite-state machine.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​|
|Versioning strategies|APIs evolve; versioning is needed when changes are breaking and clients cannot easily ignore them.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ No versioning can work for internal APIs with additive-only changes that clients ignore if unknown; use new resources or links for major additions.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ URI versioning (`/v2/customers/3`) is simple and cache-friendly but pollutes URIs and complicates HATEOAS and routing as versions multiply.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Query-string versioning (`/customers/3?version=2`) keeps URIs stable but shifts complexity into request handling and can affect caching in old proxies.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Header-based versioning uses custom headers (e.g., `Custom-Header: api-version=2`); keeps URIs clean but complicates caching and link representation; requires clients to manage headers.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Media-type versioning encodes version in `Accept`/`Content-Type` (e.g., `application/vnd.contoso.v1+json`); works well with HATEOAS but adds complexity and cache duplication.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​|
|Multitenant API design|Multitenant APIs serve multiple organizations; design must clarify how tenant is identified (subdomains, paths, headers, or tokens) and how isolation is provided.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Subdomain/domain-based tenancy (e.g., `adventureworks.api.contoso.com`) relies on DNS (A/CNAME), reverse proxies, and preserving host headers; helps with branding and data residency routing.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Header- or token-based tenancy (`X-Tenant-ID`, `Host`, JWT claims) requires L7 gateways and increases compute overhead but centralizes auth and keeps URIs clean; can complicate caching and risk data leakage if caches ignore headers.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Path-based tenancy (e.g., `/tenants/{tenantId}/orders`) makes routing straightforward but pollutes the REST resource hierarchy and can complicate patterns and canonicalization.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​|
|Distributed tracing & observability|Use correlation/trace IDs (`Correlation-ID`, `X-Request-ID`, `X-Trace-ID`) in requests and propagate them downstream for end‑to‑end tracing.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Echo correlation IDs in responses; design APIs to integrate smoothly with tracing systems to diagnose latency, failures, and cross-service flows.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​|
|Web API maturity model (RMM)|Level 0: single URI with POST for everything (RPC/SOAP-like, non-RESTful).[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Level 1: multiple URIs representing resources but not using HTTP methods semantically.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Level 2: proper use of HTTP methods, status codes, and resource URIs (where many APIs sit in practice).[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Level 3: HATEOAS – hypermedia-driven navigation with links; closest to REST’s original definition.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​|
|OpenAPI / contract-first|OpenAPI (formerly Swagger) standardizes REST API description; brings opinionated design guidelines and tooling ecosystem.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​ Encourages contract-first design: define API schema, operations, and models first, then implement; enables automated client generation and documentation.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​|

---

## Interview checklist – REST API best practices

Use this as a quick self-review before or during a senior architect interview.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​

- Confirm resource-centric modeling
    
    - Have you modeled APIs around business resources and relationships, not operations or database tables?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
    - Are URIs stable, intuitive, noun-based and pluralized for collections (e.g., `/customers`, `/customers/{id}`)?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
- Validate URI and relationship design
    
    - Are URI depths limited (no more complex than collection/item/collection) and free from deep hierarchical chains?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
    - Do you avoid chatty designs with many tiny resources in favor of well-balanced aggregates?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
- Enforce correct HTTP semantics
    
    - Are GET, POST, PUT, PATCH, DELETE used consistently with REST and HTTP specs, including idempotency guarantees where required (e.g., PUT)?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
    - Are responses using appropriate status codes (2xx/4xx/5xx) and headers (Location, Content-Type, Accept, etc.) for each operation?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
- Design for statelessness and scalability
    
    - Is the API stateless, with all client context carried in each request and state persisted in resources or storage, not in server sessions?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
    - Have you considered how statelessness interacts with data store partitioning and scalability strategies?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
- Handle long-running operations correctly
    
    - Do long-running writes use async patterns with 202 and a status endpoint, possibly returning 303 to the resulting resource when complete?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
- Optimize data transfer and performance
    
    - Is pagination implemented with sane defaults and upper bounds (limit/offset) to avoid huge responses and DOS vectors?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
    - Are filtering, sorting, and field selection supported where useful, with validation to protect sensitive fields and cache behavior considered?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
- Manage large and binary payloads
    
    - Do you support partial content (206, `Accept-Ranges`, `Range`/`Content-Range`) and HEAD for large binary resources?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
- Implement HATEOAS where appropriate
    
    - Do responses include hypermedia links that describe next possible actions and related resources, especially in workflows?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
    - Are link relations, HTTP actions, and media types clearly represented for clients?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
- Plan and standardize versioning
    
    - Is there a documented versioning strategy (URI, query, header, or media type) with clear criteria for when to introduce new versions?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
    - Have you evaluated the impact of versioning approach on caching, HATEOAS, and long-term maintainability?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
- Support multitenancy where needed
    
    - Is tenant identification strategy (subdomain, path, header, JWT) clearly defined, consistent, and aligned with security and routing requirements?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
    - Have you addressed tenant isolation, caching behavior, and data residency/compliance needs?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
- Ensure observability and traceability
    
    - Are correlation/trace IDs supported end‑to‑end and logged across services?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
    - Do APIs integrate with monitoring, logging, and distributed tracing platforms for debugging and SLOs?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
- Embrace contract-first and tooling
    
    - Is OpenAPI used to define contracts, generate documentation, and optionally clients and server stubs?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        
    - Are style guides and API governance (linting, review process) in place to keep APIs consistent across teams?[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
        

---

## 60‑second recap (bullet points)

Use this as a quick verbal summary just before the interview.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​

- Design around business resources and relationships, not RPC operations or database tables; keep URIs noun-based, plural, and shallow.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
    
- Use HTTP methods and status codes correctly: GET safe/idempotent reads; POST for creation/processing; PUT idempotent full replacement; PATCH partial update; DELETE removal.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
    
- Keep APIs stateless and scalable: each request is self-contained, resource state lives in storage, and representations are decoupled from internal models.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
    
- Optimize responses via pagination, filtering, sorting, field selection, and partial responses for large binaries; guard against chatty APIs and huge payloads.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
    
- Use hypermedia links (HATEOAS) to expose valid next actions and related resources, especially in long-lived, evolving workflows.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
    
- Handle long-running operations asynchronously with 202 + status endpoints and 303 redirects to created resources when complete.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
    
- Choose and document a versioning strategy (URI, query, header, or media-type) with clear guidelines, and understand caching and HATEOAS implications.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
    
- Design explicitly for multitenancy and observability: clear tenant identification, isolation model, correlation IDs, and integration with tracing and logging.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
    
- Standardize via OpenAPI and contract-first design, using tooling to enforce consistent, consumer-friendly API contracts across services.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)​
    

Reference: Microsoft Learn – “Best practices for RESTful web API design” ([https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design).[1](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design\).%5B1)]

1. [https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design](https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design)