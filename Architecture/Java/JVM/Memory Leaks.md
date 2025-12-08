# **Java Memory Leaks Guide Summary**

## **Article 1: Stackify - Java Memory Leaks Solutions**
**Source:** https://stackify.com/java-memory-leaks-solutions/

### **1. Memory Leak Definition in Java**
- **Key Concept:** Objects are no longer needed but still referenced → cannot be garbage collected
- **Root Cause:** Unintentional object retention in memory
- **Key Difference from C/C++:** Java has GC, but leaks still occur via *logical* issues, not *manual* memory management

### **2. Common Leak Patterns**
- **Static Fields Holding Objects:**
  ```java
  public class LeakyClass {
      private static final List<Object> STATIC_LIST = new ArrayList<>();
  }
  ```
  - Static collections grow indefinitely
  - Solution: Use weak references or clear collections

- **Unclosed Resources:**
  - Database connections, streams, sockets
  - **Must use try-with-resources:**
  ```java
  try (Connection conn = DriverManager.getConnection(url)) {
      // auto-closed
  }
  ```

- **ThreadLocal Variables:**
  - Each thread maintains its own copy
  - Not cleaned up when thread dies in pool
  - **Solution:** Call `ThreadLocal.remove()` after use

- **HashSet/HashMap with Mutable Keys:**
  - Key object changes hashCode after insertion
  - Entry becomes "lost" in map
  - **Solution:** Use immutable keys or don't modify keys

### **3. Detection Tools**
- **Heap Dump Analysis:**
  - Use `jmap -dump:live,format=b,file=heap.hprof <pid>`
  - Analyze with Eclipse MAT, VisualVM, YourKit
- **GC Log Analysis:**
  - Monitor Old Gen growth
  - Look for increasing heap usage after Full GC
- **Production Monitoring:**
  - APM tools like Stackify Retrace
  - Set heap usage alerts

### **4. Prevention Strategies**
- **Code Reviews:** Focus on collection usage, resource management
- **Static Analysis Tools:** FindBugs, SonarQube
- **Testing:** Load tests with heap analysis
- **Use Weak/Soft References** for caches

---

## **Article 2: Oracle JDK 21 Troubleshooting Guide**
**Source:** https://docs.oracle.com/en/java/javase/21/troubleshoot/troubleshooting-memory-leaks.html

### **1. Official Diagnosis Methodology**
- **Step 1:** Confirm it's a memory leak (not just high memory usage)
- **Step 2:** Identify leaking objects
- **Step 3:** Find root cause in code

### **2. Native Memory Leaks (JNI)**
- **Unique to Oracle Guide:** Covers JNI/native memory leaks
- **JNI Code Issues:**
  - Native allocations not freed
  - Global references not deleted
  - **Detection:** Native memory tracking (`-XX:NativeMemoryTracking=detail`)
- **Tools:** `jcmd <pid> VM.native_memory`

### **3. Heap Analysis Commands**
- **Live Heap Dump:**
  ```bash
  jcmd <pid> GC.heap_dump filename=heap.hprof
  ```
- **Class Histogram:**
  ```bash
  jcmd <pid> GC.class_histogram
  ```
- **Check Reachable Objects:**
  ```bash
  jmap -histo:live <pid>
  ```

### **4. Metaspace Leaks**
- **Causes:** Dynamic class generation, classloader leaks
- **Detection:** Monitor Metaspace usage
- **Flags:** `-XX:MaxMetaspaceSize`, `-XX:MetaspaceSize`
- **Common in:** Frameworks using reflection, dynamic proxies

### **5. Garbage Collection Analysis**
- **GC Logs:**
  ```bash
  -Xlog:gc*,gc+heap=debug:file=gc.log
  ```
- **Key Indicators:**
  - Old Gen grows after each Full GC
  - Increasing time between Full GCs
  - `OutOfMemoryError: Java heap space`

---

## **Article 3: Sematext - Java Memory Leaks**
**Source:** https://sematext.com/blog/java-memory-leaks/

### **1. Production-Focused Detection**
- **Monitoring Approach:**
  - Continuous heap usage tracking
  - Alert on unusual growth patterns
  - Baseline "normal" memory behavior
- **Sematext JVM Monitoring Features:**
  - Automatic leak detection algorithms
  - Correlation with GC events
  - Historical trend analysis

### **2. Common Framework-Specific Leaks**
- **Java EE Application Servers:**
  - Deployed application leaks (not unloading)
  - Classloader leaks
  - Session object retention
- **Spring Framework:**
  - Prototype beans in singleton scope
  - Event listener subscriptions
  - Cache configurations
- **Hibernate:**
  - Session not closed
  - Lazy loading collections
  - Cache configurations

### **3. Thread-Related Leaks**
- **Thread Pool Executors:**
  - Runnable/Callable capturing outer class references
  - ThreadLocal variables in pooled threads
  - **Solution:** Use `ThreadLocal.withInitial()` carefully
- **Custom Thread Implementations:**
  - Strong references in thread objects
  - Thread holding object references

### **4. Collection-Specific Leaks**
- **Listeners/Observers:**
  - Not removing listeners
  - **Pattern:** Add listener but never remove
  - **Solution:** Use WeakReference listeners
