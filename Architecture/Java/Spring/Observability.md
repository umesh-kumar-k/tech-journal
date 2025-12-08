## Spring Framework Observability Interview Checklist

- **Core Concept**: Micrometer Observation API enables metrics (timers/gauges/counters for runtime stats) and traces (end-to-end request paths across services) via ObservationRegistry.[spring](https://docs.spring.io/spring-framework/reference/integration/observability.html)​
    
- **Produced Observations**: HTTP server/client (http.server.requests/requests), JMS messaging (jms.message.publish/process), @Scheduled tasks; auto-instruments Spring components when registry configured.
    
- **KeyValues Metadata**: Low cardinality (method, status, outcome for metrics); high cardinality (URI for traces); customizable via ObservationConvention implementations.
    
- **HTTP Server Setup**: Servlet uses ServerHttpObservationFilter; Reactive via WebHttpHandlerBuilder.observationRegistry(); records unhandled exceptions, supports OTel semantic conventions.
    
- **HTTP Client Setup**: Configure ObservationRegistry on RestTemplate/RestClient/WebClient builders (Spring Boot auto-configures); measures full exchange (connect to deserialize).
    
- **JMS Instrumentation**: Micrometer-jakarta9 for publish/process; set registry on JmsTemplate or JmsListenerContainerFactory; propagates trace context via headers.
    
- **@Scheduled Tasks**: Implement SchedulingConfigurer to set registry on ScheduledTaskRegistrar; uses DefaultScheduledTaskObservationConvention with KeyValues like task name/class.
    
- **Customization**: Extend Default*ObservationConvention for extra KeyValues; use ObservationFilter for post-processing; global config via ObservationRegistry.observationConfig().
    
- **Tools/Frameworks**: Micrometer (facade), OpenTelemetry (semantics/bridge), Prometheus/Grafana (metrics viz), Jaeger/Zipkin (traces), ELK (logs correlation).
    
- **Distributed Context**: Use ContextPropagatingTaskDecorator for @Async/@EventListener to propagate trace/ThreadLocal across threads; requires micrometer-context-propagation.
    
- **Architect Trade-offs**: Zero-code via conventions vs custom for business metrics; sampling for high-volume traces; OTel migration path from Micrometer legacy.
    
- **Testing/Monitoring**: Assert observations in tests; expose via Actuator; SLOs on request latency/error rates.
    

## 60-Second Recap

- Micrometer Observation = metrics+traces via registry; auto-instruments HTTP/JMS/@Scheduled in Spring.
    
- Setup: Filter/Builder/Container configs; customize Convention/Filter for KeyValues (low/high cardinality).
    
- Tools: Micrometer+OTel → Prometheus/Grafana+Jaeger; propagate context for async/distributed.
    
- Gold: Semantic conventions, zero-code first, custom business telemetry, end-to-end SLOs.
    

**Reference**: [Spring Framework Observability](https://docs.spring.io/spring-framework/reference/integration/observability.html)[spring](https://docs.spring.io/spring-framework/reference/integration/observability.html)​

1. [https://docs.spring.io/spring-framework/reference/integration/observability.html](https://docs.spring.io/spring-framework/reference/integration/observability.html)

# **Spring Observability Summary**

**Source:** https://docs.spring.io/spring-framework/reference/integration/observability.html

## **1. Observability Core Concepts**

### **A. Three Pillars of Observability**
1. **Metrics:** Quantitative measurements over time
   - System metrics (CPU, memory, threads)
   - Application metrics (requests, errors, latency)
   - Business metrics (orders, users, revenue)

2. **Traces:** Distributed request tracking
   - End-to-end request flow
   - Span hierarchy and timing
   - Context propagation between services

3. **Logs:** Structured event data
   - Application events and errors
   - Structured key-value pairs
   - Correlation with traces and metrics

### **B. Observability vs Monitoring**
- **Monitoring:** Known unknowns (alert on predefined thresholds)
- **Observability:** Unknown unknowns (understand system behavior from outputs)
- **Spring's Approach:** Provides tools for both

## **2. Observability Infrastructure**

### **A. Micrometer Integration**
- **Metrics facade:** Vendor-agnostic metrics API
- **Supported backends:** Prometheus, Datadog, New Relic, Azure Monitor, etc.
- **Automatic metrics:** HTTP requests, JVM, cache, data source
- **Custom metrics:** Via `MeterRegistry`

### **B. Spring Boot Actuator**
- **Production-ready features:** Health checks, metrics, auditing
- **Endpoints:** `/actuator/health`, `/actuator/metrics`, `/actuator/prometheus`
- **Customization:** Add custom health indicators, metrics, info contributors

### **C. Context Propagation**
- **TraceId/SpanId:** Propagated across thread boundaries and HTTP calls
- **Thread-local management:** `ObservationThreadLocalAccessor`
- **Reactor Context:** For reactive applications

## **3. Observation API**

### **A. Key Components**
- **ObservationRegistry:** Central registry for observations
- **Observation:** Single unit of work being observed
- **ObservationHandler:** Processes observation events
- **ObservationConvention:** Customizes observation naming/tags

### **B. Creating Observations**
```java
Observation observation = Observation.createNotStarted("operation.name", registry)
    .lowCardinalityKeyValue("service", "inventory")
    .highCardinalityKeyValue("userId", "12345")
    .observe(() -> {
        // business logic
        return result;
    });
```

### **C. Observation Scopes**
- **Global:** Application-level observations
- **Local:** Method or operation-level observations
- **Hierarchical:** Nested observations create parent-child relationships

## **4. Instrumentation Points**

### **A. Web MVC Instrumentation**
- **Automatic:** HTTP requests via `ObservationFilter`
- **Tags:** HTTP method, URI, status, exception
- **Customization:** Add custom tags via `@ControllerAdvice`

### **B. Data Access Instrumentation**
- **JDBC:** Connection, query execution time
- **JPA/Hibernate:** Entity operations, query performance
- **Redis/MongoDB:** Cache/database operations

### **C. Messaging Instrumentation**
- **JMS:** Message sending/receiving
- **Kafka/RabbitMQ:** Message processing timing
- **Spring Integration:** Channel, handler instrumentation

## **5. Configuration & Customization**

### **A. Observation Configuration**
```java
@Configuration
public class ObservabilityConfig {
    
    @Bean
    public ObservationRegistry observationRegistry() {
        ObservationRegistry registry = ObservationRegistry.create();
        registry.observationConfig()
            .observationHandler(new SimpleLoggingHandler())
            .observationPredicate((name, context) -> !name.contains("internal"));
        return registry;
    }
}
```

### **B. Custom Observation Handlers**
```java
public class CustomObservationHandler implements ObservationHandler<Observation.Context> {
    
    @Override
    public void onStart(Observation.Context context) {
        log.info("Observation started: {}", context.getName());
    }
    
    @Override
    public boolean supportsContext(Observation.Context context) {
        return true;
    }
}
```

### **C. Metrics Customization**
```java
@Component
public class CustomMetrics {
    
    private final Counter customCounter;
    
    public CustomMetrics(MeterRegistry registry) {
        this.customCounter = Counter.builder("custom.operations")
            .description("Number of custom operations")
            .register(registry);
    }
    
    public void recordOperation() {
        customCounter.increment();
    }
}
```

## **6. Distributed Tracing**

### **A. Context Propagation**
- **HTTP Headers:** `traceparent`, `tracestate`, `baggage`
- **Messaging:** Headers in message properties
- **Async Operations:** Thread-local to async context propagation

### **B. Span Creation**
```java
Observation observation = Observation.createNotStarted("service.operation", registry)
    .parentObservation(parentObservation)  // Link to parent
    .start();

try (Observation.Scope scope = observation.openScope()) {
    // Operation with parent context
} finally {
    observation.stop();
}
```

### **C. Baggage (Custom Context)**
```java
observation.contextPut("user.id", "12345");

// Later in the trace
String userId = observation.contextGet("user.id");
```

## **7. Performance Considerations**

### **A. Observation Overhead**
- **Minimal impact:** Designed for production use
- **Sampling:** Configure sample rate for high-volume operations
- **Cardinality management:** Use low-cardinality tags for high-volume metrics

### **B. Memory & CPU Impact**
- **Context storage:** Thread-local storage for active observations
- **Handler chain:** Multiple handlers add sequential overhead
- **Tag creation:** String operations for tag/key-value pairs

### **C. Sampling Strategies**
```java
registry.observationConfig()
    .observationPredicate((name, context) -> {
        // Sample only important operations
        return name.startsWith("important.");
    });
```

## **8. Integration with Observability Backends**

### **A. Prometheus Integration**
```yaml
# application.yml
management:
  endpoints:
    web:
      exposure:
        include: health,prometheus,metrics
  metrics:
    export:
      prometheus:
        enabled: true
```

### **B. OpenTelemetry Integration**
- **Bridge:** Micrometer to OpenTelemetry bridge
- **Automatic instrumentation:** Spring auto-configures OTel
- **Exporters:** Jaeger, Zipkin, OTLP

### **C. Custom Exporters**
```java
@Bean
public MeterRegistryCustomizer<MeterRegistry> metricsCommonTags() {
    return registry -> registry.config().commonTags(
        "application", "inventory-service",
        "environment", "production"
    );
}
```

## **9. Testing Observability**

### **A. Unit Testing Observations**
```java
@Test
void testObservation() {
    SimpleObservationRegistry registry = new SimpleObservationRegistry();
    
    Observation observation = Observation.start("test.operation", registry);
    
    // Verify observation was created
    assertThat(registry.getCurrentObservation()).isEqualTo(observation);
}
```

### **B. Integration Testing**
```java
@SpringBootTest
@AutoConfigureObservability  // Special test annotation
public class ObservabilityIntegrationTest {
    
    @Test
    void testHttpTracing() {
        // HTTP requests will be automatically observed
    }
}
```

### **C. Metrics Testing**
```java
@Test
void testCustomMetrics() {
    SimpleMeterRegistry registry = new SimpleMeterRegistry();
    CustomMetrics metrics = new CustomMetrics(registry);
    
    metrics.recordOperation();
    
    assertThat(registry.find("custom.operations").counter().count()).isEqualTo(1);
}
```

## **10. Best Practices**

### **A. Naming Conventions**
- **Dot notation:** `service.operation.action`
- **Consistent tagging:** Same tag names across services
- **Avoid high cardinality:** In metric names and tags

### **B. Production Configuration**
- **Enable sampling:** For high-volume services
- **Configure exporters:** Based on observability backend
- **Set service name:** Consistent across all services

### **C. Alerting Strategy**
- **SLO-based alerts:** Based on service level objectives
- **Error budget:** Track and alert on budget consumption
- **Anomaly detection:** Beyond simple threshold alerts

---

## **60-Second Recap for Interview**

1. **Three Pillars:** Metrics (Micrometer), Traces (Observation API), Logs (structured)
2. **Core API:** `ObservationRegistry`, `Observation`, `ObservationHandler`
3. **Automatic Instrumentation:** Web MVC, Data access, Messaging via Spring integration
4. **Context Propagation:** TraceId/SpanId across threads and services
5. **Production Ready:** Integrates with Prometheus, OTel, vendor backends

---

## **Interview Checklist**

### **Observability Concepts:**
- [ ] Can explain difference between monitoring and observability
- [ ] Knows the three pillars of observability
- [ ] Understands context propagation in distributed systems
- [ ] Can explain sampling strategies and their importance
- [ ] Knows cardinality impact on metrics systems

### **Spring Implementation:**
- [ ] Can configure ObservationRegistry with custom handlers
- [ ] Knows how to create and use Observations
- [ ] Understands automatic instrumentation points in Spring
- [ ] Can integrate with Prometheus/OpenTelemetry
- [ ] Knows how to add custom metrics/tags

### **Scenario Response:**
**If asked about implementing observability:**
1. "Start with Spring Boot Actuator for basic metrics"
2. "Add Micrometer for vendor-agnostic metrics"
3. "Use Observation API for custom tracing"
4. "Configure appropriate exporters for your observability backend"
5. "Implement structured logging with trace correlation"

**If asked about performance impact:**
1. "Use sampling for high-volume operations"
2. "Be careful with tag cardinality - use low-cardinality tags for high-volume metrics"
3. "Consider async handlers for non-critical observations"
4. "Profile observability overhead in performance tests"
5. "Use conditional observation predicates to filter unnecessary observations"

**If asked about distributed tracing:**
1. "Ensure trace context propagates through HTTP headers"
2. "Use Observation.parentObservation() to link spans"
3. "Implement baggage for custom context propagation"
4. "Configure consistent sampling across services"
5. "Test trace propagation in integration tests"

### **Common Interview Questions Prepared:**
- **"Observability vs Monitoring?"**
  - "Monitoring tracks known metrics, observability helps understand system behavior from outputs, especially for unknown issues"

- **"How does Spring implement distributed tracing?"**
  - "Through the Observation API which creates spans, propagates context via headers, and integrates with tracing backends"

- **"Micrometer's role?"**
  - "Vendor-agnostic metrics facade that abstracts different monitoring systems, integrated with Spring's observability"

- **"Handling high cardinality in metrics?"**
  - "Use low-cardinality tags, aggregate where possible, sample high-volume metrics, use histograms for distributions"

### **Architecture & Design:**
- [ ] **Cross-cutting concern:** "Observability should be built-in, not bolted on"
- [ ] **Consistency:** "Standardize naming, tagging, and sampling across services"
- [ ] **Cost management:** "Control data volume with sampling and aggregation"
- [ ] **Team enablement:** "Provide libraries and patterns for consistent implementation"
- [ ] **Evolution:** "Start simple, add complexity based on actual needs"

### **Key Phrases to Use:**
- "Observability is about understanding system behavior from the outside, not just checking predefined metrics"
- "Spring's Observation API provides a unified model for metrics and tracing"
- "Context propagation is crucial for correlating events across distributed systems"
- "High cardinality is the enemy of metrics systems - design with cardinality in mind"
- "Sampling allows us to get representative data without overwhelming our observability backend"
- "Structured logs with trace correlation complete the observability picture alongside metrics and traces"

---

## **Observability Implementation Roadmap**

**Phase 1: Foundation**
1. Enable Spring Boot Actuator
2. Add basic health checks
3. Configure metrics export
4. Implement structured logging

**Phase 2: Enhanced Observability**
1. Add custom metrics with Micrometer
2. Implement distributed tracing with Observation API
3. Set up alerting based on SLOs
4. Add business metrics

**Phase 3: Advanced**
1. Implement automatic anomaly detection
2. Add user-experience monitoring
3. Set up continuous profiling
4. Implement AIOps capabilities

**Remember:** Observability is a journey, not a destination. Start with the basics, measure what matters for your business, and iterate based on what you learn!