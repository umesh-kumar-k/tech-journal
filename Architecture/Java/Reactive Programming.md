# **Java Reactive Programming Summary**

## **Article 1: Project Reactor Reference Guide**
**Source:** https://projectreactor.io/docs/core/release/reference/reactiveProgramming.html

### **1. Reactive Programming Core Principles**
- **Paradigm Shift:** From imperative to declarative, asynchronous data streams
- **Reactive Streams Specification:** Standard for async stream processing with backpressure
  - **Publisher:** Produces data
  - **Subscriber:** Consumes data
  - **Subscription:** Links Publisher to Subscriber
  - **Processor:** Both Publisher and Subscriber
- **Non-blocking:** Threads don't wait for I/O, better resource utilization

### **2. Reactive Manifesto Principles**
1. **Responsive:** System responds in timely manner
2. **Resilient:** Stays responsive during failures
3. **Elastic:** Stays responsive under varying load
4. **Message-Driven:** Async message passing between components

### **3. Project Reactor Core Types**
- **Mono:** 0-1 element publisher
  ```java
  Mono<String> mono = Mono.just("Hello");
  Mono<String> empty = Mono.empty();
  Mono<String> error = Mono.error(new RuntimeException());
  ```
- **Flux:** 0-N elements publisher
  ```java
  Flux<Integer> flux = Flux.range(1, 10);
  Flux<String> fromIterable = Flux.fromIterable(list);
  Flux<Long> interval = Flux.interval(Duration.ofSeconds(1));
  ```

### **4. Key Operators**
- **Transformation:** `map()`, `flatMap()`, `concatMap()`
- **Filtering:** `filter()`, `distinct()`, `take()`, `skip()`
- **Combining:** `merge()`, `concat()`, `zip()`
- **Error Handling:** `onErrorReturn()`, `onErrorResume()`, `retry()`
- **Utility:** `doOnNext()`, `doOnError()`, `doOnSubscribe()`

### **5. Schedulers (Concurrency Control)**
- **Schedulers.immediate():** Current thread
- **Schedulers.single():** Single reusable thread
- **Schedulers.elastic():** Unlimited elastic thread pool (legacy)
- **Schedulers.parallel():** Fixed thread pool (CPU-bound work)
- **Schedulers.boundedElastic():** Capped elastic pool (I/O-bound work)
- **Usage:** `.subscribeOn(Schedulers.parallel())`, `.publishOn(Schedulers.boundedElastic())`

---

## **Article 2: Baeldung - Java Reactive Systems**
**Source:** https://www.baeldung.com/java-reactive-systems

### **1. Reactive Systems vs Reactive Programming**
- **Reactive Programming:** Programming paradigm (Mono/Flux operators)
- **Reactive Systems:** Architectural style building responsive apps
- **Relationship:** Reactive Programming enables Reactive Systems

### **2. Spring WebFlux Framework**
- **Non-blocking Stack:** Alternative to Spring MVC
- **Netty Server:** Default embedded reactive server
- **Router Functions:** Functional style routing
  ```java
  @Bean
  public RouterFunction<ServerResponse> route() {
      return RouterFunctions.route()
          .GET("/users", handler::getAllUsers)
          .POST("/users", handler::createUser)
          .build();
  }
  ```
- **WebClient:** Reactive HTTP client
  ```java
  WebClient.create()
      .get()
      .uri("/api/users")
      .retrieve()
      .bodyToFlux(User.class);
  ```

### **3. Backpressure Strategies**
1. **BUFFER:** Store items until consumer ready (risk of OOM)
2. **DROP:** Discard new items when buffer full
3. **LATEST:** Keep only latest item
4. **ERROR:** Throw exception when buffer overflows
5. **IGNORE:** No backpressure (not recommended)

### **4. Testing Reactive Streams**
- **StepVerifier:** Main testing utility
  ```java
  StepVerifier.create(flux)
      .expectNext("A", "B", "C")
      .expectComplete()
      .verify();
  ```
- **TestPublisher:** Manual control of test data
- **ExchangeTestClient:** Testing WebFlux endpoints

### **5. Database Integration**
- **R2DBC:** Reactive Relational Database Connectivity
- **MongoDB Reactive:** Reactive MongoDB driver
- **Cassandra Reactive:** Reactive Cassandra driver
- **Key Challenge:** Not all databases support reactive access

---

## **Article 3: Netguru - Reactive Programming in Java**
**Source:** https://www.netguru.com/blog/reactive-programming-java

### **1. When to Go Reactive (Use Cases)**
- **High-Concurrency Apps:** Thousands of concurrent connections
- **Real-time Systems:** Chat, gaming, trading platforms
- **Stream Processing:** Data pipelines, ETL
- **Microservices Communication:** Service-to-service async calls
- **NOT for:** Simple CRUD apps, low-concurrency systems

