
## Event Sourcing Pattern

Event Sourcing stores state as an immutable sequence of events in an append-only store, replaying them to materialize current state, enabling auditability and scalability over CRUD.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing)​

- Events describe business actions (e.g., "OrderPlaced", "ItemAdded"); event store as single source of truth
    
- Materialized views/projections for fast queries; snapshots reduce replay cost for long event streams
    
- Decouples producers/consumers via queues; eventual consistency with CQRS read models[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing)​
    

## Tools and Frameworks

|Ecosystem|Tools/Frameworks|Example Use Case|
|---|---|---|
|.NET|EventStoreDB, Marten (Postgres ES), NEventStore|Conference booking with seat availability projection|
|Java|Axon Framework, Eventuate|Full ES+CQRS with saga orchestration|
|Node.js|NodeEventStore, Kafka + ksqlDB|Microservices event streams with materialized views|
|Cloud|Azure Cosmos DB Change Feed, Kafka Streams, Event Hubs|Event capture → projection → read-optimized NoSQL|
|Enterprise|GetEventStore, Apache Kafka|High-throughput financial transaction audit trails|

## Interview Checklist

- Define: Append-only event store as source of truth; replay events → current state[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing)​
    
- Workflow: Command → load entity via events → business logic → raise events → handlers update projections
    
- Benefits: No locking/contention, full audit trail, easy state reconstruction, integration via events
    
- Gotchas: Eventual consistency, event versioning, ordering (timestamps/seq IDs), idempotent handlers
    
- Storage: EventStoreDB (optimized), Kafka (streams), Cosmos (change feed); snapshots for perf
    
- CQRS combo: Events → denormalized read models; dual stores (SQL write, NoSQL read)
    
- When: High-scale writes, audit needs, complex domains; avoid simple CRUD
    
- Tradeoffs: Complexity (high migration cost), query cost (projections/snapshots required)
    

## 60-Second Recap

- **Core**: Events as immutable history; replay → state; projections for queries[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing)​
    
- **Why**: Scalable writes (no locks), audit trail, easy debugging/reconstruction
    
- **Ex**: "SeatsReserved" events → availability counter projection
    
- **Tools**: EventStoreDB, Axon, Kafka Streams, Marten, Cosmos Change Feed
    
- **Wins**: Decoupled (queues), snapshots perf boost; pair with CQRS for reads
    

**Reference**: [https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing](https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing)[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing)​

