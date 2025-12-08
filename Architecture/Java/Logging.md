Java **logging** follows **facade + implementation** pattern: **SLF4J** (API) + **Logback/Log4j2** (impl) with bridges for legacy libs; structured levels (ERROR/WARN/INFO/DEBUG) + **MDC** for request correlation.[marcobehler](https://www.marcobehler.com/guides/java-logging)​

---

## **Java Logging Summary**

## **Logging Stack (Modern Architecture)**

|Layer|Role|Examples|
|---|---|---|
|**Facade/API**|Code against this|**SLF4J**, Log4j2-api|
|**Implementation**|Actual logging|**Logback**, **Log4j2-core**|
|**Bridges**|Legacy migration|`log4j-over-slf4j`, `jul-to-slf4j`|
|**Centralized**|Multi-app correlation|**Graylog**, ELK, Splunk|

## **Log Levels (Production Guidelines)**

text

`FATAL: Process terminates ERROR: Human intervention ASAP (request aborted) WARN: Fix soon (batch failures) INFO: Status + weak errors (login failed) DEBUG: Internal flows (investigation only) TRACE: Extreme detail (Spring tx boundaries)`

---

## **Tools & Frameworks (Production 2025)**

|Category|Tools|Use Case|
|---|---|---|
|**Core Stack**|**SLF4J + Logback**|Default choice|
|**High Perf**|**Log4j2 AsyncLogger**|10M+ logs/sec|
|**Spring Boot**|**spring-boot-starter-logging**|Auto-config SLF4J+Logback|
|**Centralized**|**Graylog GELF**, **ELK**|Microservices correlation|
|**Observability**|**Micrometer + Prometheus**|Metrics + logs|

---

## **Interview Checklist – Logging Mastery**

**✅ Stack Architecture**

text

`✅ SLF4J api + Logback impl (default) ✅ Exclude legacy: log4j-over-slf4j, jul-to-slf4j ✅ Spring Boot: logback-spring.xml overrides ✅ Multiple files: error.log + status.log`

**✅ Production Practices**

-  **MDC**: `MDC.put("requestId", id)` → auto-correlation
    
-  **Proactive help**: "Port 8080 in use → kill PID 1234"
    
-  **No sensitive data**: Mask PII/credentials
    
-  Separate ERROR/WARN for alerting
    

**✅ Centralized Ops**

text

`✅ Graylog: GELF appender ✅ ELK: Logstash → Elasticsearch → Kibana ✅ Merge logs: error.log + status.log → timeline ✅ Search: requestId across services`

**✅ Performance**

-  Log4j2 AsyncLogger (disruptor ringbuffer)
    
-  Avoid string concat: `logger.debug("x={}", x)`
    
-  Logback auto-reload config
    

---

## **60-Second Recap – "Must-Say" Bullets**

text

`"SLF4J + Logback (default), Log4j2 (perf) Bridges: *-over-slf4j for legacy libs Levels: ERROR(urgent) WARN(fix soon) INFO(status) DEBUG(flows) MDC: requestId correlation across microservices Files: error.log(alerting) + status.log(timeline) Central: Graylog GELF / ELK Stack Spring: logback-spring.xml + Micrometer Perf: AsyncLogger, no string concat, auto-reload"`

**Reference**: [MarcoBehler - Modern Java Logging](https://www.marcobehler.com/guides/java-logging)[marcobehler](https://www.marcobehler.com/guides/java-logging)​

**Architect gold: MDC correlation + level discipline + centralized ops.** 🚀

1. [https://www.marcobehler.com/guides/java-logging](https://www.marcobehler.com/guides/java-logging)
2. 