### **2. Migration Considerations**
- **Learning Curve:** Steep, different mental model
- **Debugging Complexity:** Stack traces less helpful, async flows harder to trace
- **Library Support:** Not all libraries are reactive-ready
- **Team Skills:** Requires training and mindset shift
- **Performance Gains:** Only materialize at scale (1000+ concurrent users)

### **3. Common Pitfalls & Solutions**
- **Blocking Calls in Reactive Chain:**
  ```java
  // BAD: Blocks thread
  flux.map(item -> blockingService.call(item));
  
  // GOOD: Offload to elastic scheduler
  flux.flatMap(item -> 
      Mono.fromCallable(() -> blockingService.call(item))
          .subscribeOn(Schedulers.boundedElastic())
  );
  ```
- **Memory Leaks:** Forgetting to dispose subscriptions
- **Thread Starvation:** Misconfigured schedulers
- **Error Swallowing:** Errors in async chains can be lost

### **4. Production Best Practices**
- **Monitoring:** Metrics, tracing, logs correlation
- **Circuit Breakers:** Resilience4j integration
- **Rate Limiting:** Prevent overwhelming downstream services
- **Timeouts:** Always set timeouts on external calls
- **Retry Strategies:** Exponential backoff with jitter

### **5. Reactive System Patterns**
- **Event Sourcing:** Store state changes as events
- **CQRS:** Separate read/write models
- **Saga Pattern:** Distributed transaction management
- **Message Broker Integration:** Kafka, RabbitMQ with reactive drivers

---

## **60-Second Recap for Interview**

1. **Two Concepts:** Reactive Programming (code-level) vs Reactive Systems (architecture-level)
2. **Core Types:** Mono (0-1) and Flux (0-N) async streams with backpressure
3. **Spring Stack:** WebFlux + Netty for non-blocking servers, WebClient for non-blocking clients
4. **When to Use:** High-concurrency, real-time, streaming scenarios - not for simple CRUD
5. **Key Benefit:** Better resource utilization through non-blocking I/O

---

## **Interview Checklist**

### **Conceptual Understanding:**
- [ ] Can explain difference between Reactive Programming and Reactive Systems
- [ ] Can define backpressure and name 3 strategies
- [ ] Knows when to use Mono vs Flux
- [ ] Can explain subscribeOn vs publishOn
- [ ] Understands Reactive Streams specification components

### **Technical Proficiency:**
- [ ] Can create simple reactive endpoints with WebFlux
- [ ] Knows how to handle errors in reactive chains
- [ ] Can combine multiple reactive streams (merge, zip, concat)
- [ ] Understands scheduler choices for different workloads
- [ ] Can test reactive code with StepVerifier

### **Scenario Response:**
**If asked about migrating to reactive:**
1. "First assess if we actually need it - reactive benefits only at high concurrency"
2. "Start with read-only endpoints, not write operations"
3. "Ensure database/third-party services support reactive"
4. "Train team on new mental model and debugging techniques"
5. "Implement gradually, with fallbacks to blocking where needed"

**If asked about error handling:**
1. "Use onErrorResume for fallback values"
2. "Use onErrorMap to transform exceptions"
3. "Use retry with backoff for transient failures"
4. "Always include doOnError for logging"
5. "Consider circuit breakers for external service failures"

**If asked about performance:**
1. "Profile to ensure we're not accidentally blocking"
2. "Tune scheduler thread pools based on workload type"
3. "Monitor memory usage - reactive can have different GC patterns"
4. "Use backpressure to prevent overwhelming clients/servers"
5. "Consider batching for database operations"

### **Architecture Questions:**
- [ ] **Microservices Communication:** "Use WebClient with reactive streams, implement retries/circuit breakers"
- [ ] **Database Integration:** "Choose databases with reactive drivers (R2DBC for SQL, reactive Mongo/Cassandra)"
- [ ] **Message Brokers:** "Use reactive Kafka/RabbitMQ clients for async event processing"
- [ ] **Monitoring:** "Correlate logs with trace IDs, monitor reactive-specific metrics (backpressure, demand)"

### **Common Interview Questions Prepared:**
- **"Why go reactive?"**
  - "For better resource utilization under high concurrency, not for performance of individual requests"

- **"Mono vs Flux?"**
  - "Mono for 0-1 results (like HTTP response), Flux for 0-N (like streaming data)"

- **"How does backpressure work?"**
  - "Subscriber controls flow by requesting N items at a time, preventing producer overwhelm"

- **"Blocking in reactive chain?"**
  - "Offload to boundedElastic scheduler, never block event loop threads"

