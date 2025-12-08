## Retry Pattern

The Retry pattern transparently reattempts transient failures (network issues, timeouts, throttling) with configurable delays, improving application resilience without user intervention.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/retry)​

- Strategies: Cancel (non-transient), immediate retry (rare faults), delay retry (busy/throttled)
    
- Exponential backoff spreads retries; log early attempts as info, final failure as error
    
- Pair with Circuit Breaker for long failures; ensure idempotency to avoid duplicate side effects[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/retry)​
    

## Tools and Frameworks

|Language/Ecosystem|Tools/Frameworks|Example Use Case|
|---|---|---|
|.NET|Polly (RetryPolicy), Azure SDK built-in retry|HTTP calls, Cosmos DB operations with exponential backoff|
|Java|Resilience4j Retry, Spring Retry|REST clients, database connections|
|Node.js|retry-axios, async-retry|API calls with jittered delays|
|Python|tenacity, requests.adapters|Cloud service SDKs, database queries|
|Go|github.com/cenkalti/backoff|gRPC/HTTP clients|

## Interview Checklist

- Define: Reattempt transient faults (network, timeout, throttling) with delays[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/retry)​
    
- Strategies: Immediate (rare), fixed delay, exponential backoff + jitter (thundering herd)
    
- Exceptions: Distinguish transient vs permanent; log info (early) vs error (final)
    
- Idempotency: Safe to retry? (GET ok, POST/PUT may duplicate charges)
    
- Config: Max attempts, base delay, max delay, retry count per exception type
    
- Pairing: Circuit Breaker (stop retries), Bulkhead (concurrency limits)
    
- Gotchas: Overload busy services, transaction consistency, nested retry loops
    
- Testing: Chaos engineering, fault injection for retry scenarios
    

## 60-Second Recap

- **Core**: Transient fault → delay → retry (exponential backoff); fail after max attempts[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/retry)​
    
- **Why**: Handle cloud flakiness (network, throttling) transparently
    
- **Strategies**: Fixed/exponential delay + jitter; Circuit Breaker for persistent failures
    
- **Tools**: Polly (.NET), Resilience4j (Java), tenacity (Python), Azure SDK built-in
    
- **Design**: Idempotent ops only; log early=info/final=error; test with chaos
    

