# **Java Concurrency Summary**

## **Article 1: Winterbe - Java 8 Concurrency Tutorial**
**Source:** https://winterbe.com/posts/2015/04/30/java8-concurrency-tutorial-synchronized-locks-examples/

### **1. synchronized Keyword**
- **Method-level:** Locks on `this` instance
- **Block-level:** Explicit lock object specification
  ```java
  synchronized(lockObject) {
      // critical section
  }
  ```
- **Reentrant:** Same thread can acquire lock multiple times
- **Performance:** Has improved in recent JVMs but still has overhead

### **2. java.util.concurrent Locks**
- **ReentrantLock:**
  - More flexible than `synchronized`
  - Features: Try-lock, interruptible locks, fairness option
  ```java
  lock.lock();
  try {
      // critical section
  } finally {
      lock.unlock();
  }
  ```
- **ReadWriteLock:**
  - Multiple readers, single writer
  - `ReentrantReadWriteLock` implementation
- **StampedLock:** Java 8 optimized lock (optimistic reading)

### **3. Executors & Thread Pools**
- **ThreadPoolExecutor:** Core config parameters:
  - `corePoolSize`, `maxPoolSize`, `keepAliveTime`
  - `workQueue`, `threadFactory`, `rejectionHandler`
- **Common Pools:**
  - `Executors.newFixedThreadPool(n)`
  - `Executors.newCachedThreadPool()`
  - `Executors.newSingleThreadExecutor()`
  - `Executors.newScheduledThreadPool()`

### **4. Atomic Variables**
- **Classes:** `AtomicInteger`, `AtomicLong`, `AtomicBoolean`, `AtomicReference`
- **Operations:** `get()`, `set()`, `getAndSet()`, `compareAndSet()`, `incrementAndGet()`
- **No locking:** Use CAS (Compare-And-Swap) operations
- **Performance:** Better than locks for simple operations

### **5. Concurrent Collections**
- **Blocking Queues:**
  - `ArrayBlockingQueue`: Bounded, array-backed
  - `LinkedBlockingQueue`: Optional bounded, linked nodes
  - `SynchronousQueue`: Direct handoff between threads
  - `PriorityBlockingQueue`: Priority ordering
- **Non-blocking Collections:**
  - `ConcurrentHashMap`: Thread-safe map without locking entire table
  - `CopyOnWriteArrayList`: Snapshot iterator, good for read-heavy lists
  - `ConcurrentSkipListMap`: Concurrent sorted map

---

## **Article 2: Vogella - Java Concurrency Tutorial**
**Source:** https://www.vogella.com/tutorials/JavaConcurrency/article.html

### **1. Thread Fundamentals**
- **Creating Threads:**
  1. Extend `Thread` class
  2. Implement `Runnable` interface
  3. Java 8+: Lambda expressions
- **Thread States:**
  - NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING, TERMINATED
- **Thread Methods:**
  - `start()`, `run()`, `sleep()`, `join()`, `interrupt()`

### **2. Concurrency Utilities (java.util.concurrent)**
- **Callable & Future:**
  ```java
  Callable<Integer> task = () -> { return 42; };
  Future<Integer> future = executor.submit(task);
  Integer result = future.get(); // blocks until done
  ```
- **CompletableFuture:** Java 8+
  - Chain async operations
  - Combine multiple futures
  - Exception handling in chains
- **CountDownLatch:**
  - One-time barrier
  - `await()` blocks until count reaches zero
- **CyclicBarrier:**
  - Reusable barrier for multiple threads
- **Semaphore:**
  - Controls access to limited resources
  - `acquire()`, `release()`

### **3. Deadlock Prevention**
- **Conditions (all required):**
  1. Mutual Exclusion
  2. Hold and Wait
  3. No Preemption
  4. Circular Wait
- **Prevention Strategies:**
  - Lock ordering (always acquire in same order)
  - Lock timeout (`tryLock()` with timeout)
  - Deadlock detection algorithms

### **4. Thread Safety & Immutability**
- **Strategies:**
  1. **Synchronization:** Locks, synchronized blocks
  2. **Confinement:** Thread-local data
  3. **Immutability:** Final fields, immutable objects
  4. **Publishing Safely:** Safe construction, proper visibility
- **@ThreadSafe Annotation:** Documentation only (from javax.annotation)

### **5. Performance Considerations**
- **Amdahl's Law:** Theoretical speedup limit based on parallelizable portion
- **Contention:** High lock contention reduces performance
- **False Sharing:** Cache line ping-pong between cores
- **Context Switching:** Overhead of switching threads

---

## **Article 3: Jenkov - Java Concurrency Tutorial**
**Source:** https://jenkov.com/tutorials/java-concurrency/index.html

### **1. Thread Communication & Coordination**
- **Wait/Notify Pattern:**
  ```java
  synchronized(lock) {
      while(!condition) {
          lock.wait();
      }
      // proceed
  }
  
  synchronized(lock) {
      condition = true;
      lock.notifyAll();
  }
  ```
- **Common Issues:** Missed signals, spurious wakeups
- **Java 5+ Alternatives:** `Condition` interface with `Lock`

### **2. Advanced Locking**
- **Condition Interface:** `await()`, `signal()`, `signalAll()`
- **Lock Fairness:** `ReentrantLock(true)` for fair ordering
- **Read-Write Lock Tradeoffs:** 
  - Readers don't block readers
  - Writers block everyone
  - Risk of writer starvation