- **Caches:**
  - Unbounded caches (Guava, Ehcache)
  - Missing eviction policies
  - **Solution:** Size limits, TTL, soft references

---

## **Article 4: Baeldung - Java Memory Leaks**
**Source:** https://www.baeldung.com/java-memory-leaks

### **1. Practical Code Examples**
- **Inner Class Reference:**
  ```java
  public class Outer {
      private String data;
      private class Inner {
          // Implicitly holds reference to Outer.this
      }
  }
  ```
  - Inner class instance prevents Outer collection

- **String.intern() in Long-lived Map:**
  ```java
  private Map<String, String> map = new HashMap<>();
  public void add(String data) {
      map.put(data, data.intern()); // Leak!
  }
  ```

### **2. Tools & Techniques Comparison**
| **Tool** | **Best For** | **Limitations** |
|----------|--------------|-----------------|
| **VisualVM** | Quick analysis, live monitoring | Limited deep analysis |
| **Eclipse MAT** | Deep heap analysis, leak suspects | Requires heap dump |
| **JProfiler** | Production profiling, low overhead | Commercial license |
| **YourKit** | Memory allocation tracking | Commercial license |
| **NetBeans Profiler** | Development-time analysis | Not for production |

### **3. Prevention Patterns**
- **Use WeakHashMap for Cache-Like Structures**
- **Implement AutoCloseable for Resources**
- **Clear References in finally blocks**
- **Use try-with-resources for AutoCloseable**
- **Periodically clear collections** in long-lived objects

### **4. Testing for Leaks**
- **Unit Tests with Assertions:**
  ```java
  @Test
  public void testNoMemoryLeak() {
      WeakReference<Object> ref = new WeakReference<>(createObject());
      System.gc();
      assertNull(ref.get()); // Object should be collected
  }
  ```
- **Load Testing with Heap Analysis**
- **Static Code Analysis Rules**

---

## **60-Second Recap for Interview**

1. **Leak Definition:** Objects reachable but unused → GC cannot collect
2. **Common Causes:** Static collections, unclosed resources, ThreadLocal, listeners/caches, inner classes
3. **Detection Tools:** Heap dumps (`jmap`, `jcmd`), Eclipse MAT, GC log analysis
4. **Prevention:** try-with-resources, weak references, careful static usage, code reviews
5. **Production Strategy:** Monitor heap trends, set alerts, baseline normal behavior

---

## **Interview Checklist**

### **Conceptual Understanding:**
- [ ] Can explain difference between memory leak vs. high memory usage
- [ ] Knows why Java can have leaks despite garbage collection
- [ ] Can describe at least 3 common leak patterns with examples
- [ ] Understands strong vs. weak vs. soft references

### **Tool Proficiency:**
- [ ] Knows how to take a heap dump (`jmap` or `jcmd`)
- [ ] Familiar with Eclipse MAT basic views (Histogram, Dominator Tree, Leak Suspects)
- [ ] Can analyze GC logs for leak indicators
- [ ] Knows native memory leak detection commands

### **Scenario Response:**
**If asked about suspected memory leak:**
1. "First, I'd monitor heap usage to confirm pattern (continuous growth)"
2. "Take multiple heap dumps over time and compare object counts"
3. "Use Eclipse MAT to find GC roots retaining objects"
4. "Check for common patterns: static collections, unclosed resources"

**If asked about prevention:**
1. "Code reviews focusing on resource management"
2. "Use try-with-resources for AutoCloseable"
3. "Avoid long-lived collections holding short-lived data"
4. "Implement proper cache eviction policies"

**If asked about production strategy:**
1. "Continuous heap monitoring with alerts on unusual growth"
2. "Regular heap analysis in staging/load tests"
3. "Establish memory usage baselines for each release"
4. "Educate team on common leak patterns"

### **Framework-Specific Knowledge:**
- [ ] Spring: Prototype bean misuse, event listeners
- [ ] Hibernate: Session management, cache settings
- [ ] Application Servers: Classloader leaks, deployment leaks
- [ ] Thread Pools: ThreadLocal cleanup

### **Advanced Topics:**
- [ ] Native memory leaks via JNI
- [ ] Metaspace/classloader leaks
- [ ] Custom classloader issues
- [ ] Direct ByteBuffer leaks

### **Key Phrases to Use:**
- "Memory leaks in Java are typically logical issues, not GC failures"
- "I always start with heap dump analysis using Eclipse MAT"
- "try-with-resources is essential for preventing resource leaks"
- "Static collections are a common leak source - they live for the ClassLoader's lifetime"
- "ThreadLocal variables need explicit remove() in thread pool scenarios"
- "WeakReferences are useful for cache-like structures to prevent leaks"

---

## **Memory Leak Detection Flowchart**

**Suspect Leak → Monitor Heap → GC Logs → Heap Dumps → MAT Analysis → Code Fix**

1. **Monitor:** Continuous heap usage (growing after Full GC?)
2. **Confirm:** Multiple heap dumps showing same objects increasing
3. **Analyze:** Use MAT to find retaining paths
4. **Identify:** Common pattern or custom issue
5. **Fix:** Remove unnecessary references, close resources
6. **Verify:** Monitor after fix, regression tests

**Remember:** Not all memory growth is a leak - understand your application's normal behavior first!