# **Spring Boot Fundamentals Summary**

## **Article 1: Spring.io Getting Started Guide**
**Source:** https://spring.io/guides/gs/spring-boot/

### **1. Spring Boot Core Philosophy**
- **Opinionated defaults:** Sane defaults that can be overridden
- **Standalone applications:** Embedded servers (Tomcat, Jetty, Undertow)
- **Production-ready:** Actuator endpoints, metrics, health checks
- **No code generation:** No XML configuration required
- **Microservices ready:** Built for distributed systems

### **2. Spring Boot Starter Dependencies**
- **Convention:** `spring-boot-starter-*` naming pattern
- **Common starters:**
  - `spring-boot-starter-web`: Web applications
  - `spring-boot-starter-data-jpa`: JPA with Hibernate
  - `spring-boot-starter-security`: Spring Security
  - `spring-boot-starter-test`: Testing support
  - `spring-boot-starter-actuator`: Production monitoring
- **Dependency management:** Inherits from `spring-boot-dependencies` BOM

### **3. Auto-configuration Magic**
- **Conditional configuration:** `@Conditional` annotations
- **Auto-configuration classes:** `spring-boot-autoconfigure` module
- **Exclusion:** `@SpringBootApplication(exclude = {...})`
- **Order:** User configurations override auto-configuration

### **4. Main Application Class**
```java
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```
- **@SpringBootApplication:** Combines:
  - `@Configuration`: Source of bean definitions
  - `@EnableAutoConfiguration`: Enable auto-configuration
  - `@ComponentScan`: Scan for components in package

### **5. Externalized Configuration**
- **Hierarchical sources:** 17+ sources in specific order
- **Common sources:**
  1. Command line arguments
  2. `SPRING_APPLICATION_JSON` (inline JSON)
  3. `ServletConfig` init parameters
  4. `ServletContext` init parameters
  5. JNDI attributes
  6. Java System properties
  7. OS environment variables
  8. `application-{profile}.properties/yml`
  9. `application.properties/yml`
- **YAML support:** Hierarchical configuration
- **Profile-specific:** `application-{profile}.properties`

### **6. Embedded Servers**
- **Tomcat (default):** Servlet 5.0, JSP 3.1
- **Jetty:** Reactive applications, lightweight
- **Undertow:** High performance, non-blocking
- **Configuration:**
  ```properties
  server.port=8081
  server.servlet.context-path=/api
  ```

### **7. SpringApplication Customization**
- **Banner customization:** `banner.txt` in resources
- **Application events:** `ApplicationListener`
- **Builder pattern:** `new SpringApplicationBuilder()`
- **Web application types:** `NONE`, `SERVLET`, `REACTIVE`

---

## **Article 2: GeeksforGeeks Spring Boot Advanced**
**Source:** https://www.geeksforgeeks.org/advance-java/spring-boot/

### **1. Spring Boot Architecture Layers**
- **Presentation Layer:** Controllers, REST endpoints
- **Business Layer:** Services, business logic
- **Persistence Layer:** Repositories, data access
- **Database Layer:** Data storage

### **2. Advanced Features**

#### **A. Actuator & Monitoring**
- **Production monitoring endpoints:**
  - `/actuator/health`: Application health
  - `/actuator/metrics`: Application metrics
  - `/actuator/info`: Application info
  - `/actuator/env`: Environment properties
  - `/actuator/beans`: All Spring beans
- **Security:** Secure actuator endpoints
- **Custom health indicators:**
  ```java
  @Component
  public class CustomHealthIndicator implements HealthIndicator {
      @Override
      public Health health() {
          // custom health check
          return Health.up().build();
      }
  }
  ```

#### **B. Spring Boot Testing**
- **Test slices:** Test specific parts of application
  ```java
  @WebMvcTest(HomeController.class)  // MVC slice
  @DataJpaTest                       // JPA slice  
  @JsonTest                          // JSON slice
  @RestClientTest                    // REST client slice
  ```
- **Test annotations:**
  - `@SpringBootTest`: Full integration test
  - `@TestPropertySource`: Test properties
  - `@MockBean`: Mock Spring beans