1. [https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing](https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing)
2. [https://learn.microsoft.com/en-us/](https://learn.microsoft.com/en-us/)
# **Event Sourcing Pattern**

## **Core Thesis**
Event Sourcing persists the state of a business entity as a sequence of state-changing events, allowing reconstruction of current state by replaying events, enabling audit trails, temporal queries, and complex business logic while solving data consistency problems in distributed systems.

---

## **Detailed Notes**

### **1. Problem Statement & Context**

**Traditional CRUD Limitations:**
1. **Lost History:** Only current state stored, no audit trail
2. **Concurrency Issues:** Optimistic locking failures
3. **Complex Business Logic:** Hard to model state transitions
4. **Data Consistency:** Distributed transactions problematic

**What Event Sourcing Solves:**
- Complete audit trail of all changes
- Ability to reconstruct any past state
- Natural fit for complex domain logic
- Better performance for certain workloads

### **2. Core Concepts & Architecture**

**Basic Event Sourcing Model:**
```
┌─────────────────────────────────────────────────┐
│               Event Store                        │
│  ┌─────────────────────────────────────────┐   │
│  │ Event 1: OrderCreated                   │   │
│  │ Event 2: OrderItemAdded                 │   │
│  │ Event 3: OrderSubmitted                 │   │
│  │ Event 4: OrderApproved                  │   │
│  │ Event 5: OrderShipped                   │   │
│  └─────────────────────────────────────────┘   │
└─────────────────────────────────────────────────┘
         │                 │               │
         ▼                 ▼               ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│ Current State   │ │ State at Time X │ │ State at Time Y │
│ (Replay all)    │ │ (Replay first 3)│ │ (Replay first 4)│
└─────────────────┘ └─────────────────┘ └─────────────────┘
```

**Key Components:**
1. **Event:** Immutable record of something that happened
2. **Event Store:** Append-only log of events
3. **Aggregate:** Business entity that processes commands and emits events
4. **Projection:** Read model derived from events

### **3. Implementation Examples**

**JavaScript/Node.js Implementation:**
```javascript
// Event Definitions
class DomainEvent {
  constructor(aggregateId, type, data, timestamp = new Date()) {
    this.aggregateId = aggregateId;
    this.type = type;
    this.data = data;
    this.timestamp = timestamp;
    this.version = 0;
  }
}

// Order Events
class OrderCreated extends DomainEvent {
  constructor(orderId, customerId, items) {
    super(orderId, 'OrderCreated', { customerId, items });
  }
}

class OrderItemAdded extends DomainEvent {
  constructor(orderId, item) {
    super(orderId, 'OrderItemAdded', { item });
  }
}

class OrderSubmitted extends DomainEvent {
  constructor(orderId) {
    super(orderId, 'OrderSubmitted', {});
  }
}

// Aggregate (Order)
class Order {
  constructor() {
    this.id = null;
    this.customerId = null;
    this.items = [];
    this.status = 'DRAFT';
    this.version = 0;
    this.changes = []; // Pending events
  }

  // Reconstruct from events
  static fromEvents(events) {
    const order = new Order();
    events.forEach(event => order.applyEvent(event, false));
    order.changes = [];
    return order;
  }

  // Apply event to state
  applyEvent(event, isNew = true) {
    switch (event.type) {
      case 'OrderCreated':
        this.id = event.aggregateId;
        this.customerId = event.data.customerId;
        this.items = event.data.items;
        break;
      case 'OrderItemAdded':
        this.items.push(event.data.item);
        break;
      case 'OrderSubmitted':
        this.status = 'SUBMITTED';
        break;
    }
    this.version++;
    if (isNew) {
      this.changes.push(event);
    }
  }

  // Command handlers (return events, don't modify state directly)
  create(orderId, customerId, items) {
    if (this.id) throw new Error('Order already exists');
    
    const event = new OrderCreated(orderId, customerId, items);
    this.applyEvent(event);
    return this.changes;
  }

  addItem(sku, quantity, price) {
    if (this.status !== 'DRAFT') {
      throw new Error('Cannot add items to submitted order');
    }
    
    const event = new OrderItemAdded(this.id, { sku, quantity, price });
    this.applyEvent(event);
    return this.changes;
  }

  submit() {
    if (this.items.length === 0) {
      throw new Error('Cannot submit empty order');
    }
    
    const event = new OrderSubmitted(this.id);
    this.applyEvent(event);
    return this.changes;
  }

  // Get pending changes (new events)
  getUncommittedChanges() {
    return this.changes;
  }

  markChangesAsCommitted() {
    this.changes = [];
  }
}
```

**Event Store Implementation:**
```javascript
class EventStore {
  constructor() {
    this.events = new Map(); // aggregateId -> events[]
  }

  async save(aggregateId, events, expectedVersion) {
    const existingEvents = this.events.get(aggregateId) || [];
    
    // Optimistic concurrency check
    if (expectedVersion !== -1 && existingEvents.length !== expectedVersion) {
      throw new Error(`Concurrency conflict. Expected version: ${expectedVersion}, Actual: ${existingEvents.length}`);
    }
    
    // Assign version numbers
    events.forEach((event, index) => {
      event.version = existingEvents.length + index + 1;
    });
    
    this.events.set(aggregateId, [...existingEvents, ...events]);
    return events;
  }

  async getEvents(aggregateId) {
    return this.events.get(aggregateId) || [];
  }

  async getEventsByType(eventType) {
    const allEvents = [];
    for (const events of this.events.values()) {
      allEvents.push(...events.filter(e => e.type === eventType));
    }
    return allEvents;
  }
}
```

**Java/Spring Boot Implementation:**
```java
// Event Interface
public interface DomainEvent {
    String getAggregateId();
    String getType();
    Instant getTimestamp();
    int getVersion();
}

// OrderCreated Event
public class OrderCreated implements DomainEvent {
    private final String orderId;
    private final String customerId;
    private final List<OrderItem> items;
    private final Instant timestamp;
    private int version;
    
    // Constructor, getters, etc.
}

// Aggregate Root
public class Order {
    private String id;
    private String customerId;
    private List<OrderItem> items;
    private OrderStatus status;
    private int version;
    private List<DomainEvent> changes = new ArrayList<>();
    
    // Reconstruct from history
    public static Order fromHistory(List<DomainEvent> history) {
        Order order = new Order();
        history.forEach(order::apply);
        order.changes.clear();
        return order;
    }
    
    private void apply(DomainEvent event) {
        if (event instanceof OrderCreated) {
            apply((OrderCreated) event);
        } else if (event instanceof OrderItemAdded) {
            apply((OrderItemAdded) event);
        }
        this.version = event.getVersion();
    }
    
    // Command methods
    public void create(String orderId, String customerId, List<OrderItem> items) {
        if (this.id != null) {
            throw new IllegalStateException("Order already exists");
        }
        
        OrderCreated event = new OrderCreated(orderId, customerId, items);
        apply(event);
        changes.add(event);
    }
    
    public List<DomainEvent> getUncommittedChanges() {
        return new ArrayList<>(changes);
    }
    
    public void markChangesAsCommitted() {
        changes.clear();
    }
}
```

### **4. Event Store Design**

**Event Storage Considerations:**
```javascript
// Event Schema Example
{
  "id": "evt_123",              // Unique event ID
  "aggregateId": "order_456",   // Which aggregate this belongs to
  "type": "OrderItemAdded",     // Event type
  "data": {                     // Event payload
    "sku": "PROD-001",
    "quantity": 2,
    "price": 29.99
  },
  "metadata": {                 // System metadata
    "userId": "user_789",
    "correlationId": "corr_abc",
    "causationId": "cmd_xyz"
  },
  "version": 3,                 // Sequence number in aggregate
  "timestamp": "2024-01-15T10:30:00Z"
}
```

**Event Store Technologies:**
| **Technology** | **Best For** | **Considerations** |
|----------------|--------------|-------------------|
| **EventStoreDB** | Dedicated event store | Built for event sourcing |
| **Apache Kafka** | High-throughput streams | Can be used as event store |
| **SQL Database** | Relational events | Simple, ACID transactions |
| **NoSQL (Document)** | Flexible schema | Easy to query events |
| **Azure Cosmos DB** | Global distribution | Multi-region support |

### **5. Projections & Read Models**

**Projection Implementation:**
```javascript
// Projection: Builds read model from events
class OrderProjection {
  constructor() {
    this.orders = new Map(); // orderId -> order view
    this.customerOrders = new Map(); // customerId -> orderIds
  }

  // Process events to update read model
  async apply(event) {
    switch (event.type) {
      case 'OrderCreated':
        this.orders.set(event.aggregateId, {
          id: event.aggregateId,
          customerId: event.data.customerId,
          items: event.data.items,
          status: 'DRAFT',
          total: this.calculateTotal(event.data.items),
          createdAt: event.timestamp
        });
        
        // Update customer index
        if (!this.customerOrders.has(event.data.customerId)) {
          this.customerOrders.set(event.data.customerId, new Set());
        }
        this.customerOrders.get(event.data.customerId).add(event.aggregateId);
        break;

      case 'OrderItemAdded':
        const order = this.orders.get(event.aggregateId);
        if (order) {
          order.items.push(event.data.item);
          order.total = this.calculateTotal(order.items);
        }
        break;

      case 'OrderSubmitted':
        const submittedOrder = this.orders.get(event.aggregateId);
        if (submittedOrder) {
          submittedOrder.status = 'SUBMITTED';
          submittedOrder.submittedAt = event.timestamp;
        }
        break;
    }
  }

  calculateTotal(items) {
    return items.reduce((sum, item) => sum + (item.quantity * item.price), 0);
  }

  // Query methods
  getOrder(orderId) {
    return this.orders.get(orderId);
  }

  getCustomerOrders(customerId) {
    const orderIds = this.customerOrders.get(customerId) || new Set();
    return Array.from(orderIds).map(id => this.orders.get(id));
  }
}

// Projection Processor (listens to events)
class ProjectionProcessor {
  constructor(eventStore, projections) {
    this.eventStore = eventStore;
    this.projections = projections;
  }

  async start() {
    // Process existing events
    const allEvents = await this.getAllEvents();
    allEvents.forEach(event => {
      this.projections.forEach(projection => projection.apply(event));
    });

    // Subscribe to new events
    this.eventStore.on('event', (event) => {
      this.projections.forEach(projection => projection.apply(event));
    });
  }

  async getAllEvents() {
    // Implementation to get all events from event store
  }
}
```

### **6. Benefits & Advantages**

**Business Benefits:**
1. **Complete Audit Trail:** Every change recorded
2. **Temporal Queries:** "What was the state at time X?"
3. **Business Intelligence:** Analyze event patterns
4. **Legal Compliance:** Complete history for regulations

**Technical Benefits:**
1. **Eventual Consistency:** Natural fit for distributed systems
2. **Performance:** Append-only writes are fast
3. **Scalability:** Events can be processed asynchronously
4. **Debugging:** Replay events to reproduce issues

**Architectural Benefits:**
- Natural fit with CQRS
- Enables complex business logic
- Supports multiple read models
- Facilitates microservices integration

### **7. Common Variations & Extensions**

**Snapshotting Pattern:**
```javascript
// Regular snapshots to avoid replaying all events
class Snapshot {
  constructor(aggregateId, state, version) {
    this.aggregateId = aggregateId;
    this.state = state; // Serialized aggregate state
    this.version = version;
    this.timestamp = new Date();
  }
}

class SnapshotRepository {
  async getLatestSnapshot(aggregateId) {
    // Get most recent snapshot
  }

  async saveSnapshot(snapshot) {
    // Save snapshot
  }
}

class AggregateRepository {
  constructor(eventStore, snapshotRepository, snapshotInterval = 100) {
    this.eventStore = eventStore;
    this.snapshotRepository = snapshotRepository;
    this.snapshotInterval = snapshotInterval;
  }

  async get(aggregateId) {
    // Try to get snapshot first
    const snapshot = await this.snapshotRepository.getLatestSnapshot(aggregateId);
    let events = [];
    
    if (snapshot) {
      // Get events after snapshot
      events = await this.eventStore.getEventsAfterVersion(aggregateId, snapshot.version);
      // Reconstruct from snapshot + recent events
      return Aggregate.fromSnapshotAndEvents(snapshot, events);
    } else {
      // Get all events
      events = await this.eventStore.getEvents(aggregateId);
      return Aggregate.fromEvents(events);
    }
  }

  async save(aggregate) {
    const events = aggregate.getUncommittedChanges();
    await this.eventStore.save(aggregate.id, events, aggregate.version - events.length);
    
    // Create snapshot if needed
    if (aggregate.version % this.snapshotInterval === 0) {
      const snapshot = new Snapshot(aggregate.id, aggregate.serialize(), aggregate.version);
      await this.snapshotRepository.saveSnapshot(snapshot);
    }
    
    aggregate.markChangesAsCommitted();
  }
}
```

### **8. Best Practices & Guidelines**

**Event Design Principles:**
1. **Use Past Tense:** `OrderCreated`, `PaymentProcessed`
2. **Immutable:** Events never change after creation
3. **Meaningful Names:** Reflect business domain language
4. **Self-Contained:** Include all necessary data

**Versioning Strategies:**
```javascript
// Event versioning approaches

// 1. Upcasters (transform old events to new format)
class EventUpcaster {
  upcast(event) {
    if (event.version === 1) {
      return this.upcastV1ToV2(event);
    } else if (event.version === 2) {
      return this.upcastV2ToV3(event);
    }
    return event;
  }

  upcastV1ToV2(v1Event) {
    return {
      ...v1Event,
      version: 2,
      data: {
        ...v1Event.data,
        // Add new fields with defaults
        currency: v1Event.data.currency || 'USD'
      }
    };
  }
}

// 2. Dual-write during migration
// Write both old and new event formats during transition
```

**Concurrency Control:**
```javascript
// Optimistic concurrency with version checking
class EventStore {
  async save(aggregateId, newEvents, expectedVersion) {
    const currentEvents = await this.getEvents(aggregateId);
    
    // Check for concurrent modifications
    if (expectedVersion !== -1 && currentEvents.length !== expectedVersion) {
      throw new ConcurrencyError(
        `Expected version ${expectedVersion}, but found ${currentEvents.length} events`
      );
    }
    
    // Assign sequential versions
    newEvents.forEach((event, index) => {
      event.version = currentEvents.length + index + 1;
    });
    
    await this.appendEvents(aggregateId, newEvents);
  }
}
```

### **9. Common Pitfalls & Anti-Patterns**

**Implementation Mistakes:**
1. **Events Too Fine-Grained:** `OrderCustomerNameChanged`, `OrderCustomerEmailChanged`
2. **Events Too Coarse:** `OrderUpdated` (what changed?)
3. **Business Logic in Events:** Events should be facts, not behavior
4. **Ignoring Idempotency:** Replaying events should be safe

**Performance Issues:**
- **Event Explosion:** Too many events per aggregate
- **Long Replay Times:** No snapshot strategy
- **Projection Lag:** Read models too far behind
- **Storage Costs:** Keeping events forever

**Design Anti-Patterns:**
1. **Using Events as Messages:** Events are facts, not commands
2. **Exposing Event Internals:** Events are part of domain model
3. **Event Sourcing Everything:** Not all entities need event sourcing
4. **Ignoring Schema Evolution:** Events will change over time

### **10. When to Use Event Sourcing**

**Good Fit For:**
1. **Financial Systems:** Complete audit trail required
2. **Collaborative Tools:** Version history important (Google Docs)
3. **Trading Systems:** Need to replay market events
4. **Legal/Compliance:** Regulatory requirements for audit

**Poor Fit For:**
1. **Simple CRUD Applications:** Over-engineering
2. **High-Volume Simple Data:** Event overhead too high
3. **Limited Audit Requirements:** No need for history
4. **Small Teams:** Complexity may be overwhelming

### **11. Interview-Specific Considerations**

**Common Interview Questions:**
1. "How do you handle event schema changes?"
2. "What's the difference between events and commands?"
3. "How do you ensure idempotency when replaying events?"
4. "When would you choose event sourcing vs traditional CRUD?"

**Design Discussion Points:**
- Trade-off between complexity and benefits
- Performance implications of event replay
- Integration with existing systems
- Team skill requirements

**Real-World Examples:**
1. **Banking:** Transaction history and audit
2. **E-commerce:** Order state transitions
3. **IoT:** Device state changes over time
4. **Collaboration Tools:** Document edit history

---

## **Before-Interview Checklist**

**✅ Core Concept Understanding**
- [ ] Difference between events and state
- [ ] Event replay and state reconstruction
- [ ] Immutability of events
- [ ] Event store vs traditional database

**✅ Implementation Details**
- [ ] Aggregate pattern and command handling
- [ ] Event sourcing with CQRS
- [ ] Projections and read models
- [ ] Snapshot strategies

**✅ Use Case Identification**
- [ ] When event sourcing is appropriate
- [ ] Audit trail requirements
- [ ] Temporal query needs
- [ ] Business complexity justification

**✅ Technical Considerations**
- [ ] Event schema design and versioning
- [ ] Concurrency control
- [ ] Performance optimization
- [ ] Storage and retention policies

**✅ Real-World Examples**
- [ ] Financial transaction auditing
- [ ] Order management systems
- [ ] Collaborative editing
- [ ] IoT state tracking

---

## **60-Second Recap (Bullet Format)**

**Core Concept:**
- Store state changes as immutable events
- Reconstruct current state by replaying events
- Append-only event log as source of truth

**Key Components:**
- **Events:** Facts about what happened
- **Event Store:** Append-only log of events
- **Aggregates:** Process commands, emit events
- **Projections:** Build read models from events

**Benefits:**
- Complete audit trail
- Ability to reconstruct past states
- Natural fit for complex business logic
- Enables temporal queries

**Common Use Cases:**
- Financial systems (audit requirements)
- Collaborative applications (version history)
- Trading systems (event replay)
- Compliance-heavy industries

**Implementation Patterns:**
- Combine with CQRS for read/write separation
- Use snapshots to optimize replay
- Create multiple projections for different views
- Version events for schema evolution

**When to Use:**
- Need complete audit trail
- Complex business logic with state transitions
- Temporal query requirements
- Distributed system with eventual consistency

**When to Avoid:**
- Simple CRUD applications
- No audit requirements
- Limited development resources
- Performance-critical simple operations

**Best Practices:**
- Design events as immutable facts
- Use meaningful business event names
- Implement snapshotting for performance
- Plan for event schema evolution

**Common Challenges:**
- Event schema changes over time
- Performance of event replay
- Storage management for events
- Complexity of implementation

---

## **Sources & References**

**Primary Source:**
- Microsoft Azure Architecture Center: Event Sourcing Pattern
- URL: https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing

**Additional References:**
- "Domain-Driven Design" by Eric Evans
- "Implementing Domain-Driven Design" by Vaughn Vernon
- EventStoreDB documentation
- Greg Young's event sourcing materials

**Related Azure Patterns:**
- CQRS Pattern (often combined with event sourcing)
- Materialized View Pattern
- Saga Pattern
- Compensating Transaction Pattern

**Interview Citation Format:**
- "As outlined in Microsoft's Event Sourcing pattern documentation..."
- "Following the immutable event log approach for audit trails..."
- "The pattern enables temporal queries and state reconstruction as described in Azure's architecture guidance..."