## Spring Transaction Management Interview Checklist

- **Core Abstraction**: Unified API across JTA, JDBC, Hibernate, JPA; PlatformTransactionManager (DataSourceTransactionManager, JpaTransactionManager) handles resource-specific tx logic.[spring](https://docs.spring.io/spring-framework/reference/data-access/transaction.html)​
    
- **Declarative Tx**: @Transactional(propagation=REQUIRED, isolation=READ_COMMITTED, rollbackFor=Exception.class); AOP proxy intercepts methods, binds resources to thread.
    
- **Propagation Types**: REQUIRED (default, join/create), REQUIRES_NEW (suspend+new), NESTED (savepoint), SUPPORTS (no-tx ok); architect choice for saga/choreography patterns.
    
- **Isolation Levels**: READ_UNCOMMITTED (dirty reads), READ_COMMITTED (non-repeatable), REPEATABLE_READ (phantom), SERIALIZABLE (full lock); balance consistency vs perf.
    
- **Programmatic Tx**: TransactionTemplate.execute() or TransactionAspectSupport.currentTransactionStatus(); use for dynamic tx boundaries, batch ops.
    
- **Resource Synchronization**: getCurrentSession()/getCurrentEntityManager() auto-binds resources; ConnectionHolder ensures reuse within tx bounds.
    
- **Distributed Tx**: JtaTransactionManager + Atomikos/Narayana for XA across DBs/JMS; Spring Boot + Spring Cloud for microservices sagas (compensating tx).
    
- **Tools/Frameworks**: HikariCP (pooling), Hibernate (2nd-level cache), Spring Batch (chunked tx), Kafka/RabbitMQ (tx-aware messaging), Debezium (CDC).
    
- **Pitfalls**: Self-invocation bypasses @Transactional (use AopContext), proxy pitfalls (interface/CGLIB), optimistic locking (@Version).
    
- **Architect Trade-offs**: Declarative (clean code) vs Programmatic (flexible); Local vs XA (scale vs 2PC perf); Reactive (R2DBC TransactionalOperator).
    
- **Monitoring**: Actuator metrics (tx commit/rollback rates), Micrometer timers; audit tx events via listeners.
    
- **Testing**: @Transactional in tests (rollback), TestTransaction for manual control; AbstractTransactionalJUnit4SpringContextTests.
    

## 60-Second Recap

- Spring Tx = PlatformTransactionManager abstraction + @Transactional AOP for declarative management across JDBC/JPA/Hibernate/JTA.
    
- Key: Propagation/Isolation config, resource auto-binding, XA for distributed.
    
- Tools: Hikari + Atomikos + Spring Batch; pitfalls: self-invocation, proxy limits.
    
- Gold: Declarative-first, saga-aware for microservices, comprehensive exception rollback.
    

**Reference**: [Spring Transaction Management](https://docs.spring.io/spring-framework/reference/data-access/transaction.html) | [Baeldung Tx Guide](https://www.baeldung.com/transaction-configuration-with-jpa-and-spring)[spring](https://docs.spring.io/spring-framework/reference/data-access/transaction.html)​

1. [https://docs.spring.io/spring-framework/reference/data-access/transaction.html](https://docs.spring.io/spring-framework/reference/data-access/transaction.html)

## Spring Declarative Transaction Interview Checklist

- **Core Mechanism**: @Transactional annotation on methods/classes/interfaces; AOP proxy (JDK/CGLIB) intercepts calls, creates PlatformTransactionManager-bound tx; defaults: propagation=REQUIRED, isolation=DEFAULT, rollbackFor=RuntimeException.[spring](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/annotations.html)​
    
- **Proxy Pitfalls**: Self-invocation bypasses proxy (no tx); private/final methods not proxied; use AopContext.currentProxy() or refactor to separate service.[marcobehler](https://www.marcobehler.com/guides/spring-transaction-management-transactional-in-depth)​
    
- **Propagation Levels**: REQUIRED (join/create), REQUIRES_NEW (suspend+new tx), NESTED (savepoint), MANDATORY (existing tx only), NEVER/SUPPORTS/NOT_SUPPORTED for no-tx scenarios.[spring](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/tx-propagation.html)​
    
- **Isolation Levels**: READ_UNCOMMITTED (dirty reads), READ_COMMITTED (non-repeatable), REPEATABLE_READ (phantoms), SERIALIZABLE (locks); trade consistency vs throughput.[w3tutorials](https://www.w3tutorials.net/blog/spring-transactional-isolation-propagation/)​
    
- **Rollback Rules**: Defaults to unchecked (RuntimeException/Error); customize rollbackFor/noRollbackFor=Exception.class; checked exceptions commit by default.[manish-in-java.github](https://manish-in-java.github.io/manish-in-java/page/spring/transactions.html)​
    
- **Precedence**: Method > Class > Interface; most specific wins; readOnly=true optimizes (no dirty checks, Hibernate flush prevention).
    
- **Config Options**: timeout (sec), label (tx monitor), value (qualifier for multiple tx managers).
    
- **Tools/Frameworks**: Spring Boot auto-config (DataSourceTransactionManager), Hibernate/JPA (JpaTransactionManager), Atomikos (JTA/XA), Spring Batch (chunked tx).[geeksforgeeks](https://www.geeksforgeeks.org/springboot/spring-boot-transaction-management-using-transactional-annotation/)​
    
- **Advanced**: @TransactionalEventListener (tx-bound events: AFTER_COMMIT/ROLLBACK), Reactive (TransactionalOperator), Multi-tx managers via qualifier.
    
- **Pitfalls**: Tx naming (class.method), overusing REQUIRES_NEW (leakage), high isolation deadlocks, forgetting noRollbackFor.[reddit](https://www.reddit.com/r/java/comments/pxwy0k/spring_transactional_mistakes_everyone_did/)​
    
- **Testing**: @Transactional(rollbackFor=Exception.class) in @SpringBootTest; verifyTxApplied(), TestTransaction for manual control.
    
- **Architect Trade-offs**: Declarative (clean, scalable) vs Programmatic (dynamic boundaries); local vs distributed (sagas over XA).
    

## 60-Second Recap

- @Transactional = AOP proxy + PlatformTransactionManager; defaults: REQUIRED/DEFAULT/unchecked rollback.
    
- Key: Proxy pitfalls, propagation/isolation tuning, rollback customization.
    
- Tools: Boot auto-config + Hibernate/Atomikos; avoid self-calls/high isolation.
    
- Gold: Declarative-first, explicit rollbackFor, saga patterns for microservices.
    

**Reference**: [Spring @Transactional Docs](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/annotations.html) | [Marco Behler Guide](https://www.marcobehler.com/guides/spring-transaction-management-transactional-in-depth)[spring+1](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/annotations.html)​

1. [https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/annotations.html](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/annotations.html)
2. [https://www.marcobehler.com/guides/spring-transaction-management-transactional-in-depth](https://www.marcobehler.com/guides/spring-transaction-management-transactional-in-depth)
3. [https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/tx-propagation.html](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/tx-propagation.html)
4. [https://www.w3tutorials.net/blog/spring-transactional-isolation-propagation/](https://www.w3tutorials.net/blog/spring-transactional-isolation-propagation/)
5. [https://manish-in-java.github.io/manish-in-java/page/spring/transactions.html](https://manish-in-java.github.io/manish-in-java/page/spring/transactions.html)
6. [https://www.geeksforgeeks.org/springboot/spring-boot-transaction-management-using-transactional-annotation/](https://www.geeksforgeeks.org/springboot/spring-boot-transaction-management-using-transactional-annotation/)
7. [https://www.reddit.com/r/java/comments/pxwy0k/spring_transactional_mistakes_everyone_did/](https://www.reddit.com/r/java/comments/pxwy0k/spring_transactional_mistakes_everyone_did/)
8. [https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/tx-decl-explained.html](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/tx-decl-explained.html)
9. [https://www.tutorialspoint.com/spring/spring_transaction_management.htm](https://www.tutorialspoint.com/spring/spring_transaction_management.htm)
10. [https://ciit-training.com/en/2024/01/06/transactional-in-spring-how-it-works/](https://ciit-training.com/en/2024/01/06/transactional-in-spring-how-it-works/)
11. [https://stackoverflow.com/questions/8490852/spring-transactional-isolation-propagation](https://stackoverflow.com/questions/8490852/spring-transactional-isolation-propagation)
12. [https://rwinch.github.io/spring-framework/data-access/transaction/tx-decl-vs-prog.html](https://rwinch.github.io/spring-framework/data-access/transaction/tx-decl-vs-prog.html)
13. [https://stackoverflow.com/questions/1099025/spring-transactional-what-happens-in-background](https://stackoverflow.com/questions/1099025/spring-transactional-what-happens-in-background)
14. [https://stackoverflow.com/questions/31204194/declarative-transaction-vs-programmatic-transaction](https://stackoverflow.com/questions/31204194/declarative-transaction-vs-programmatic-transaction)
15. [https://www.tutorialspoint.com/spring/declarative_management.htm](https://www.tutorialspoint.com/spring/declarative_management.htm)
16. [https://www.geeksforgeeks.org/advance-java/transaction-propagation-and-isolation-in-spring-transactional-annotation/](https://www.geeksforgeeks.org/advance-java/transaction-propagation-and-isolation-in-spring-transactional-annotation/)
17. [https://docs.spring.io/spring-framework/reference/data-access/transaction/tx-decl-vs-prog.html](https://docs.spring.io/spring-framework/reference/data-access/transaction/tx-decl-vs-prog.html)
18. [https://www.dineshonjava.com/declarative-transaction-management/](https://www.dineshonjava.com/declarative-transaction-management/)
19. [https://www.geeksforgeeks.org/advance-java/programmatic-transaction-management-in-spring/](https://www.geeksforgeeks.org/advance-java/programmatic-transaction-management-in-spring/)
20. [https://docs.spring.vmware.com/spring-framework/reference/data-access/transaction/declarative/tx-decl-explained.html](https://docs.spring.vmware.com/spring-framework/reference/data-access/transaction/declarative/tx-decl-explained.html)

## Spring Programmatic Transaction Interview Checklist

- **Core Approaches**: TransactionTemplate (imperative callback), TransactionalOperator (reactive operator), direct PlatformTransactionManager/ReactiveTransactionManager usage.[spring](https://docs.spring.io/spring-framework/reference/data-access/transaction/programmatic.html)​
    
- **TransactionTemplate**: new TransactionTemplate(txManager).execute(TransactionCallback); thread-safe, configurable isolation/timeout/propagation; auto resource bind/commit.[spring](https://docs.spring.io/spring-framework/reference/data-access/transaction/programmatic.html)​
    
- **Reactive Tx**: TransactionalOperator.create(reactiveTxManager); mono.as(txOperator::transactional) or execute(TransactionCallback); handles cancel signals as rollback.[spring](https://docs.spring.io/spring-framework/reference/data-access/transaction/programmatic.html)​
    
- **Direct TxManager**: txManager.getTransaction(def) → status → commit/rollback; explicit name/propagation; lowest level, full control (no callback boilerplate).
    
- **Config Options**: DefaultTransactionDefinition.setIsolationLevel(), setTimeout(), setPropagationBehavior(), setReadOnly(); shared templates across methods.
    
- **Rollback Control**: status.setRollbackOnly() in callbacks; onErrorResume → rollback in reactive; manual exception handling vs @Transactional defaults.
    
- **Use Cases**: Dynamic tx boundaries (batch size loops), conditional tx (if/else), multi-tx manager routing, testing tx scenarios.[rwinch.github](https://rwinch.github.io/spring-framework/data-access/transaction/tx-decl-vs-prog.html)​
    
- **Tools/Frameworks**: R2DBC (ReactiveTransactionManager), Spring Batch (chunk-oriented), Kafka Transactions (tx-aware producers), Atomikos (programmatic XA).
    
- **Pitfalls**: Couples to Spring APIs (vs JTA UserTransaction), manual resource handling risk, forget commit/rollback, reactive full consumption needed.
    
- **vs Declarative**: Programmatic = flexible boundaries/control; Declarative = clean code/AOP; hybrid: Template inside @Transactional methods.
    
- **Architect Patterns**: Batch processing (loop + template), saga orchestration (REQUIRES_NEW per step), reactive pipelines (WebFlux + R2DBC).
    
- **Testing**: Mock TransactionStatus, verify commit/rollback calls; @Transactional(propagation=NOT_SUPPORTED) for template tests.
    

## 60-Second Recap

- Programmatic Tx: TransactionTemplate.execute() (imperative), TransactionalOperator (reactive), direct TxManager.
    
- Wins: Dynamic boundaries, batch/chunked, conditional rollback.
    
- Tools: R2DBC + Spring Batch + Kafka tx; pitfalls: manual commit, Spring coupling.
    
- Gold: Template for 80%, direct for perf-critical, reactive for WebFlux.
    

**Reference**: [Spring Programmatic Tx Docs](https://docs.spring.io/spring-framework/reference/data-access/transaction/programmatic.html) | [Declarative vs Programmatic](https://rwinch.github.io/spring-framework/data-access/transaction/tx-decl-vs-prog.html)[spring+1](https://docs.spring.io/spring-framework/reference/data-access/transaction/programmatic.html)​

1. [https://docs.spring.io/spring-framework/reference/data-access/transaction/programmatic.html](https://docs.spring.io/spring-framework/reference/data-access/transaction/programmatic.html)
2. [https://rwinch.github.io/spring-framework/data-access/transaction/tx-decl-vs-prog.html](https://rwinch.github.io/spring-framework/data-access/transaction/tx-decl-vs-prog.html)


## Spring Programmatic Transaction Rollback Interview Checklist

- **Core Mechanism**: TransactionStatus.setRollbackOnly() in TransactionTemplate callbacks marks tx for rollback; container decides at boundary (no manual commit needed).[spring+1](https://docs.spring.io/spring-framework/reference/data-access/transaction/programmatic.html)​
    
- **Imperative Rollback**: In TransactionCallback/TransactionCallbackWithoutResult: try{ business logic } catch(BusinessEx ex){ status.setRollbackOnly(); }.[matthung0807.blogspot](https://matthung0807.blogspot.com/2020/08/spring-transactiontemplate-programmatically-rollback.html)​
    
- **Reactive Rollback**: TransactionalOperator callback: .doOnError(BusinessEx.class, e -> status.setRollbackOnly()); cancel signals auto-rollback since Reactor 5.3.[spring](https://docs.spring.io/spring-framework/reference/data-access/transaction/programmatic.html)​
    
- **Direct TxManager**: After txManager.getTransaction(def), explicit txManager.rollback(status) on exceptions; full manual control.[spring](https://docs.spring.io/spring-framework/reference/data-access/transaction/programmatic.html)​
    
- **Declarative Hybrid**: Inside @Transactional method, TransactionAspectSupport.currentTransactionStatus().setRollbackOnly() for conditional rollback without exception.[stackoverflow](https://stackoverflow.com/questions/30597715/rollback-transaction-in-spring-programmatically)​
    
- **Use Cases**: Batch chunk failure (Spring Batch), validation failures, business rule violations, saga compensating actions.[baeldung](https://www.baeldung.com/spring-programmatic-transaction-management)​
    
- **Verification**: status.isRollbackOnly() checks flag; txManager.commit() skips if marked; testing via MockTransactionStatus.[doc.codeboy](https://doc.codeboy.com/JSD/spring-framework-3.2.8/javadoc-api/org/springframework/transaction/TransactionStatus.html)​
    
- **Tools/Frameworks**: Spring Batch (chunk tx rollback), R2DBC (reactive), Atomikos (XA rollback), Testcontainers (tx verification).
    
- **Pitfalls**: Forgetting setRollbackOnly() (silent commits), reactive incomplete Flux consumption, nested tx propagation confusion.[github](https://github.com/spring-projects/spring-batch/issues/2752)​
    
- **vs Declarative**: Programmatic = explicit control (no exception needed); Declarative = unchecked exceptions auto-rollback.[spring](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/rolling-back.html)​
    
- **Best Practices**: Single shared TransactionTemplate, business exceptions only (not tech), logging before rollback, metrics on rollback rate.
    
- **Testing**: @Rollback(false) + verify rollback called; custom TestTransaction for multi-step scenarios.
    

## 60-Second Recap

- Programmatic Rollback: status.setRollbackOnly() in TransactionTemplate/TransactionalOperator callbacks.
    
- Reactive: doOnError() → setRollbackOnly(); cancels auto-rollback.
    
- Wins: Conditional rollback without exceptions; pitfalls: forget flag = silent commits.
    
- Gold: Business rule rollbacks, batch failures, hybrid with @Transactional.
    

**Reference**: [Spring Programmatic Tx](https://docs.spring.io/spring-framework/reference/data-access/transaction/programmatic.html) | [Baeldung Guide](https://www.baeldung.com/spring-programmatic-transaction-management)[spring+1](https://docs.spring.io/spring-framework/reference/data-access/transaction/programmatic.html)​

1. [https://docs.spring.io/spring-framework/reference/data-access/transaction/programmatic.html](https://docs.spring.io/spring-framework/reference/data-access/transaction/programmatic.html)
2. [https://docs.spring.io/spring-framework/reference/data-access/transaction/programmatic.html](https://docs.spring.io/spring-framework/reference/data-access/transaction/programmatic.html)
3. [https://matthung0807.blogspot.com/2020/08/spring-transactiontemplate-programmatically-rollback.html](https://matthung0807.blogspot.com/2020/08/spring-transactiontemplate-programmatically-rollback.html)
4. [https://stackoverflow.com/questions/30597715/rollback-transaction-in-spring-programmatically](https://stackoverflow.com/questions/30597715/rollback-transaction-in-spring-programmatically)
5. [https://www.baeldung.com/spring-programmatic-transaction-management](https://www.baeldung.com/spring-programmatic-transaction-management)
6. [https://doc.codeboy.com/JSD/spring-framework-3.2.8/javadoc-api/org/springframework/transaction/TransactionStatus.html](https://doc.codeboy.com/JSD/spring-framework-3.2.8/javadoc-api/org/springframework/transaction/TransactionStatus.html)
7. [https://github.com/spring-projects/spring-batch/issues/2752](https://github.com/spring-projects/spring-batch/issues/2752)
8. [https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/rolling-back.html](https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/rolling-back.html)
9. [https://www.appsloveworld.com/springmvc/100/3/best-practices-for-rolling-back-transactions-in-spring-3-hibernate](https://www.appsloveworld.com/springmvc/100/3/best-practices-for-rolling-back-transactions-in-spring-3-hibernate)
10. [https://docs.spring.vmware.com/spring-framework/reference/data-access/transaction/programmatic.html](https://docs.spring.vmware.com/spring-framework/reference/data-access/transaction/programmatic.html)
11. [https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/transaction/TransactionStatus.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/transaction/TransactionStatus.html)
12. [https://codingtechroom.com/question/-spring-transaction-rollback-exception](https://codingtechroom.com/question/-spring-transaction-rollback-exception)
13. [https://spring.pleiades.io/spring-framework/reference/data-access/transaction/programmatic.html](https://spring.pleiades.io/spring-framework/reference/data-access/transaction/programmatic.html)
14. [https://stackoverflow.com/questions/12194891/rollback-a-transactional-annotated-method](https://stackoverflow.com/questions/12194891/rollback-a-transactional-annotated-method)
15. [https://stackoverflow.com/questions/4402506/best-practices-for-rolling-back-transactions-in-spring-3-hibernate](https://stackoverflow.com/questions/4402506/best-practices-for-rolling-back-transactions-in-spring-3-hibernate)
16. [https://www.marcobehler.com/guides/spring-transaction-management-transactional-in-depth](https://www.marcobehler.com/guides/spring-transaction-management-transactional-in-depth)
17. [https://docs.spring.io/spring-framework/docs/4.2.x/spring-framework-reference/html/transaction.html](https://docs.spring.io/spring-framework/docs/4.2.x/spring-framework-reference/html/transaction.html)
18. [https://stackoverflow.com/questions/69329687/how-to-set-norollbackfor-on-a-transactiontemplate](https://stackoverflow.com/questions/69329687/how-to-set-norollbackfor-on-a-transactiontemplate)
19. [http://www.java2s.com/example/java-api/org/springframework/transaction/transactionstatus/setrollbackonly-0-1.html](http://www.java2s.com/example/java-api/org/springframework/transaction/transactionstatus/setrollbackonly-0-1.html)
20. [https://www.spring-certification.com/post/mastering-transaction-management-in-spring-tests-rollback-commit-and-best-practices](https://www.spring-certification.com/post/mastering-transaction-management-in-spring-tests-rollback-commit-and-best-practices)
21. [https://dev.to/wynnt3o/spring-transactional-rollback-handling-hc8](https://dev.to/wynnt3o/spring-transactional-rollback-handling-hc8)
# **Spring Transaction Management Summary**

## **Article 1: Spring Framework Transaction Overview**
**Source:** https://docs.spring.io/spring-framework/reference/data-access/transaction.html

### **1. Transaction Abstraction Layer**
- **PlatformTransactionManager:** Core interface for transaction management
- **Key Implementations:**
  - `DataSourceTransactionManager` (JDBC)
  - `JpaTransactionManager` (JPA)
  - `JtaTransactionManager` (JTA for distributed transactions)
- **TransactionDefinition:** Interface defining transaction properties
- **TransactionStatus:** Interface for controlling transaction execution

### **2. Transaction Synchronization**
- **Resource Synchronization:** Binds resources (JDBC connections) to transactions
- **Callbacks:** `TransactionSynchronizationManager` for custom synchronization
- **Thread-bound Resources:** Resources are bound to current thread
- **Cleanup:** Automatic resource cleanup on transaction completion

### **3. Declarative vs Programmatic**
- **Declarative:** `@Transactional` annotations (recommended for most cases)
- **Programmatic:** `TransactionTemplate` or `PlatformTransactionManager` API
- **XML Configuration:** Legacy approach with `<tx:advice>` and AOP

---

## **Article 2: Declarative Transaction Management Explained**
**Source:** https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/tx-decl-explained.html

### **1. @Transactional Annotation**
- **Implementation:** Uses Spring AOP proxy mechanism
- **Placement:** On interface, class, or method level
- **Method Visibility:** Only public methods can be transactional
- **Self-invocation Limitation:** Internal method calls bypass proxy

### **2. Proxy-based Transaction Management**
- **AOP Proxy:** Wraps bean to add transaction boundaries
- **Advice:** `TransactionInterceptor` handles transaction logic
- **Order:** Transaction advisor typically has highest precedence

### **3. Transaction Attribute Sources**
- **AnnotationTransactionAttributeSource:** Reads `@Transactional` attributes
- **Method-based Attributes:** Method-level overrides class-level
- **Interface vs Implementation:** Prefer implementation for clarity

### **4. Transaction Interceptor Workflow**
1. Check existing transaction context
2. Create new transaction if needed
3. Bind resources to transaction
4. Execute business method
5. Commit/rollback based on outcome
6. Clean up thread-bound resources

---

## **Article 3: Transaction Propagation**
**Source:** https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/tx-propagation.html

### **1. Propagation Behaviors**
- **REQUIRED (Default):** Use existing transaction or create new
  ```java
  @Transactional(propagation = Propagation.REQUIRED)
  ```
- **REQUIRES_NEW:** Always create new transaction, suspend existing
- **NESTED:** Create nested transaction within existing (savepoint-based)
- **SUPPORTS:** Execute with transaction if exists, otherwise non-transactional
- **NOT_SUPPORTED:** Execute non-transactionally, suspend existing
- **NEVER:** Throw exception if transaction exists
- **MANDATORY:** Must have existing transaction, throw exception otherwise

### **2. Propagation Scenarios**
- **REQUIRED:** Most common, ensures atomicity
- **REQUIRES_NEW:** For audit logs, independent operations
- **NESTED:** Partial rollback capability (JDBC only)
- **SUPPORTS:** Read operations that can work with or without transaction

### **3. Transaction Suspension**
- **When occurs:** `REQUIRES_NEW`, `NOT_SUPPORTED`, `NEVER`
- **Resource handling:** Resources suspended, not available in inner method
- **Resume:** Original transaction resumed after inner method completes

---

## **Article 4: Programmatic Transaction Management**
**Source:** https://docs.spring.io/spring-framework/reference/data-access/transaction/programmatic.html

### **1. TransactionTemplate**
- **Callback Pattern:** Execute code within transaction boundaries
  ```java
  transactionTemplate.execute(status -> {
      // business logic
      return result;
  });
  ```
- **Benefits:** Full control, no proxy limitations
- **Drawbacks:** More verbose, mixes transaction and business code

### **2. PlatformTransactionManager Direct Usage**
- **Manual Control:**
  ```java
  TransactionStatus status = transactionManager.getTransaction(definition);
  try {
      // business logic
      transactionManager.commit(status);
  } catch (Exception e) {
      transactionManager.rollback(status);
      throw e;
  }
  ```
- **Use Cases:** Complex transaction scenarios, batch processing

### **3. TransactionCallback Interfaces**
- `TransactionCallback<T>`: Returns result
- `TransactionCallbackWithoutResult`: Void operations
- `TransactionCallback<T>` is preferred over `TransactionCallbackWithoutResult`

### **4. When to Use Programmatic**
- **Fine-grained control:** Dynamic transaction boundaries
- **Complex logic:** Conditional transaction requirements
- **Performance critical:** Avoid proxy overhead
- **Legacy code:** Integration with non-Spring code

---

## **Article 5: Transaction Rollback Rules**
**Source:** https://docs.spring.io/spring-framework/reference/data-access/transaction/declarative/rolling-back.html

### **1. Default Rollback Behavior**
- **Checked Exceptions:** No rollback (IOException, SQLException)
- **Runtime Exceptions:** Rollback (RuntimeException and subclasses)
- **Errors:** Rollback (VirtualMachineError, etc.)

### **2. Custom Rollback Rules**
- **rollbackFor:** Specify exceptions that trigger rollback
  ```java
  @Transactional(rollbackFor = {IOException.class, MyBusinessException.class})
  ```
- **noRollbackFor:** Specify exceptions that don't trigger rollback
  ```java
  @Transactional(noRollbackFor = {OptimisticLockingFailureException.class})
  ```

### **3. Understanding Rollback Semantics**
- **Commit:** If method completes normally (no exception thrown)
- **Rollback:** If runtime exception or specified checked exception
- **Partial Rollback:** Nested transactions with savepoints (JDBC)

### **4. Rollback in Propagation Scenarios**
- **REQUIRED/REQUIRES_NEW:** Rollback of inner affects outer based on exception type
- **NESTED:** Inner rollback doesn't affect outer (savepoint)
- **Transaction Synchronization:** Callbacks executed on rollback

---

## **60-Second Recap for Interview**

1. **Two Approaches:** Declarative (`@Transactional`) and Programmatic (`TransactionTemplate`)
2. **Propagation:** `REQUIRED` (default), `REQUIRES_NEW`, `NESTED`, `SUPPORTS`, etc.
3. **Rollback Rules:** Runtime exceptions rollback, checked exceptions don't (configurable)
4. **Implementation:** AOP proxy-based, public methods only, self-invocation problem
5. **Platform Abstraction:** `PlatformTransactionManager` works with JDBC, JPA, JTA

---

## **Interview Checklist**

### **Core Concepts:**
- [ ] Can explain difference between declarative and programmatic transactions
- [ ] Knows all 7 propagation behaviors and when to use each
- [ ] Understands default rollback behavior and how to customize it
- [ ] Can explain self-invocation problem and solutions
- [ ] Knows how transaction resources are synchronized to threads

### **Implementation Details:**
- [ ] Can configure `@Transactional` with proper attributes
- [ ] Knows how to use `TransactionTemplate` for programmatic control
- [ ] Understands transaction manager selection for different data access technologies
- [ ] Can handle nested transaction scenarios correctly
- [ ] Knows how to test transactional methods

### **Scenario Response:**
**If asked about transaction not working:**
1. "Check method visibility - must be public"
2. "Verify it's not a self-invocation issue (method called from within same class)"
3. "Ensure exception type triggers rollback (runtime vs checked)"
4. "Check propagation behavior - maybe method running without transaction"
5. "Verify proxy creation - is bean properly advised?"

**If asked about choosing propagation:**
1. "Most cases: REQUIRED (default)"
2. "Independent operations (like audit logging): REQUIRES_NEW"
3. "Read-only operations that don't need transaction: SUPPORTS"
4. "Partial rollback needed: NESTED (JDBC only)"
5. "Must have existing transaction: MANDATORY"

**If asked about performance optimization:**
1. "Use readOnly=true for query methods"
2. "Set appropriate timeout to prevent long-running transactions"
3. "Consider propagation=SUPPORTS for read-only operations without transaction"
4. "Batch operations in single transaction when possible"
5. "Avoid unnecessarily large transaction boundaries"

### **Common Interview Questions Prepared:**
- **"@Transactional how does it work?"**
  - "Spring creates AOP proxy that wraps method with TransactionInterceptor, managing begin/commit/rollback"

- **"REQUIRED vs REQUIRES_NEW?"**
  - "REQUIRED participates in existing transaction or creates new, REQUIRES_NEW always creates new and suspends existing"

- **"When does rollback occur?"**
  - "By default on RuntimeException and Error, not on checked exceptions - customizable with rollbackFor/noRollbackFor"

- **"Self-invocation problem?"**
  - "Internal method calls bypass proxy so @Transactional doesn't work - refactor or use AspectJ"

### **Architecture & Design:**
- [ ] **Transaction Boundaries:** "Define at service layer, not DAO/repository"
- [ ] **Exception Strategy:** "Define consistent rollback rules across application"
- [ ] **Testing Strategy:** "Test rollback behavior, isolation levels, propagation scenarios"
- [ ] **Monitoring:** "Track long-running transactions, rollback rates"
- [ ] **Distributed Transactions:** "Consider JTA for multiple resources, or eventual consistency patterns"

### **Key Phrases to Use:**
- "Spring's transaction abstraction provides consistent programming model across different persistence technologies"
- "Propagation behaviors control how transactions interact when methods call each other"
- "The self-invocation limitation is due to proxy-based AOP, not a Spring transaction bug"
- "Always set readOnly=true for query methods to allow database optimizations"
- "Nested transactions with savepoints are database-dependent and work differently across vendors"
- "Programmatic transactions give you fine-grained control but mix transaction and business logic"

---

## **Transaction Configuration Matrix**

| **Scenario** | **Propagation** | **Isolation** | **Timeout** | **Read Only** |
|-------------|----------------|---------------|-------------|---------------|
| **Write operation** | REQUIRED | DEFAULT | 30s | false |
| **Read operation** | SUPPORTS | READ_COMMITTED | - | true |
| **Audit logging** | REQUIRES_NEW | DEFAULT | 5s | false |
| **Batch processing** | REQUIRED | DEFAULT | 300s | false |
| **Report generation** | NOT_SUPPORTED | - | - | - |

## **Common Transaction Pitfalls & Solutions**
1. **Self-invocation:** Refactor to separate beans or use AspectJ
2. **Long transactions:** Set appropriate timeout, break into smaller units
3. **Deadlocks:** Use proper isolation levels, access resources in consistent order
4. **Connection leaks:** Ensure proper resource cleanup, use connection pooling
5. **Inconsistent rollback:** Define clear rollback rules, test exception scenarios

**Remember:** Transactions are about consistency, not performance. They add overhead but ensure data integrity!


# **Spring Transaction Strategies Summary**

**Source:** https://docs.spring.io/spring-framework/reference/data-access/transaction/strategies.html

## **1. PlatformTransactionManager Implementations**

### **A. JDBC Transactions**
- **DataSourceTransactionManager:**
  - For plain JDBC or MyBatis
  - Works with any `javax.sql.DataSource`
  - **Configuration:**
    ```xml
    <bean id="transactionManager" 
          class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    ```
    ```java
    @Bean
    public PlatformTransactionManager transactionManager(DataSource dataSource) {
        return new DataSourceTransactionManager(dataSource);
    }
    ```

### **B. JPA/Hibernate Transactions**
- **JpaTransactionManager:**
  - For JPA applications
  - Integrates with `EntityManagerFactory`
  - **Configuration:**
    ```java
    @Bean
    public PlatformTransactionManager transactionManager(EntityManagerFactory emf) {
        return new JpaTransactionManager(emf);
    }
    ```
- **HibernateTransactionManager:**
  - For native Hibernate (without JPA)
  - Works with `SessionFactory`
  - **Note:** Use `JpaTransactionManager` for JPA, even with Hibernate as provider

### **C. Distributed Transactions**
- **JtaTransactionManager:**
  - For JTA (Java Transaction API)
  - Supports multiple resources (XA transactions)
  - **Use Cases:** Multiple databases, JMS + database
  - **Configuration:**
    ```java
    @Bean
    public PlatformTransactionManager transactionManager(UserTransaction ut, 
                                                         TransactionManager tm) {
        return new JtaTransactionManager(ut, tm);
    }
    ```

### **D. Other Transaction Managers**
- **CciLocalTransactionManager:** For CCI (Common Client Interface)
- **WebLogic/WebSphere specific managers:** For app server integration

## **2. Transaction Manager Selection Criteria**

### **A. Single Database Application**
- **Recommended:** `DataSourceTransactionManager` (JDBC) or `JpaTransactionManager` (JPA)
- **No JTA needed:** Single resource doesn't require distributed transactions
- **Simpler:** Less overhead, easier configuration

### **B. Multiple Resources (Database + JMS)**
- **Option 1:** JTA with `JtaTransactionManager`
- **Option 2:** Chained transactions (not atomic)
- **Option 3:** Compensating transactions pattern
- **Trade-off:** Atomicity vs complexity

### **C. Application Server vs Standalone**
- **Application Server:** Use server's JTA implementation
- **Standalone:** Use embedded JTA provider (Atomikos, Bitronix)
- **Spring Boot:** Auto-configures based on dependencies

## **3. JTA vs Resource-local Transactions**

| **Aspect** | **JTA** | **Resource-local** |
|------------|---------|-------------------|
| **Multiple Resources** | Supported | Not supported |
| **XA Protocol** | Required | Not needed |
| **Performance** | Higher overhead | Lower overhead |
| **Complexity** | More complex | Simpler |
| **Recovery** | Two-phase commit | Manual recovery |

## **4. Configuration Strategies**

### **A. XML Configuration (Legacy)**
```xml
<!-- JDBC -->
<bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"/>
</bean>

<!-- JPA -->
<bean id="txManager" class="org.springframework.orm.jpa.JpaTransactionManager">
    <property name="entityManagerFactory" ref="entityManagerFactory"/>
</bean>
```

### **B. Java Configuration**
```java
@Configuration
@EnableTransactionManagement
public class PersistenceConfig {
    
    @Bean
    public PlatformTransactionManager transactionManager(DataSource dataSource) {
        return new DataSourceTransactionManager(dataSource);
    }
    
    // OR for JPA
    @Bean
    public PlatformTransactionManager transactionManager(EntityManagerFactory emf) {
        return new JpaTransactionManager(emf);
    }
}
```

### **C. Spring Boot Auto-configuration**
- **Detects dependencies:** Automatically configures appropriate transaction manager
- **JDBC:** `DataSourceTransactionManager`
- **JPA:** `JpaTransactionManager`
- **JTA:** Auto-detects when JTA dependencies present
- **Override:** Define your own `PlatformTransactionManager` bean

## **5. Transaction Synchronization**

### **A. How Synchronization Works**
- **Resource Binding:** Connections bound to current thread via `TransactionSynchronizationManager`
- **Lookup Pattern:** `DataSourceUtils.getConnection()` instead of `dataSource.getConnection()`
- **Cleanup:** Automatic on transaction completion

### **B. Manual Resource Access**
```java
// Without Spring
Connection conn = dataSource.getConnection();

// With Spring transaction synchronization
Connection conn = DataSourceUtils.getConnection(dataSource);
```

### **C. Custom Synchronization**
```java
TransactionSynchronizationManager.registerSynchronization(
    new TransactionSynchronization() {
        @Override
        public void afterCommit() {
            // custom logic after commit
        }
    }
);
```

## **6. Testing Transaction Managers**

### **A. Integration Testing**
```java
@SpringBootTest
@Transactional
public class TransactionalTest {
    
    @Test
    @Rollback
    public void testTransactionalMethod() {
        // test code - transaction rolled back after test
    }
}
```

### **B. Manual Testing**
```java
@Test
public void testTransactionManager() {
    TransactionStatus status = transactionManager.getTransaction(
        new DefaultTransactionDefinition()
    );
    try {
        // business logic
        transactionManager.commit(status);
    } catch (Exception e) {
        transactionManager.rollback(status);
    }
}
```

## **7. Performance Considerations**

### **A. Connection Pooling**
- **Essential for performance:** Use HikariCP, Tomcat JDBC, etc.
- **Configure properly:** Based on workload (OLTP vs batch)
- **Monitor:** Active/idle connections, wait times

### **B. Transaction Isolation Impact**
- **READ_UNCOMMITTED:** Fastest, least safe
- **READ_COMMITTED:** Good balance (default)
- **REPEATABLE_READ/SERIALIZABLE:** Slower, safer

### **C. Long-running Transactions**
- **Problem:** Hold locks, block other transactions
- **Solution:** Break into smaller transactions, optimize queries
- **Timeout:** Always set reasonable timeout

## **8. Migration Strategies**

### **A. Adding Transactions to Legacy Code**
1. Start with `@Transactional` on service methods
2. Use `DataSourceTransactionManager` for JDBC
3. Test rollback behavior
4. Gradually increase transaction boundaries

### **B. Switching Transaction Managers**
1. **JDBC → JPA:** Change to `JpaTransactionManager`
2. **Single → Multiple resources:** Consider JTA if atomicity needed
3. **App Server migration:** May need to switch JTA providers

### **C. Distributed Transaction Patterns**
1. **Saga Pattern:** Compensating transactions
2. **Eventual Consistency:** Accept temporary inconsistency
3. **Two-phase commit:** JTA for strict consistency

---

## **60-Second Recap for Interview**

1. **Manager Selection:** `DataSourceTransactionManager` (JDBC), `JpaTransactionManager` (JPA), `JtaTransactionManager` (distributed)
2. **Single vs Multiple Resources:** JTA needed for atomic transactions across multiple resources
3. **Auto-configuration:** Spring Boot picks manager based on dependencies
4. **Synchronization:** Resources bound to thread, cleaned up automatically
5. **Testing:** Use `@Transactional` in tests for automatic rollback

---

## **Interview Checklist**

### **Transaction Manager Knowledge:**
- [ ] Can name 3+ `PlatformTransactionManager` implementations
- [ ] Knows when to use JTA vs resource-local transactions
- [ ] Understands transaction synchronization mechanism
- [ ] Can configure different managers in Spring Boot
- [ ] Knows performance implications of each manager

### **Implementation Scenarios:**
**If asked about multiple databases:**
1. "If atomicity required: JTA with XA transactions"
2. "If eventual consistency acceptable: Chained transactions with compensation"
3. "Consider if truly need atomicity or can accept partial failure"
4. "Evaluate performance impact of JTA vs simpler approaches"

**If asked about migration from JDBC to JPA:**
1. "Change from `DataSourceTransactionManager` to `JpaTransactionManager`"
2. "Update configuration to reference `EntityManagerFactory`"
3. "Test transaction boundaries still work correctly"
4. "Verify connection pooling still efficient"

**If asked about transaction timeout:**
1. "Set at method level: `@Transactional(timeout = 30)`"
2. "Configure in `TransactionTemplate` for programmatic"
3. "Consider different timeouts for different operations"
4. "Monitor for timeout exceptions in production"

### **Common Interview Questions Prepared:**
- **"Which transaction manager for JPA?"**
  - "`JpaTransactionManager` - it works with JPA's `EntityManagerFactory`"

- **"When do you need JTA?"**
  - "When you need atomic transactions across multiple resources (multiple databases, JMS + DB)"

- **"How does Spring synchronize transactions?"**
  - "Through `TransactionSynchronizationManager` which binds resources to thread-local storage"

- **"Spring Boot transaction auto-configuration?"**
  - "Detects dependencies and configures appropriate manager (JPA → JpaTransactionManager, etc.)"

### **Architecture Decisions:**
- [ ] **Distributed Transactions:** "JTA for atomicity, Saga pattern for flexibility"
- [ ] **Performance:** "Resource-local faster than JTA, but limited to single resource"
- [ ] **Testing:** "Always test with actual transaction manager, not mocks"
- [ ] **Monitoring:** "Track transaction duration, rollback rates, timeout occurrences"
- [ ] **Cloud Considerations:** "JTA more complex in cloud, consider eventual consistency"

### **Key Phrases to Use:**
- "Choose transaction manager based on persistence technology, not personal preference"
- "JTA provides atomicity across resources but adds complexity and performance overhead"
- "Transaction synchronization is key to Spring's resource management - never get connections directly from DataSource"
- "Spring Boot's auto-configuration simplifies setup but know how to override when needed"
- "Always consider the trade-off between transaction scope and performance"

---

## **Transaction Manager Selection Flowchart**

**What persistence technology?**
- Plain JDBC/MyBatis → `DataSourceTransactionManager`
- JPA (Hibernate/EclipseLink) → `JpaTransactionManager`
- Native Hibernate → `HibernateTransactionManager`
- Multiple resources (XA) → `JtaTransactionManager`

**Need distributed transactions?**
- Single database → No JTA needed
- Multiple databases with atomic requirement → JTA
- Multiple resources, eventual consistency OK → Consider Saga pattern

**Application environment?**
- Spring Boot → Use auto-configuration, override if needed
- Application server → May use server's JTA implementation
- Standalone → Use embedded JTA if needed

**Remember:** The simplest transaction manager that meets your requirements is usually the best choice. Don't use JTA unless you truly need distributed transactions!