- **Test utilities:**
  - `TestRestTemplate`: For integration tests
  - `MockMvc`: For MVC unit tests
  - `@Sql`: Database test data

#### **C. Spring Boot DevTools**
- **Automatic restart:** Code changes trigger restart
- **Live reload:** Browser refresh without restart
- **Property defaults:** Sensible development defaults
- **Remote debugging:** Remote application support
- **Exclusions:** `spring.devtools.restart.exclude`

#### **D. Spring Boot CLI**
- **Rapid prototyping:** Write apps with Groovy
- **Command line:**
  ```bash
  spring run app.groovy
  spring init --dependencies=web,data-jpa myproject
  ```
- **Features:**
  - Auto-import detection
  - Auto-main method
  - Embedded server

#### **E. Spring Boot Database Integration**
- **Auto-configured DataSource:** Based on dependencies
- **Connection pooling:** HikariCP (default), Tomcat, Commons DBCP2
- **Flyway/Liquibase:** Database migration support
- **Multiple databases:** Configure multiple DataSources

#### **F. Spring Boot Security**
- **Auto-configuration:** Default security configuration
- **Custom security:**
  ```java
  @Configuration
  public class SecurityConfig extends WebSecurityConfigurerAdapter {
      @Override
      protected void configure(HttpSecurity http) throws Exception {
          http
              .authorizeRequests()
              .antMatchers("/public/**").permitAll()
              .anyRequest().authenticated()
              .and()
              .formLogin();
      }
  }
  ```
- **OAuth2:** Social login, resource server configuration

#### **G. Spring Boot Caching**
- **Cache providers:** EhCache, Hazelcast, Redis, Caffeine
- **Annotations:**
  ```java
  @Cacheable("books")
  @CacheEvict("books")
  @CachePut("books")
  ```
- **Configuration:** `spring.cache.type`, cache manager beans

#### **H. Spring Boot Messaging**
- **JMS:** ActiveMQ, Artemis
- **AMQP:** RabbitMQ
- **Kafka:** Spring for Apache Kafka
- **WebSocket:** STOMP support

#### **I. Spring Boot Batch Processing**
- **Job configuration:** `@EnableBatchProcessing`
- **Job components:** Job, Step, ItemReader, ItemProcessor, ItemWriter
- **Scheduling:** `@Scheduled` cron expressions

### **3. Production Deployment**

#### **A. Packaging Options**
- **Executable JAR:** `java -jar app.jar` (default)
- **WAR file:** Traditional web application
- **Docker containers:** Layered JAR for efficient Docker builds
- **Cloud deployment:** Cloud Foundry, Heroku, AWS, Azure

#### **B. Performance Optimization**
- **Connection pooling:** Tune HikariCP settings
- **Cache configuration:** Appropriate cache size and TTL
- **Thread pool tuning:** Async, scheduled tasks
- **Garbage collection:** JVM tuning for container environments

#### **C. Logging Configuration**
- **Logback (default):** Spring Boot's default logging
- **Log levels:** `logging.level.root=WARN`
- **Log groups:** Predefined groups for Spring components
- **File logging:** `logging.file.name`, `logging.file.path`

### **4. Spring Boot Best Practices**

#### **A. Code Organization**
- **Package by feature:** Not by layer
- **Configuration classes:** Separate from business logic
- **Constants:** Use `@ConfigurationProperties`
- **Exception handling:** Global `@ControllerAdvice`

#### **B. Configuration Management**
- **Profile-based:** Different configurations per environment
- **External configuration:** Config servers, environment variables
- **Sensitive data:** Use Spring Cloud Config or Vault
- **Validation:** `@Validated` on configuration classes

#### **C. Monitoring & Observability**
- **Custom metrics:** Micrometer integration
- **Distributed tracing:** Sleuth/Zipkin
- **Alerting:** Health indicators with thresholds
- **Audit logging:** Spring Boot Audit support

---

## **60-Second Recap for Interview**

1. **Core Concept:** Opinionated framework for production-ready Spring apps
2. **Key Features:** Auto-configuration, starters, embedded servers, Actuator
3. **Configuration:** Hierarchical property sources, YAML support, profiles
4. **Production:** Health checks, metrics, externalized config, packaging options
5. **Testing:** Test slices, MockMvc, TestRestTemplate, @MockBean

