These are **NOT** basic @Entity or save() questions. Interviewers (usually Distinguished Engineers or Principal Architects) expect you to have **broken, fixed, and scaled Hibernate at millions of TPS**, and to know when **NOT** to use it.

### 1. Hibernate 6.x + Jakarta EE 9+ + Java 17–21 Internals (Mandatory)
- Major breaking changes from Hibernate 5 → 6.2+ (new metamodel, no more bytecode enhancement via Maven plugin, new attribute converter rules)
- How Hibernate 6 generates the static metamodel differently with the new processor
- Impact of records/sealed classes/virtual threads on entity design and bytecode enhancement

### 2. Second-Level Cache Architecture at Scale
- Real production setup with distributed 2nd-level cache (Infinispan clustered vs Hazelcast vs Redis + RLEC)
- Cache concurrency strategies deep dive: READ_WRITE with soft-lock vs NONSTRICT_READ_WRITE vs READ_ONLY real trade-offs
- Cache invalidation storms you have survived and fixed (thundering herd, hot entity explosion)
- Query cache pitfalls and why most companies disable it at scale
- Natural id cache + entity cache coordination

### 3. N+1 Problem Detection & Elimination at Scale
- How you detect N+1 in production with 1000+ microservices (Datadog APM, custom ByteMan/AOP, Hibernate statistics MBeans)
- EntityGraph vs Fetch Join vs Blaze-Persistence vs JOIN FETCH pagination issues
- DTO projection vs entity vs Blaze-Persistence CTA (Criteria Tree API) performance comparison at 100k+ RPS

### 4. Connection Pool + Transaction Management Nightmares
- HikariCP + Hibernate connection leak detection in depth (leakDetectionThreshold real cases)
- Hibernate + multiple DataSources + JTA (Narayana/Bitronix/Atomikos) vs Spring @Transactional with virtual threads
- Long-running transactions causing connection pool exhaustion scenarios you fixed

### 5. Performance & GC Tuning with Hibernate
- Session vs StatelessSession vs bulk operations (real 10M-row insert/update benchmarks)
- Hibernate + GC pauses: how large session or dirty-checking caused minutes of stop-the-world
- Dirty-checking overhead mitigation (bytecode enhancement, @SelectBeforeUpdate, selective dirty tracking)
- Impact of collection types (Bag vs Indexed List vs Set) on memory and performance

### 6. Concurrency & Optimistic Locking at Scale
- Optimistic locking with @Version on high-contention entities (version conflicts at 10k+ TPS)
- PESSIMISTIC_WRITE vs PESSIMISTIC_READ with deadlocks you have debugged
- Custom optimistic lock modes without @Version (manual timestamp + WHERE clause)

### 7. Schema Evolution & Migration Strategies
- Liquibase vs Flyway vs Hibernate DDL auto in production (why everyone bans hbm2ddl)
- Zero-downtime schema migration patterns with 10TB+ databases
- How you handled adding a NOT NULL column with default value on a 500M-row table

### 8. Sharding & Multi-Tenancy Architectures
- Horizontal sharding with Hibernate (custom ShardResolver + MultiTenantConnectionProvider)
- Schema-based vs discriminator-based vs shared schema with client_id filter
- CITUS, Vitess, or YugabyteDB integration experience with Hibernate

### 9. When NOT to Use Hibernate (Critical Architect Question)
- Scenarios where you replaced Hibernate with JOOQ, Spring JdbcTemplate, or pure JDBC
- High-frequency trading or payment systems where Hibernate was too slow
- Real examples of moving from Hibernate to reactive R2DBC or jOOQ + Reactor

### 10. Hibernate Reactive & Vert.x (Emerging but Frequently Asked)
- Hibernate Reactive with Panache vs traditional Hibernate differences
- Real production deployment of Hibernate Reactive on PostgreSQL/MongoDB

### 11. Observability & Debugging War Stories
- Hibernate statistics + slow query log + custom JDBC proxy for query fingerprinting
- How you reduced query count from 10,000 → 200 per request
- Fixed a major OutOfMemory caused by Hibernate (e.g., Cartesian product in lazy loading)

### Expected Real-War Answers (You Must Have Stories)
- Reduced average Hibernate query time from 800ms → 12ms
- Survived Black Friday with 2nd-level cache cluster meltdown and recovery
- Migrated 800 entities from Hibernate 5 → 6 with zero downtime
- Fixed a production incident caused by N+1 that took down the entire service
- Achieved < 5ms p99 latency on read-heavy services using Hibernate + caching

### Key Books / Resources Architects Actually Read
- “Java Persistence with Hibernate 2nd Edition” (2015) – still the bible for internals
- “High-Performance Java Persistence” – Vlad Mihalcea (must know by heart)
- Hibernate 6 release notes + GitHub issues (especially bytecode enhancement removal)
- Vlad Mihalcea’s blog (hypersistence.io) – advanced articles

If you can confidently discuss the above with **real production numbers, trade-offs, and incidents you personally fixed**, you will dominate any Hibernate architect interview at the highest level in 2025–2026.

Most candidates with 15+ years still talk about @OneToMany(fetch = LAZY). The bar for true architects is orders of magnitude higher.