**Reference**: [https://learn.microsoft.com/en-us/azure/architecture/patterns/retry](https://learn.microsoft.com/en-us/azure/architecture/patterns/retry)[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/retry)​

1. [https://learn.microsoft.com/en-us/azure/architecture/patterns/retry](https://learn.microsoft.com/en-us/azure/architecture/patterns/retry)

# **Retry Pattern**

## **Core Thesis**
The Retry pattern enables applications to handle transient failures when connecting to remote services or network resources by transparently retrying failed operations, improving application stability and resilience without requiring complex failure recovery logic in business code.

---

## **Detailed Notes**

### **1. Problem Statement & Context**

**Transient Fault Characteristics:**
1. **Temporary Nature:** Failures that are self-correcting
2. **Intermittent Occurrence:** Not consistent or predictable
3. **External Dependencies:** Often in network or cloud services
4. **Examples:**
   - Network connectivity loss (few seconds)
   - Service temporarily overloaded
   - Database connection timeout
   - HTTP 429 (Too Many Requests) or 503 (Service Unavailable)

**Without Retry Pattern Issues:**
- **User Experience:** Immediate failure for temporary issues
- **Resource Inefficiency:** Manual retry logic scattered in code
- **Inconsistent Behavior:** Different retry strategies across application
- **Maintenance Complexity:** Hard to update retry logic globally

### **2. Core Implementation Strategies**

**Basic Retry Logic Flow:**
```
[Operation Attempt] → [Success?] → Yes → [Return Result]
         ↓ No
    [Check Retry Policy]
         ↓
    [Wait (Backoff)] → [Increment Attempt Count]
         ↓
    [Max Retries Reached?] → Yes → [Throw Exception]
         ↓ No
    [Retry Operation]
```

**Key Components to Implement:**
1. **Retry Counter:** Track number of attempts
2. **Delay Calculator:** Determine wait time between retries
3. **Exception Filter:** Identify which exceptions to retry
4. **Result Validator:** Check if response is acceptable

### **3. Backoff Strategy Types**

**Common Backoff Algorithms:**
| **Strategy** | **Formula** | **Best For** | **Trade-offs** |
|--------------|-------------|--------------|----------------|
| **Fixed Delay** | Wait = Constant | Simple scenarios | May overload recovering service |
| **Incremental** | Wait = Base × Attempt | Moderate load | Predictable pattern |
| **Exponential** | Wait = 2^(Attempt-1) × Base | Cloud services | Rapidly increasing wait times |
| **Randomized** | Wait = Random(Base, Max) | High contention | Unpredictable but fair |
| **Jittered Exp** | Wait = Exp + Random(-J, +J) | Distributed systems | Combines benefits of exp + random |

**Example Exponential Backoff:**
```csharp
// Base delay: 100ms, max delay: 30 seconds
Attempt 1: 100ms
Attempt 2: 200ms  
Attempt 3: 400ms
Attempt 4: 800ms
Attempt 5: 1.6 seconds
Attempt 6: 3.2 seconds
...
```

### **4. Implementation Details**

**Configuration Parameters:**
```yaml
retryCount: 5                    # Maximum retry attempts
minBackoff: "00:00:00.1"         # Minimum delay (100ms)
maxBackoff: "00:00:30"           # Maximum delay (30 seconds)
deltaBackoff: "00:00:01"         # Incremental increase
firstFastRetry: true             # Immediate first retry
```

**Exception Classification:**
```
Retryable Exceptions:
• TimeoutException
• HttpRequestException (HTTP 5xx)
• SocketException
• SqlException (transient error codes)

Non-Retryable Exceptions:
• ArgumentException (client error)
• AuthenticationException
• NotImplementedException
• Business logic exceptions
```

**Implementation Template:**
```csharp
public async Task<T> RetryAsync<T>(
    Func<Task<T>> operation, 
    int maxRetries = 3, 
    TimeSpan delay = default)
{
    int attempt = 0;
    while (true)
    {
        try
        {
            return await operation();
        }
        catch (Exception ex) when (IsTransient(ex) && attempt < maxRetries)
        {
            attempt++;
            TimeSpan waitTime = CalculateBackoff(attempt, delay);
            await Task.Delay(waitTime);
        }
    }
}
```

### **5. Benefits & Advantages**

**Resilience Improvements:**
1. **Higher Success Rates:** Many transient failures resolve on retry
2. **Better User Experience:** Automatic recovery vs manual retry
3. **Load Distribution:** Backoff strategies prevent thundering herd
4. **System Stability:** Graceful handling of temporary issues

**Operational Benefits:**
1. **Simplified Code:** Centralized retry logic
2. **Configurable Behavior:** Adjust parameters without code changes
3. **Monitoring Friendly:** Easy to track retry patterns
4. **Predictable Behavior:** Consistent handling across application

### **6. Common Variations & Extensions**

**Advanced Retry Patterns:**

1. **Retry with Circuit Breaker:**
   ```
   [Request] → [Circuit Breaker] → [Retry Logic] → [Service]
         ↑                                     ↓
   [Fallback] ← [Circuit Open] ← [Persistent Failure]
   ```

2. **Retry with Fallback:**
   ```csharp
   try
   {
       return await RetryAsync(() => primaryService.Call());
   }
   catch
   {
       return await secondaryService.Call(); // Fallback
   }
   ```

3. **Retry with Degradation:**
   - First attempt: Full functionality
   - Subsequent attempts: Reduced functionality
   - Final attempt: Minimal functionality

**Cloud-Specific Considerations:**
- **Azure Services:** Built-in retry policies in SDKs
- **AWS:** Exponential backoff with jitter recommended
- **GCP:** Context-aware retry configurations
- **Database:** Connection retry vs query retry distinction

### **7. Best Practices & Guidelines**

**Configuration Recommendations:**
1. **Start Conservative:**
   - Max retries: 3-5
   - Initial delay: 100ms - 1 second
   - Max delay: 30-60 seconds

2. **Service-Specific Tuning:**
   - User-facing services: Fewer retries, shorter delays
   - Background jobs: More retries, longer delays
   - Payment processing: Conservative retry, immediate fallback

3. **Monitoring Thresholds:**
   - Alert on > 50% retry rate
   - Monitor retry latency impact
   - Track success rates per retry attempt

**Implementation Guidelines:**
```csharp
// DO: Use async/await for non-blocking delays
await Task.Delay(backoffTime);

// DO: Log retry attempts with context
logger.LogWarning("Retry attempt {Attempt} for operation {Operation}", attempt, operationName);

// DON'T: Retry indefinitely
while (true) // ❌ Infinite loop danger

// DON'T: Retry on all exceptions
catch (Exception ex) // ❌ Too broad
```

### **8. Common Pitfalls & Anti-Patterns**

**Implementation Mistakes:**
1. **Infinite Retry Loops:** No maximum attempt limit
2. **No Backoff:** Immediate retries causing thundering herd
3. **Wrong Exception Handling:** Retrying non-transient faults
4. **Blocking Threads:** Using Thread.Sleep instead of async delays

**Design Anti-Patterns:**
1. **Retry Everything:** No discrimination between fault types
2. **Same Strategy Everywhere:** Not adapting to service characteristics
3. **Ignoring Idempotency:** Retrying non-idempotent operations
4. **No Monitoring:** Blind retries without visibility

**Idempotency Considerations:**
```
Idempotent Operations (Safe to Retry):
• GET requests (read-only)
• PUT with full resource replacement
• DELETE operations
• Stateless operations

Non-Idempotent Operations (Retry with Caution):
• POST requests (create new)
• PATCH operations (partial updates)
• Financial transactions
• State-changing operations
```

### **9. Azure-Specific Implementation**

**Azure SDK Retry Policies:**
| **Azure Service** | **Default Retry Policy** | **Customization** |
|-------------------|--------------------------|-------------------|
| **Azure Storage** | Exponential backoff (4 retries) | RetryOptions in client |
| **Service Bus** | Linear retry (5 attempts) | RetryPolicy property |
| **Cosmos DB** | 9 retries with backoff | CosmosClientOptions |
| **Event Hubs** | Exponential backoff | EventHubProducerClientOptions |

**Polly Library Implementation:**
```csharp
var retryPolicy = Policy
    .Handle<HttpRequestException>()
    .Or<TimeoutException>()
    .WaitAndRetryAsync(
        retryCount: 3,
        sleepDurationProvider: attempt => TimeSpan.FromSeconds(Math.Pow(2, attempt)),
        onRetry: (exception, timeSpan, attempt, context) => 
        {
            logger.LogWarning($"Retry {attempt} after {timeSpan.TotalSeconds}s");
        });
```

**Application Insights Integration:**
```csharp
// Track retry metrics
telemetryClient.TrackMetric("RetryAttempts", attemptCount);
telemetryClient.TrackDependency(
    dependencyTypeName: "HTTP",
    target: serviceUrl,
    data: operationName,
    success: false,
    properties: new Dictionary<string, string>
    {
        ["RetryAttempt"] = attempt.ToString(),
        ["BackoffMs"] = backoffTime.TotalMilliseconds.ToString()
    });
```

### **10. Interview-Specific Considerations**

**Common Interview Questions:**
1. "How do you determine optimal retry count and delay?"
2. "What's the difference between retry and circuit breaker patterns?"
3. "How do you handle non-idempotent operations with retries?"
4. "What metrics would you monitor for retry patterns?"

**Design Discussion Points:**
- Trade-off between persistence and user experience
- How to handle different failure modes
- Integration with existing logging/monitoring
- Impact on downstream services

**Real-World Scenarios:**
1. **E-commerce:** Payment gateway retries during peak traffic
2. **Mobile Apps:** API retries with poor network connectivity
3. **Microservices:** Service-to-service communication retries
4. **Data Processing:** Batch job retries for transient failures

---

## **Before-Interview Checklist**

**✅ Core Concept Understanding**
- [ ] Difference between transient vs permanent faults
- [ ] Common backoff strategies and when to use each
- [ ] Idempotency considerations for retry operations
- [ ] When retry pattern is appropriate vs inappropriate

**✅ Implementation Details**
- [ ] Basic retry loop implementation
- [ ] Backoff calculation algorithms
- [ ] Exception filtering logic
- [ ] Async/await implementation considerations

**✅ Configuration Parameters**
- [ ] Max retry count determination
- [ ] Delay calculation strategies
- [ ] Jitter and randomization benefits
- [ ] Service-specific tuning approaches

**✅ Integration Patterns**
- [ ] How retry works with circuit breaker
- [ ] Fallback strategy implementation
- [ ] Monitoring and metrics collection
- [ ] Logging best practices for retries

**✅ System Design Application**
- [ ] Where to implement retry logic in architecture
- [ ] Impact on system load and downstream services
- [ ] User experience considerations
- [ ] Testing strategies for retry logic

---

## **60-Second Recap (Bullet Format)**

**Core Purpose:**
- Automatically retry failed operations due to transient faults
- Improve application resilience without user intervention
- Handle temporary network or service issues

**Key Strategies:**
- **Fixed Delay:** Constant wait between retries
- **Exponential Backoff:** Wait doubles each attempt
- **Randomized/Jitter:** Add randomness to prevent synchronization
- **Incremental:** Fixed increase per attempt

**Implementation Points:**
- Set maximum retry limit (3-5 typical)
- Distinguish retryable vs non-retryable exceptions
- Use async delays to avoid blocking threads
- Implement proper backoff to prevent overload

**When to Use:**
- Network calls to external services
- Database connections
- File I/O operations
- Any operation with temporary failure modes

**When to Avoid:**
- Non-idempotent operations (without safeguards)
- Business logic failures (not transient)
- Operations where immediate failure is better than delay

**Best Practices:**
- Always implement maximum retry limit
- Use exponential backoff with jitter
- Monitor retry rates and success metrics
- Distinguish between different failure types

**Common Combinations:**
- With Circuit Breaker (stop retrying when service is down)
- With Fallback (alternative path when retries exhausted)
- With Timeout (prevent indefinite hanging)

---

## **Sources & References**

**Primary Source:**
- Microsoft Azure Architecture Center: Retry Pattern
- URL: https://learn.microsoft.com/en-us/azure/architecture/patterns/retry

**Additional References:**
- Polly .NET Resilience Library
- "Cloud Design Patterns" by Microsoft Patterns & Practices
- AWS Retry Strategies Documentation
- Google Cloud Retry Recommendations

**Related Azure Patterns:**
- Circuit Breaker Pattern (complementary)
- Health Endpoint Monitoring Pattern
- Bulkhead Pattern (resource isolation)
- Compensating Transaction Pattern

**Interview Citation Format:**
- "As outlined in Microsoft's Retry pattern documentation..."
- "Following the exponential backoff strategy recommended for cloud services..."
- "The retry implementation should distinguish between transient and permanent faults as described..."