---

## **Interview Checklist**

### **Spring Boot Core Concepts:**
- [ ] Can explain Spring Boot's opinionated defaults philosophy
- [ ] Knows what @SpringBootApplication combines
- [ ] Understands auto-configuration and conditional beans
- [ ] Can explain starter dependencies and their purpose
- [ ] Knows configuration property hierarchy

### **Advanced Features:**
- [ ] Can configure Actuator endpoints and security
- [ ] Knows different testing strategies and test slices
- [ ] Understands DevTools for development productivity
- [ ] Can configure multiple databases and connection pools
- [ ] Knows caching strategies and implementations

### **Scenario Response:**
**If asked about migrating to Spring Boot:**
1. "Start with Spring Initializr for project setup"
2. "Replace XML configuration with Java config or properties"
3. "Use starter dependencies for auto-configuration"
4. "Externalize configuration for different environments"
5. "Add Actuator for monitoring in production"

**If asked about production deployment:**
1. "Use executable JAR with embedded server for container deployment"
2. "Configure proper logging and monitoring with Actuator"
3. "Externalize sensitive configuration (secrets, passwords)"
4. "Set up health checks and readiness/liveness probes"
5. "Configure connection pooling and thread pools appropriately"

**If asked about performance optimization:**
1. "Profile application to find bottlenecks"
2. "Configure connection pooling (HikariCP settings)"
3. "Implement caching for frequently accessed data"
4. "Use async processing for long-running operations"
5. "Tune JVM settings for container environment"

### **Common Interview Questions Prepared:**
- **"What is Spring Boot?"**
  - "Opinionated framework for creating production-ready Spring applications with minimal configuration"

- **"@SpringBootApplication annotation?"**
  - "Combines @Configuration, @EnableAutoConfiguration, and @ComponentScan"

- **"Auto-configuration how it works?"**
  - "Uses @Conditional annotations to configure beans based on classpath, properties, and other beans"

- **"Spring Boot vs Spring MVC?"**
  - "Spring Boot is an opinionated framework that auto-configures Spring MVC and other components"

### **Architecture & Design:**
- [ ] **Microservices ready:** "Built for distributed systems with embedded servers and external config"
- [ ] **Production focus:** "Actuator provides health, metrics, and management endpoints"
- [ ] **Configuration strategy:** "Externalize all configuration, use profiles for environments"
- [ ] **Testing strategy:** "Use test slices for focused testing, @SpringBootTest for integration"
- [ ] **Deployment strategy:** "Container-friendly with executable JARs and layered Docker builds"

### **Key Phrases to Use:**
- "Spring Boot's opinionated defaults reduce boilerplate while allowing customization"
- "Auto-configuration works through @Conditional annotations that check classpath, properties, and bean existence"
- "Starter dependencies provide curated sets of dependencies for common use cases"
- "Actuator makes applications production-ready with health checks, metrics, and monitoring"
- "Externalized configuration with hierarchical property sources enables environment-specific setups"
- "Test slices in Spring Boot allow testing specific layers without loading the entire context"

---

## **Spring Boot Decision Framework**

**Project Setup:**
- New project → Spring Initializr with appropriate starters
- Legacy migration → Gradually replace XML with Java config
- Microservices → Use embedded servers, external config, Actuator

**Configuration Strategy:**
- Simple app → application.properties/yml
- Multiple environments → Profiles (application-{env}.properties)
- Cloud deployment → Config server, environment variables
- Secrets → Vault, Kubernetes secrets, environment variables

**Production Considerations:**
- Monitoring → Actuator + Micrometer + Prometheus/Grafana
- Logging → Structured logging with correlation IDs
- Deployment → Docker with layered JARs, health checks
- Scaling → Stateless design, external session storage

**Testing Strategy:**
- Unit tests → Mock dependencies
- Integration tests → @SpringBootTest with TestRestTemplate
- Slice tests → @WebMvcTest, @DataJpaTest for specific layers
- End-to-end → Separate test suite

**Remember:** Spring Boot is about productivity and production-readiness. It simplifies Spring development but doesn't remove the need to understand Spring fundamentals!