### **3. Thread Pools Internals**
- **Work Queue Strategies:**
  - Direct handoff (SynchronousQueue)
  - Unbounded queue (LinkedBlockingQueue)
  - Bounded queue (ArrayBlockingQueue)
- **Saturation Policies:**
  - Abort (throws RejectedExecutionException)
  - Caller-runs (executes in calling thread)
  - Discard (silently drop)
  - Discard-oldest (drop oldest task)

### **4. Non-blocking Algorithms**
- **CAS Operations:** `compareAndSet()` at hardware level
- **ABA Problem:** Value A→B→A appears unchanged
- **AtomicStampedReference:** Solves ABA with version stamp
- **Lock-free vs Wait-free:**
  - Lock-free: System makes progress, individual threads may starve
  - Wait-free: All threads make progress

### **5. Concurrency Patterns**
- **Producer-Consumer:** BlockingQueue implementation
- **Thread Pool Pattern:** ExecutorService
- **Future Pattern:** Async computation with result
- **Fork-Join:** Divide and conquer (Java 7+)
  - `ForkJoinPool`, `RecursiveTask`, `RecursiveAction`

---

## **60-Second Recap for Interview**

1. **Three Synchronization Approaches:** `synchronized`, `Lock` objects, atomic variables
2. **Executor Framework:** Never create raw threads - use thread pools
3. **Concurrent Collections:** `ConcurrentHashMap`, blocking queues for thread-safe data sharing
4. **Coordination Tools:** CountDownLatch, CyclicBarrier, Semaphore for thread coordination
5. **Modern Patterns:** CompletableFuture for async chains, Fork-Join for parallel processing

---

## **Interview Checklist**

### **Core Concepts:**
- [ ] Can explain difference between `synchronized` and `ReentrantLock`
- [ ] Knows thread states and lifecycle
- [ ] Can describe deadlock conditions and prevention
- [ ] Understands volatile vs synchronized vs atomic
- [ ] Knows common thread pools and when to use each

### **Tool Proficiency:**
- [ ] Can implement producer-consumer with BlockingQueue
- [ ] Knows how to use CountDownLatch/CyclicBarrier
- [ ] Can create CompletableFuture chains
- [ ] Understands ForkJoinPool usage
- [ ] Can use ConcurrentHashMap properly

### **Scenario Response:**
**If asked about high-concurrency design:**
1. "I'd start with profiling to identify actual bottlenecks"
2. "Consider lock striping for maps vs ConcurrentHashMap"
3. "Use thread pools sized appropriately (not too many, not too few)"
4. "Consider non-blocking algorithms if contention is high"
5. "Use concurrent collections instead of synchronized wrappers"

**If asked about thread pool configuration:**
1. "CPU-bound tasks: pool size = core count"
2. "IO-bound tasks: larger pool (core count * (1 + wait time/service time))"
3. "Use bounded queues to prevent out-of-memory"
4. "Set appropriate rejection policies"
5. "Consider using `ThreadPoolExecutor` directly for fine control"

**If asked about synchronization choice:**
1. "Simple mutual exclusion: synchronized"
2. "Advanced features (try-lock, fairness): ReentrantLock"
3. "Single variable atomic updates: Atomic classes"
4. "Multiple readers/single writer: ReadWriteLock"
5. "Optimistic reads: StampedLock"

### **Common Interview Questions Prepared:**
- **"synchronized vs ReentrantLock?"**
  - "synchronized is simpler, built-in, auto-releasing. ReentrantLock offers tryLock, fairness, multiple Conditions"

- **"How to avoid deadlock?"**
  - "Always acquire locks in same order, use tryLock with timeout, deadlock detection"

- **"When to use volatile?"**
  - "Single writer, multiple readers scenario where atomicity not needed"

- **"ConcurrentHashMap vs synchronized HashMap?"**
  - "ConcurrentHashMap uses lock striping, better scalability for high concurrency"

### **Performance Patterns:**
- [ ] **Reduce Lock Granularity:** Fine-grained locking
- [ ] **Lock Striping:** Multiple locks for different hash buckets
- [ ] **Copy-on-Write:** For read-heavy, write-rarely data
- [ ] **Thread Confinement:** Keep data thread-local when possible
- [ ] **Immutable Objects:** No synchronization needed

### **Key Phrases to Use:**
- "I prefer executor services over raw thread creation for better resource management"
- "ConcurrentHashMap uses lock striping for better scalability under high contention"
- "Atomic variables use CAS operations, avoiding locks entirely for simple operations"
- "CompletableFuture provides a fluent API for async programming chains"
- "The Fork-Join framework is ideal for divide-and-conquer algorithms"
- "Always release locks in finally blocks to prevent deadlock on exceptions"

---

## **Concurrency Decision Tree**

**Need thread-safe access?**
- → Single variable? → Need atomicity? → Use Atomic classes
                    → No atomicity? → Use volatile
- → Collection? → Map → ConcurrentHashMap
               → List → CopyOnWriteArrayList (read-heavy)
                      → Collections.synchronizedList (write-heavy)
               → Queue → BlockingQueue implementations
- → Custom logic? → Simple exclusion? → synchronized
                 → Advanced needs? → ReentrantLock
                 → Read/Write pattern? → ReadWriteLock

**Remember:** Always measure performance under realistic loads - theoretical benefits may not materialize in your specific use case!