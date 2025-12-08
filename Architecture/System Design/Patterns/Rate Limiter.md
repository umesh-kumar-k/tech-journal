## Rate Limiting Pattern

Rate Limiting proactively respects throttled service capacity by controlling request rates, using durable queues + distributed locks to avoid naive retry storms and predict throughput.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/rate-limiting-pattern)​

- Buffer work in high-capacity queues (Event Hubs); process at service's throttle limit
    
- Distributed mutual exclusion (blob leases) coordinates multiple consumers sharing capacity
    
- Granular releases (e.g., 20/sec → 100ms intervals) smooths load vs bursty batches[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/rate-limiting-pattern)​
    

## Tools and Frameworks

|Component|Tools/Frameworks|Example Use Case|
|---|---|---|
|Durable Queue|Azure Event Hubs, Service Bus, Queue Storage|Buffer 10K Cosmos writes beyond 20K RU/s limit|
|Distributed Locks|Azure Storage blob leases, Redis Redlock|20 partitions × 100 req/s = 2K req/s shared capacity|
|Rate Limiters|Guava RateLimiter (Java), Bottleneck (Node), Leaky Bucket algos|Token bucket per partition|
|Languages|Go/Java GitHub impls|Multi-process DB ingestion coordinators|

## Interview Checklist

- Define: Proactive throttling respect vs reactive retry; queue → controlled processing[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/rate-limiting-pattern)​
    
- Problem: Naive retry → 3x traffic (10K records → 30K attempts for 20K RU/s limit)
    
- Architecture: High-throughput queue → processors acquire capacity leases → process batch
    
- Coordination: Blob leases (15s) grant partition capacity; random partition selection
    
- Granularity: Frequent small batches (200ms intervals) vs second/minute bursts
    
- Multi-process: Shared capacity partitions; reserved + contested pools
    
- Monitoring: Queue depth, lease contention, throttle errors, predicted completion
    
- When: Known service limits, batch jobs, multi-client coordination
    

## 60-Second Recap

- **Core**: Queue → lease capacity partitions → controlled processing rate[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/rate-limiting-pattern)​
    
- **Why**: Avoid retry storms (10K→30K requests); predict throughput
    
- **Coordination**: Blob leases = capacity tokens; random partitions reduce contention
    
- **Tools**: Event Hubs + Storage leases; Guava limiter; Go/Java impls
    
- **Math**: 2K req/s = 20 partitions × 100 req/s; smooth 200ms intervals
    

**Reference**: [https://learn.microsoft.com/en-us/azure/architecture/patterns/rate-limiting-pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/rate-limiting-pattern)[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/rate-limiting-pattern)​

1. [https://learn.microsoft.com/en-us/azure/architecture/patterns/rate-limiting-pattern](https://learn.microsoft.com/en-us/azure/architecture/patterns/rate-limiting-pattern)