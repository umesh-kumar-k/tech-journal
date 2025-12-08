Real-time data APIs deliver **continuous streaming data** with sub-second latency (<1s end-to-end) vs batch processing, enabling immediate decision-making, fraud detection, and personalized customer experiences.[qlik](https://www.qlik.com/us/streaming-data/real-time-data)​

---

## **Real-Time Data APIs – Senior Architect Summary**

## **Core Characteristics (Memorize Latency Tiers)**

|Tier|Latency|Use Case|Tech Stack|
|---|---|---|---|
|**Real-time**|<1s|Fraud, personalization|Kafka Streams, Kinesis|
|**Near real-time**|1-10s|Dashboards, alerts|Change Data Capture|
|**Batch**|Minutes-Hours|Reports, analytics|Spark, Airflow|

## **Architecture Patterns (Must-Know 4)**

|Pattern|Flow|Pros|Cons|
|---|---|---|---|
|**Event Streaming**|Producer → Kafka → Consumer|Durable, scalable|Ordering complexity|
|**WebSocket**|Bidirectional client ↔ server|Low latency UI|Stateful connections|
|**Server-Sent Events**|Server → client (unidirectional)|Simple, auto-reconnect|No client→server|
|**Change Data Capture**|DB log → stream → consumer|Zero-downtime sync|Schema evolution|

## **Key Metrics (SLA Targets)**

text

`✅ End-to-end latency: <500ms P99 ✅ Throughput: 1M+ events/sec ✅ Durability: 99.999% (3 replicas) ✅ Exactly-once: Idempotent consumers`

---

## **Interview Checklist – Real-Time Mastery**

**✅ Architecture Decisions**

-  Streaming (Kafka) vs WebSocket vs SSE selection
    
-  Exactly-once vs at-least-once trade-offs
    
-  CDC (Debezium) for DB→stream sync
    

**✅ Scale Patterns**

-  Partitioning (consumer groups, key-based)
    
-  Backpressure handling (buffer sizing)
    
-  Schema registry (Avro/Protobuf evolution)
    

**✅ Use Cases Mapping**

-  Fraud: <100ms anomaly detection
    
-  Personalization: User activity → recommendations
    
-  Monitoring: Infra metrics → alerts
    

**✅ Monitoring/Operations**

-  Lag monitoring (consumer offset)
    
-  Dead letter queues
    
-  Circuit breakers + fallbacks
    

---

## **60-Second Recap – "Must-Say" Bullets**

text

`"Real-time APIs = <1s E2E latency streaming (Kafka/WebSocket/SSE) Patterns: Event Streaming(Kafka) → fraud/personalization WebSocket ↔ UI, CDC → DB sync, SSE → dashboards Scale: Partitioning + consumer groups + schema registry Metrics: P99<500ms + 1M/sec + exactly-once Ops: Lag monitoring + DLQ + backpressure + idempotency"`

**Reference**: [Qlik - What is Real-Time Data?](https://www.qlik.com/us/streaming-data/real-time-data)[qlik](https://www.qlik.com/us/streaming-data/real-time-data)​

**Architect gold: latency tiers, pattern selection, scale operations.** 🚀

1. [https://www.qlik.com/us/streaming-data/real-time-data](https://www.qlik.com/us/streaming-data/real-time-data)


Real-time data transport comparison: **WebSocket** (bidirectional, persistent) vs **SSE** (server→client, auto-reconnect) vs **Long Polling** (HTTP hack, server-held connections) vs **Polling** (client-pull, wasteful).

---

## **Real-Time Transport Comparison – Senior Architect Summary**

## **Core Patterns (Memorize Latency + Direction)**

|Pattern|Direction|Latency|Connections|Overhead|Browser Support|
|---|---|---|---|---|---|
|**Polling**|Client→Server|High (interval)|Short-lived|**High** (empty responses)|✅ All|
|**Long Polling**|Client→Server|Low-Medium|**Long-held**|Medium (held connections)|✅ All|
|**SSE**|**Server→Client**|**Low**|Auto-reconnect|**Low** (HTTP/2)|✅ Modern|
|**WebSocket**|**↔ Bidirectional**|**Lowest**|Persistent|Low (framing)|✅ Modern|

## **Decision Matrix (Production Selection)**

|Use Case|Winner|Why|
|---|---|---|
|**Dashboards/Alerts**|**SSE**|Auto-reconnect + event IDs|
|**Chat/Live Collab**|**WebSocket**|Bidirectional + binary|
|**Legacy/Simple**|**Long Polling**|Universal HTTP|
|**IoT Metrics**|**WebSocket**|High throughput|

---

## **Interview Checklist – Real-Time Transport Mastery**

**✅ Pattern Selection**

-  SSE: unidirectional server→client, auto-reconnect, event IDs
    
-  WebSocket: bidirectional, subprotocol negotiation, binary data
    
-  Long Polling: HTTP/1.1 hack, connection exhaustion risk
    
-  Polling: wasteful baseline (avoid)
    

**✅ Architecture Trade-offs**

-  WebSocket: sticky sessions vs load balancer upgrades
    
-  SSE: HTTP/2 multiplexing advantage
    
-  Connection limits: 6→1000+ per domain
    
-  Fallback: SSE→Long Poll→Polling
    

**✅ Scale & Ops**

-  WebSocket: heartbeats (ping/pong), graceful close
    
-  SSE: `Last-Event-ID` resume capability
    
-  Proxy config: timeout + upgrade headers
    
-  Monitoring: connection count, drop rates
    

**✅ Implementation**

text

``✅ SSE: `text/event-stream`, `event:`, `data:`, `retry:` ✅ WebSocket: `Upgrade: websocket`, `Sec-WebSocket-Key` ✅ Long Poll: `Connection: keep-alive` + server delay``

---

## **60-Second Recap – "Must-Say" Bullets**

text

`"Real-time: Polling(wasteful) → Long Poll(HTTP hack) → SSE(server→client) → WebSocket(↔) SSE: dashboards/alerts (auto-reconnect + event IDs) WebSocket: chat/collab (bidirectional + binary) Long Poll: legacy fallback (universal HTTP) Scale: SSE(HTTP/2 mux) > WS(sticky/proxy), monitor drops + heartbeats Fallback: SSE→Long→Poll, proxy timeout config critical"`

**Reference**: [Medium - HTTP Polling vs WebSocket vs SSE](https://medium.com/@rano3003/demystifying-http-request-vs-http-polling-vs-long-polling-vs-websocket-vs-server-sent-event-vs-aa65d26d1e80)

**Architect gold: pattern progression, proxy pitfalls, fallback strategy.** 🚀

Certainly! Here are popular tools and frameworks for each real-time transport pattern, to help bridge architecture to implementation:

---

## Real-Time Transport – Tools & Frameworks Reference

|Pattern|Tools/Frameworks|Description|Ecosystem|
|---|---|---|---|
|**Polling**|jQuery AJAX, Axios|Simple HTTP request libraries|JS/browser, Node.js|
|**Long Polling**|SockJS, Socket.IO (fallback mode)|Abstracts WebSocket + fallback to long polling|JS/browser, server|
|**SSE**|EventSource API (browser native), `sse-client`|Native browser client, Node.js lightweight lib|Browsers, Node.js|
||Spring WebFlux, Vert.x|Java reactive frameworks with SSE support|Java|
|**WebSocket**|Socket.IO, ws, SignalR|WebSocket abstraction + fallbacks + multiplexing|JS, .NET, Java|
||Kaazing, Pusher, AWS AppSync|Managed real-time APIs/platforms|Cloud & multi-language|

---

## Architecture & Scale Tips With Tools

- Use **Socket.IO** or **Spring WebFlux** for automatic fallback (WebSocket → Long Polling).
    
- For **SSE**, native EventSource client + reactive backend frameworks recommended.
    
- Managed services like **Pusher** or **AWS AppSync** simplify scaling for real-time apps.
    
- Proxy config critical: Nginx/Envoy WebSocket upgrade headers + idle timeouts.
    
- Monitor connections with tools like **Prometheus + Grafana** (connection count, drop rates).
    

---