### **Key Phrases to Use:**
- "Reactive is about resource efficiency, not raw speed"
- "Backpressure is crucial for system stability in data streaming scenarios"
- "The event loop threads must never be blocked - that's the cardinal rule"
- "Reactive systems are message-driven and resilient by design"
- "Spring WebFlux is an alternative stack to Spring MVC for non-blocking apps"
- "Test reactive streams with StepVerifier - traditional testing doesn't work well"

---

## **Reactive Decision Framework**

**Should we use reactive?**
- High concurrent users (>1000)? → Yes
- Real-time streaming needed? → Yes  
- Simple CRUD, low traffic? → No
- Team has reactive experience? → Consider
- All dependencies reactive-ready? → Required

**Which scheduler?**
- CPU-bound work → parallel()
- I/O-bound work → boundedElastic()
- Quick operations → immediate()
- Legacy blocking calls → boundedElastic()

**Remember:** Reactive programming has significant complexity costs - only use when the benefits (concurrency scale) justify the investment!

# **Spring Boot Reactive Architecture Summary**

## **Article 1: Reflectoring - Reactive Architecture with Spring Boot**
**Source:** https://reflectoring.io/reactive-architecture-with-spring-boot/

### **1. Architectural Patterns for Reactive Systems**
- **Event-Driven Architecture:**
  - Components communicate via async events
  - Loose coupling, better scalability
  - Event sourcing for state reconstruction
- **CQRS Pattern:**
  - Separate models for commands (write) and queries (read)
  - Allows optimization of each path
  - Can use different databases for read/write
- **Saga Pattern:**
  - Manages distributed transactions
  - Sequence of local transactions with compensating actions
  - Choreography (events) vs Orchestration (central coordinator)

### **2. Spring Boot Reactive Stack**
- **Spring WebFlux:**
  - Reactive web framework
  - Router functions (functional) vs @Controller (annotations)
  - Built on Project Reactor
- **Spring Data Reactive:**
  - Reactive repositories for MongoDB, Cassandra
  - R2DBC for reactive SQL
- **Spring Cloud Stream:**
  - Event-driven microservices
  - Binder abstraction for Kafka, RabbitMQ
- **Spring Security Reactive:**
  - Reactive security configuration
  - WebFilter chain instead of servlet filters

### **3. Deployment & Scaling Strategies**
- **Containerization:** Docker + Kubernetes
- **Horizontal Scaling:** Stateless services scale easily
- **Service Mesh:** Istio/Linkerd for traffic management
- **Observability:**
  - Metrics: Micrometer + Prometheus
  - Tracing: Spring Cloud Sleuth + Zipkin
  - Logging: Structured logs with correlation IDs

### **4. Resilience Patterns**
- **Circuit Breaker:** Resilience4j integration
  ```java
  @CircuitBreaker(name = "backendService", fallbackMethod = "fallback")
  public Mono<String> callService() { ... }
  ```
- **Retry:** Exponential backoff with jitter
- **Bulkhead:** Isolate failures to specific thread pools
- **Rate Limiter:** Prevent service overload

### **5. Testing Strategy**
- **Integration Tests:** `@SpringBootTest` with `WebTestClient`
- **Unit Tests:** `StepVerifier` for reactive streams
- **Contract Tests:** Pact for consumer-driven contracts
- **Performance Tests:** Gatling for load testing reactive endpoints

---

## **Article 2: Dev.to - Reactive Programming with Spring Boot and WebFlux**
**Source:** https://dev.to/tutorialq/reactive-programming-with-spring-boot-and-web-flux-io8

