Different API styles trade off simplicity, flexibility, performance, and connectivity patterns; a senior architect is expected to know when to pick REST, GraphQL, WebSocket, Webhook, RPC/gRPC, or SOAP based on domain, client mix, and non‑functional requirements.[wallarm+1](https://www.wallarm.com/cloud-native-products-101/grpc-vs-rest-api-communication)​

---

## API styles

- REST
    
    - Resource-oriented over HTTP using standard verbs (GET/POST/PUT/DELETE) and status codes.
        
    - Strengths: simplicity, cacheability, browser/native support, great for public and CRUD-style APIs.
        
    - Weaknesses: can be over/under-fetching, chatty for complex graphs, no built-in subscriptions.
        
- GraphQL
    
    - Query language over HTTP with a strongly-typed schema; clients specify exactly what they need in a single endpoint.
        
    - Strengths: solves over/under-fetching, strong typing, great for aggregating multiple backends, fits complex UIs.
        
    - Weaknesses: more complex server/runtime, cache harder than REST, risk of expensive queries without controls.
        
- WebSocket
    
    - Full-duplex, long-lived TCP-based channel between client and server.
        
    - Strengths: low-latency bidirectional messaging, ideal for real-time apps (trading, gaming, collaboration).
        
    - Weaknesses: stateful connections, more complex scaling & load-balancing, no native HTTP semantics or caching.
        
- Webhook
    
    - Server-to-server callbacks over HTTP: provider POSTs events to subscriber URLs.
        
    - Strengths: simple push model, good for async integrations and event notifications.
        
    - Weaknesses: delivery reliability, security of public endpoints, versioning/event-evolution concerns.
        
- RPC/gRPC
    
    - Remote procedure calls (often gRPC over HTTP/2 + Protobuf) treating remote calls like local methods.
        
    - Strengths: high performance, strong contracts, streaming support, good for internal microservices.
        
    - Weaknesses: weaker browser support (need gRPC-Web/gateway), binary debugging friction, less cache-friendly.
        
- SOAP
    
    - XML-based protocol with WSDL contracts, often over HTTP.
        
    - Strengths: strong formal contracts, mature tooling, WS-* (security, transactions) in enterprise environments.
        
    - Weaknesses: verbose XML, heavier stack, less natural fit for modern web/mobile compared to REST/JSON.
        
- Architect-level positioning
    
    - REST/GraphQL are best for external APIs and UIs; WebSocket/Webhook add real-time and push; RPC/gRPC excels in internal service meshes; SOAP persists in some legacy or regulated ecosystems.[dreamfactory+1](https://blog.dreamfactory.com/grpc-vs-rest-how-does-grpc-compare-with-traditional-rest-apis)​
        

---

## Interview checklist – “Can you choose the right style?”

- Can you explain the core idea and typical use case for REST, GraphQL, WebSocket, Webhook, RPC/gRPC, and SOAP in one sentence each?
    
- Can you argue when to choose REST vs GraphQL for a public API (simplicity & caching vs schema-driven flexibility and fewer round-trips)?
    
- Can you identify when HTTP request/response is not enough and you need WebSocket (real-time, bidirectional) or Webhooks (server push, event fan-out)?
    
- Can you position gRPC/RPC as internal, high-performance, strongly-typed microservice communication rather than a public web API?
    
- Can you discuss legacy/enterprise scenarios where SOAP and WS-* still make sense (compliance, vendor ecosystems)?
    
- Can you map each style to key non-functional drivers: performance, latency, caching, schema governance, browser/mobile support, and operational complexity?
    
- Can you describe a hybrid architecture where REST/GraphQL face clients, gRPC connects microservices, and Webhooks/WebSockets handle events and real-time flows?
    

---

## 60‑second recap – “must-say” bullets

- REST: simple, resource-based HTTP APIs, great for public CRUD, caching, and broad client support.
    
- GraphQL: schema-driven single endpoint that lets clients ask exactly for what they need, ideal for complex UIs and aggregating multiple backends.
    
- WebSocket: persistent, bidirectional channel for low-latency real-time messaging when request/response is not enough.
    
- Webhook: provider-initiated HTTP callbacks to consumer endpoints for async events and integrations.
    
- RPC/gRPC: high-performance, strongly-typed remote calls (often over HTTP/2 + Protobuf) optimized for internal microservice-to-microservice communication.
    
- SOAP: XML and WSDL-based protocol with rich WS-* features, still relevant in some legacy and regulated enterprise stacks.
    
- In a modern enterprise, combine them: REST/GraphQL at the edge, gRPC inside, Webhooks for integrations, WebSockets for real time, and SOAP only where the ecosystem demands it.
    

Reference: high-level comparison of REST, GraphQL, WebSocket, Webhook, RPC/gRPC, and SOAP API styles.[learn.microsoft+1](https://learn.microsoft.com/en-us/aspnet/core/grpc/comparison?view=aspnetcore-10.0)​

1. [https://www.wallarm.com/cloud-native-products-101/grpc-vs-rest-api-communication](https://www.wallarm.com/cloud-native-products-101/grpc-vs-rest-api-communication)
2. [https://blog.dreamfactory.com/grpc-vs-rest-how-does-grpc-compare-with-traditional-rest-apis](https://blog.dreamfactory.com/grpc-vs-rest-how-does-grpc-compare-with-traditional-rest-apis)
3. [https://learn.microsoft.com/en-us/aspnet/core/grpc/comparison?view=aspnetcore-10.0](https://learn.microsoft.com/en-us/aspnet/core/grpc/comparison?view=aspnetcore-10.0)
4. [https://hackernoon.com/the-system-design-cheat-sheet-api-styles-rest-graphql-websocket-webhook-rpcgrpc-soap](https://hackernoon.com/the-system-design-cheat-sheet-api-styles-rest-graphql-websocket-webhook-rpcgrpc-soap)


Different API styles serve distinct connectivity, performance, and client needs; senior architects must match REST/GraphQL to external APIs, gRPC to internal services, WebSocket/Webhook to real-time/events, and SOAP to legacy ecosystems.

---

## API Styles – memorization-focused summary (architect view)

|**Style**|**Core pattern + positioning**|
|---|---|
|**REST**|Resource-oriented HTTP (GET/POST/PUT/DELETE), cacheable, universal browser support. **Public CRUD APIs, external clients.**|
|**GraphQL**|Schema-driven single endpoint, client specifies exact fields. **Complex UIs, aggregate multiple backends, avoid over/under-fetching.**|
|**WebSocket**|Persistent full-duplex TCP channel. **Real-time bidirectional (chat, trading, gaming, live updates).**|
|**Webhook**|Provider → consumer HTTP callbacks. **Async events, server-push notifications, integrations.**|
|**RPC/gRPC**|Remote procedure calls (HTTP/2 + Protobuf). **High-perf internal microservices, streaming, polyglot teams.**|
|**SOAP**|XML + WSDL contracts, WS-* standards. **Legacy enterprise, regulated industries, formal contracts.**|

---

## Interview checklist – API style selection

- Can you define each style in one phrase and give its primary use case?
    
- REST vs GraphQL: when simplicity/caching beats schema flexibility (and vice versa)?
    
- When does request/response fail and you need WebSocket (bidirectional real-time)?
    
- When do you need server-push and choose Webhook over polling?
    
- gRPC positioning: internal services vs public APIs (perf + contracts vs browser reach)?
    
- SOAP survival: where formal contracts + WS-Security still matter?
    
- Hybrid strategy: REST/GraphQL → edge, gRPC → service mesh, Webhook/WebSocket → events/real-time?
    
- NFR mapping: performance, caching, browser support, schema governance, operational complexity?
    

---

## 60-second recap – "must-say" bullets

- **REST**: resource HTTP APIs → public CRUD, caching, universal support
    
- **GraphQL**: schema queries → complex UIs, precise data fetching, backend aggregation
    
- **WebSocket**: persistent channels → real-time bidirectional (chat, live data)
    
- **Webhook**: HTTP callbacks → async events, server-push notifications
    
- **gRPC/RPC**: binary RPC → internal microservices, streaming, high throughput
    
- **SOAP**: XML contracts → legacy enterprise, WS-* compliance
    
- **Architect rule**: REST/GraphQL for clients, gRPC for services, WebSocket/Webhook for real-time/events
    

Reference: HackerNoon System Design Cheat Sheet - API Styles comparison [https://hackernoon.com/the-system-design-cheat-sheet-api-styles-rest-graphql-websocket-webhook-rpcgrpc-soap](https://hackernoon.com/the-system-design-cheat-sheet-api-styles-rest-graphql-websocket-webhook-rpcgrpc-soap)