### **1. Getting Started with WebFlux**
- **Dependencies:**
  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-webflux</artifactId>
  </dependency>
  ```
- **Two Programming Models:**
  1. **Annotation-based:** Similar to Spring MVC
     ```java
     @RestController
     public class UserController {
         @GetMapping("/users")
         public Flux<User> getUsers() { ... }
     }
     ```
  2. **Functional endpoints:** Router functions
     ```java
     @Bean
     public RouterFunction<ServerResponse> routes() {
         return route(GET("/users"), handler::getUsers);
     }
     ```

### **2. Reactive REST Client - WebClient**
- **Non-blocking HTTP client:**
  ```java
  WebClient client = WebClient.create("http://api.example.com");
  
  Mono<User> user = client.get()
      .uri("/users/{id}", userId)
      .retrieve()
      .bodyToMono(User.class);
  ```
- **Features:**
  - Request/response body reactive types
  - Functional fluent API
  - Built-in error handling
  - Exchange strategies for customization

### **3. Server-Sent Events (SSE)**
- **Real-time streaming to clients:**
  ```java
  @GetMapping(value = "/stream", produces = MediaType.TEXT_EVENT_STREAM_VALUE)
  public Flux<ServerSentEvent<String>> streamEvents() {
      return Flux.interval(Duration.ofSeconds(1))
          .map(seq -> ServerSentEvent.builder("Event " + seq).build());
  }
  ```
- **Use cases:** Live updates, notifications, dashboards

### **4. Reactive WebSocket**
- **Full-duplex communication:**
  ```java
  @Configuration
  public class WebSocketConfig {
      @Bean
      public HandlerMapping handlerMapping() {
          Map<String, WebSocketHandler> map = new HashMap<>();
          map.put("/ws", new MyWebSocketHandler());
          return new SimpleUrlHandlerMapping(map, -1);
      }
  }
  ```

### **5. Error Handling in Reactive Chains**
- **Global Error Handling:**
  ```java
  @Bean
  public WebExceptionHandler webExceptionHandler() {
      return (exchange, ex) -> {
          // handle error
          return Mono.empty();
      };
  }
  ```
- **Controller-level:**
  ```java
  @ExceptionHandler(Exception.class)
  public ResponseEntity<String> handleException(Exception e) {
      return ResponseEntity.status(500).body("Error: " + e.getMessage());
  }
  ```

---

## **Article 3: Simform Engineering - Deep Dive into Reactive Spring Boot**
**Source:** https://medium.com/simform-engineering/deep-dive-into-reactive-programming-with-spring-boot-d62cae63bb03

### **1. Internal Architecture**
- **Event Loop Model:**
  - Small number of threads (usually CPU cores * 2)
  - Non-blocking I/O operations
  - Netty as default server (also supports Tomcat, Jetty, Undertow)
- **Reactor Netty Threading:**
  - Event loop group (non-blocking I/O)
  - Worker thread pool (blocking tasks when necessary)

### **2. Performance Characteristics**
- **Memory Usage:**
  - Lower thread stack memory
  - Different garbage collection patterns
  - Potential for object allocation churn
- **Throughput vs Latency:**
  - Better throughput under high concurrency
  - Similar or slightly higher latency for single requests
  - **Key insight:** Not faster, just more efficient

### **3. Database Integration Patterns**
- **Reactive vs Blocking Drivers:**
  - Reactive drivers: MongoDB, Cassandra, Couchbase
  - R2DBC for SQL databases (Postgres, MySQL, MSSQL)
  - **Important:** JDBC is blocking, cannot use in reactive chains
- **Connection Pool Management:**
  - Smaller pools needed (connections not tied to threads)
  - Connection reuse is more efficient
- **Transaction Management:**
  - Reactive transactions via `@Transactional` (Spring Data R2DBC)
  - Different semantics than JDBC transactions

### **4. Caching Strategies**
- **Reactive Cache Managers:**
  - Caffeine with reactive support
  - Redis reactive cache
  ```java
  @Cacheable(cacheNames = "users", key = "#id")
  public Mono<User> findUserById(String id) { ... }
  ```
- **Cache-Aside Pattern:**
  ```java
  public Mono<User> getUser(String id) {
      return cacheManager.getCache("users")
          .get(id, User.class)
          .switchIfEmpty(repository.findById(id)
              .doOnNext(user -> cacheManager.getCache("users").put(id, user)));
  }
  ```

### **5. Production Readiness Checklist**
- **Monitoring & Metrics:**
  - `/actuator/metrics` for reactive-specific metrics
  - Backpressure monitoring
  - Request latency percentiles
- **Debugging Tools:**
  - Reactor Debug Agent (adds traceability)
  - Checkpoint() for stream debugging
  - Hooks.onOperatorDebug()
- **Common Production Issues:**
  - Memory leaks from unsubscribed streams
  - Blocking calls on event loop threads
  - Missing backpressure causing OOM
  - Thread starvation from misconfigured schedulers

---

## **60-Second Recap for Interview**

1. **Two WebFlux Styles:** Annotation-based (like MVC) and functional (RouterFunctions)
2. **Non-blocking Stack:** WebFlux + Netty + Reactive DB drivers + WebClient
3. **Key Benefit:** Efficient resource usage for high-concurrency, not faster single requests
4. **Architecture Patterns:** CQRS, Event Sourcing, Saga for distributed transactions
5. **Production Critical:** Monitoring, proper error handling, never block event loop

---

## **Interview Checklist**

### **Architecture & Patterns:**
- [ ] Can explain when to use CQRS and its trade-offs
- [ ] Knows Saga pattern for distributed transactions
- [ ] Understands event-driven vs request-response architectures
- [ ] Can design reactive microservices communication
- [ ] Knows deployment considerations for reactive apps

### **Spring Boot Implementation:**
- [ ] Can create both annotation-based and functional WebFlux endpoints
- [ ] Knows how to configure WebClient for service