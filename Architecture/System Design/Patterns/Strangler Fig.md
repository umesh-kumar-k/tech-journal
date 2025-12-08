
## Strangler Fig Pattern

Strangler Fig pattern incrementally migrates legacy systems by routing requests through a façade/proxy that gradually shifts traffic from old to new services, enabling zero-downtime replacement.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/strangler-fig)​

- Façade intercepts client requests; routes to legacy (initially) → new services (progressively)
    
- Run both systems in parallel; migrate features iteratively by complexity/ROI
    
- Decommission legacy after full migration; façade becomes permanent adapter or removed[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/strangler-fig)​
    

## Tools and Frameworks

|Platform|Tools/Frameworks|Example Use Case|
|---|---|---|
|API Gateway|Azure API Management, AWS API Gateway, Kong|Route /orders → legacy vs /users → new microservice|
|Service Mesh|Istio VirtualService, Linkerd Route|Canary deployments during migration|
|Reverse Proxy|NGINX, HAProxy|URL-based routing to old/new backends|
|Feature Flags|LaunchDarkly, Unleash, Azure App Configuration|Toggle traffic by feature/user segment|
|Database|Database migration tools (ETL), CDC (Change Data Capture)|Shadow writes during DB migration|

## Interview Checklist

- Define: Proxy façade gradually routes traffic from legacy → new system[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/strangler-fig)​
    
- Process: Façade → legacy (100%) → migrate features → new services → decommission legacy
    
- Routing: URL paths, headers, feature flags, canary releases
    
- Data Migration: Shared DB → shadow writes → new DB → cutover
    
- Benefits: Zero-downtime, risk reduction, parallel operation, ROI prioritization
    
- Gotchas: Façade as bottleneck/SPoF, data sync complexity, testing both systems
    
- Monitoring: Split traffic metrics, error rates per backend, migration progress
    
- When: Large legacy migration; avoid small/simple replacements
    

## 60-Second Recap

- **Core**: Façade proxy shifts traffic legacy → new iteratively; zero-downtime[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/strangler-fig)​
    
- **Why**: Risk-managed migration; run both systems in parallel
    
- **Flow**: Client → API Gateway → /legacy/* → old, /new/* → modern services
    
- **Tools**: APIM/AWS Gateway, Istio, NGINX, feature flags, CDC for DB
    
- **Wins**: Gradual migration, canary testing, ROI prioritization
    

**Reference**: [https://learn.microsoft.com/en-us/azure/architecture/patterns/strangler-fig](https://learn.microsoft.com/en-us/azure/architecture/patterns/strangler-fig)[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/strangler-fig)​

1. [https://learn.microsoft.com/en-us/azure/architecture/patterns/strangler-fig](https://learn.microsoft.com/en-us/azure/architecture/patterns/strangler-fig)
# **Strangler Fig Pattern**

## **Core Thesis**
The Strangler Fig pattern incrementally migrates a legacy system by gradually replacing functionality with new services, routing traffic to the new system over time while keeping the old system operational, minimizing risk and enabling continuous delivery during migration.

---

## **Detailed Notes**

### **1. Problem Statement & Context**

**Legacy System Challenges:**
1. **Monolithic Architecture:** Difficult to modify and scale
2. **Technical Debt:** Outdated technologies and practices
3. **High Risk:** Big bang rewrites often fail
4. **Business Pressure:** Need to deliver new features while maintaining existing

**Migration Goals:**
- Replace system without business disruption
- Deliver value incrementally
- Minimize risk of failure
- Enable continuous delivery

### **2. The Strangler Fig Analogy**

**Natural World Inspiration:**
```
Strangler Fig in Nature:         Software Migration:
┌─────────────────┐             ┌─────────────────┐
│  Host Tree      │             │  Legacy System  │
│  (Old System)   │             │                 │
│                 │             │                 │
│  Fig Seeds      │             │  New Services   │
│  Land on        │             │  Wrap Around    │
│  Branches       │             │  Legacy         │
│                 │             │                 │
│  Fig Grows      │             │  Gradually      │
│  Around Tree    │             │  Replace Parts  │
│                 │             │                 │
│  Tree Dies      │             │  Legacy Retired │
│  Fig Stands     │             │  New System Runs│
└─────────────────┘             └─────────────────┘
```

### **3. Implementation Phases**

**Three-Phase Migration Strategy:**

**Phase 1: Transform (Wrap & Intercept)**
```
┌─────────────────────────────────────┐
│         Legacy System               │
│  ┌─────────────────────────────┐   │
│  │  Original Functionality     │   │
│  └─────────────────────────────┘   │
└─────────────────────────────────────┘
              │
              ▼
┌─────────────────────────────────────┐
│         Strangler Facade            │
│  • Routes requests                  │
│  • Can intercept calls              │
│  • Collects metrics                 │
└─────────────────────────────────────┘
```

**Phase 2: Coexist (Parallel Run)**
```
┌─────────────────────────────────────┐
│         Strangler Facade            │
│                                     │
│  Request ──┐                        │
│            ├───► New Implementation │
│  Request ──┤                        │
│            └───► Legacy System      │
│                                     │
└─────────────────────────────────────┘
```

**Phase 3: Eliminate (Legacy Retirement)**
```
┌─────────────────────────────────────┐
│         New System                  │
│  ┌─────────────────────────────┐   │
│  │  All functionality migrated │   │
│  └─────────────────────────────┘   │
└─────────────────────────────────────┘
              │
              ▼
┌─────────────────────────────────────┐
│         Legacy System               │
│         (Retired/Decommissioned)    │
└─────────────────────────────────────┘
```

### **4. Implementation Examples**

**JavaScript/Node.js Strangler Facade:**
```javascript
// Strangler Facade - Route requests to old or new implementation
class StranglerFacade {
  constructor() {
    this.routingConfig = {
      // Feature flags for gradual migration
      features: {
        userProfile: { useNew: true, percentage: 100 },
        orderProcessing: { useNew: false, percentage: 0 },
        payment: { useNew: true, percentage: 50 }, // 50% traffic to new
        inventory: { useNew: false, percentage: 0 }
      },
      // Fallback to legacy if new implementation fails
      fallbackEnabled: true
    };
    
    // Initialize clients
    this.legacyClient = new LegacySystemClient();
    this.newSystemClient = new NewSystemClient();
    
    // Metrics collection
    this.metrics = {
      requests: { legacy: 0, new: 0 },
      errors: { legacy: 0, new: 0 },
      latency: { legacy: [], new: [] }
    };
  }
  
  // Generic route method
  async routeRequest(featureName, operation, ...args) {
    const config = this.routingConfig.features[featureName];
    const useNew = this.shouldUseNewImplementation(config);
    
    const startTime = Date.now();
    
    try {
      let result;
      
      if (useNew) {
        // Try new implementation first
        result = await this.callNewSystem(featureName, operation, ...args);
        this.metrics.requests.new++;
      } else {
        // Use legacy system
        result = await this.callLegacySystem(featureName, operation, ...args);
        this.metrics.requests.legacy++;
      }
      
      const latency = Date.now() - startTime;
      this.recordLatency(featureName, useNew ? 'new' : 'legacy', latency);
      
      return result;
      
    } catch (error) {
      const errorType = useNew ? 'new' : 'legacy';
      this.metrics.errors[errorType]++;
      
      // Fallback logic
      if (this.routingConfig.fallbackEnabled && useNew) {
        console.log(`New system failed for ${featureName}, falling back to legacy`);
        return this.callLegacySystem(featureName, operation, ...args);
      }
      
      throw error;
    }
  }
  
  shouldUseNewImplementation(config) {
    if (!config.useNew) return false;
    if (config.percentage === 100) return true;
    if (config.percentage === 0) return false;
    
    // Percentage-based routing
    return Math.random() * 100 < config.percentage;
  }
  
  async callNewSystem(featureName, operation, ...args) {
    // Map to new system implementation
    switch (featureName) {
      case 'userProfile':
        return this.newSystemClient.userProfile[operation](...args);
      case 'payment':
        return this.newSystemClient.payment[operation](...args);
      default:
        throw new Error(`Feature ${featureName} not implemented in new system`);
    }
  }
  
  async callLegacySystem(featureName, operation, ...args) {
    // Map to legacy system
    return this.legacyClient.call(operation, ...args);
  }
  
  recordLatency(featureName, system, latency) {
    const key = `${featureName}:${system}`;
    if (!this.metrics.latency[key]) {
      this.metrics.latency[key] = [];
    }
    this.metrics.latency[key].push(latency);
    
    // Keep only last 1000 measurements
    if (this.metrics.latency[key].length > 1000) {
      this.metrics.latency[key].shift();
    }
  }
  
  // Feature-specific methods
  async getUserProfile(userId) {
    return this.routeRequest('userProfile', 'getUser', userId);
  }
  
  async processPayment(orderId, amount) {
    return this.routeRequest('payment', 'processPayment', orderId, amount);
  }
  
  async createOrder(items) {
    return this.routeRequest('orderProcessing', 'createOrder', items);
  }
  
  // Gradual migration control
  enableFeature(featureName, percentage = 0) {
    if (!this.routingConfig.features[featureName]) {
      this.routingConfig.features[featureName] = { useNew: false, percentage: 0 };
    }
    this.routingConfig.features[featureName].useNew = true;
    this.routingConfig.features[featureName].percentage = percentage;
    console.log(`Enabled ${featureName} at ${percentage}%`);
  }
  
  disableFeature(featureName) {
    if (this.routingConfig.features[featureName]) {
      this.routingConfig.features[featureName].useNew = false;
      this.routingConfig.features[featureName].percentage = 0;
    }
  }
}

// Usage
const facade = new StranglerFacade();

// Initially: All traffic to legacy
const user = await facade.getUserProfile('user123');

// Enable new implementation for 10% of users
facade.enableFeature('userProfile', 10);

// Increase to 50%
facade.enableFeature('userProfile', 50);

// Eventually: 100% to new system
facade.enableFeature('userProfile', 100);

// Disable legacy completely for this feature
facade.disableFeature('userProfile');
```

**Java/Spring Boot API Gateway Strangler:**
```java
// Spring Cloud Gateway as Strangler
@Configuration
public class StranglerGatewayConfig {
    
    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder builder) {
        return builder.routes()
            // Route based on feature flags
            .route("user-profile", r -> r
                .path("/api/users/**")
                .filters(f -> f
                    .filter(featureFlagFilter())
                    .circuitBreaker(config -> config
                        .setName("userProfileCB")
                        .setFallbackUri("forward:/fallback/user-profile")))
                .uri("lb://new-user-service"))
            
            .route("legacy-users", r -> r
                .path("/api/users/**")
                .and()
                .predicate(exchange -> 
                    !FeatureFlags.isEnabled("new-user-service"))
                .uri("lb://legacy-user-service"))
            
            .route("orders", r -> r
                .path("/api/orders/**")
                .and()
                .predicate(exchange -> 
                    FeatureFlags.getPercentage("new-order-service") > 
                    Math.random() * 100)
                .uri("lb://new-order-service"))
            
            .route("legacy-orders", r -> r
                .path("/api/orders/**")
                .uri("lb://legacy-order-service"))
            
            .build();
    }
    
    @Bean
    public GatewayFilter featureFlagFilter() {
        return (exchange, chain) -> {
            String path = exchange.getRequest().getPath().toString();
            
            // Check if feature is migrated
            if (path.startsWith("/api/users") && 
                FeatureFlags.isEnabled("new-user-service")) {
                // Route to new service
                return chain.filter(exchange.mutate()
                    .request(exchange.getRequest().mutate()
                        .header("X-Strangler-Source", "new")
                        .build())
                    .build());
            }
            
            // Default to legacy
            return chain.filter(exchange.mutate()
                .request(exchange.getRequest().mutate()
                    .header("X-Strangler-Source", "legacy")
                    .build())
                .build());
        };
    }
}

// Feature flag service
@Component
public class FeatureFlags {
    private static Map<String, FeatureConfig> features = new HashMap<>();
    
    static {
        // Initial configuration
        features.put("new-user-service", new FeatureConfig(true, 100));
        features.put("new-order-service", new FeatureConfig(false, 0));
        features.put("new-payment-service", new FeatureConfig(true, 50));
    }
    
    public static boolean isEnabled(String feature) {
        FeatureConfig config = features.get(feature);
        return config != null && config.enabled && 
               config.percentage > Math.random() * 100;
    }
    
    public static int getPercentage(String feature) {
        FeatureConfig config = features.get(feature);
        return config != null && config.enabled ? config.percentage : 0;
    }
    
    public static void enable(String feature, int percentage) {
        features.put(feature, new FeatureConfig(true, percentage));
    }
    
    static class FeatureConfig {
        boolean enabled;
        int percentage;
        
        FeatureConfig(boolean enabled, int percentage) {
            this.enabled = enabled;
            this.percentage = percentage;
        }
    }
}
```

### **5. Data Migration Strategies**

**Dual-Write Pattern:**
```javascript
// Write to both old and new systems during migration
class DualWriteService {
  constructor() {
    this.legacyDB = new LegacyDatabase();
    this.newDB = new NewDatabase();
    this.syncQueue = [];
  }
  
  async createUser(userData) {
    try {
      // Write to new system first (primary)
      const newUserId = await this.newDB.createUser(userData);
      
      // Write to legacy system
      const legacyUserId = await this.legacyDB.createUser({
        ...userData,
        newSystemId: newUserId // Store reference
      });
      
      // Store mapping
      await this.storeIdMapping(newUserId, legacyUserId);
      
      return { newUserId, legacyUserId };
      
    } catch (error) {
      // If new system fails, still write to legacy
      if (error instanceof NewSystemError) {
        const legacyUserId = await this.legacyDB.createUser(userData);
        // Queue for later sync
        await this.queueForSync('create', userData, null, legacyUserId);
        return { newUserId: null, legacyUserId };
      }
      throw error;
    }
  }
  
  async updateUser(userId, updates) {
    // Determine which ID we have
    const mapping = await this.getIdMapping(userId);
    
    const promises = [];
    
    if (mapping.newUserId) {
      promises.push(this.newDB.updateUser(mapping.newUserId, updates));
    }
    
    if (mapping.legacyUserId) {
      promises.push(this.legacyDB.updateUser(mapping.legacyUserId, updates));
    }
    
    await Promise.all(promises);
  }
  
  async queueForSync(operation, data, newId, legacyId) {
    this.syncQueue.push({
      operation,
      data,
      newId,
      legacyId,
      timestamp: new Date(),
      retries: 0
    });
    
    // Process queue in background
    this.processSyncQueue();
  }
  
  async processSyncQueue() {
    while (this.syncQueue.length > 0) {
      const item = this.syncQueue[0];
      
      try {
        if (item.newId === null) {
          // Need to create in new system
          const newId = await this.newDB.createUser(item.data);
          await this.storeIdMapping(newId, item.legacyId);
        } else if (item.legacyId === null) {
          // Need to create in legacy system
          const legacyId = await this.legacyDB.createUser(item.data);
          await this.storeIdMapping(item.newId, legacyId);
        }
        
        // Success - remove from queue
        this.syncQueue.shift();
        
      } catch (error) {
        item.retries++;
        if (item.retries > 3) {
          // Move to dead letter queue for manual intervention
          console.error(`Failed to sync after 3 retries:`, item);
          this.moveToDeadLetterQueue(item);
          this.syncQueue.shift();
        } else {
          // Exponential backoff
          await this.sleep(1000 * Math.pow(2, item.retries));
        }
      }
    }
  }
}
```

### **6. Migration Approaches**

**Feature-Based Migration:**
```javascript
// Migrate by business capability/feature
class FeatureBasedMigration {
  constructor() {
    this.migrationPlan = [
      {
        feature: 'user-management',
        steps: [
          { action: 'setup-facade', duration: '1 week' },
          { action: 'migrate-reads', percentage: 10 },
          { action: 'migrate-writes', percentage: 10 },
          { action: 'increase-traffic', to: 50 },
          { action: 'increase-traffic', to: 100 },
          { action: 'decommission-legacy', after: '2 weeks stable' }
        ],
        status: 'in-progress',
        currentStep: 2
      },
      {
        feature: 'order-processing',
        steps: [/* similar structure */],
        status: 'planned',
        currentStep: 0
      },
      {
        feature: 'payment',
        steps: [/* similar structure */],
        status: 'completed',
        currentStep: 6
      }
    ];
  }
  
  getNextFeatureToMigrate() {
    return this.migrationPlan
      .filter(f => f.status === 'planned')
      .sort((a, b) => a.priority - b.priority)[0];
  }
  
  calculateMigrationRisk(feature) {
    // Consider factors:
    // 1. Complexity of feature
    // 2. Business criticality
    // 3. Data volume
    // 4. Integration points
    // 5. Team expertise
    
    return {
      feature: feature.name,
      riskScore: this.calculateRiskScore(feature),
      recommendedApproach: this.getApproach(feature),
      estimatedTimeline: this.getTimeline(feature)
    };
  }
}
```

### **7. Monitoring & Observability**

**Migration Dashboard:**
```javascript
class MigrationDashboard {
  constructor() {
    this.metrics = {
      traffic: {
        legacy: { requests: 0, errors: 0, latency: [] },
        new: { requests: 0, errors: 0, latency: [] }
      },
      features: {}, // Per-feature metrics
      dataConsistency: {}, // Data sync status
      businessMetrics: {} // Key business indicators
    };
  }
  
  async collectMetrics() {
    // Collect from various sources
    const trafficMetrics = await this.collectTrafficMetrics();
    const dataMetrics = await this.checkDataConsistency();
    const businessMetrics = await this.collectBusinessMetrics();
    
    // Update dashboard
    this.updateDashboard({
      traffic: trafficMetrics,
      dataConsistency: dataMetrics,
      business: businessMetrics
    });
    
    // Check for anomalies
    this.checkForAnomalies();
  }
  
  async checkDataConsistency() {
    // Compare data between old and new systems
    const inconsistencies = [];
    
    // Sample comparison
    const sampleUsers = await this.getSampleData('users', 100);
    
    for (const user of sampleUsers) {
      const legacyData = await this.legacyDB.getUser(user.id);
      const newData = await this.newDB.getUser(user.id);
      
      if (!this.areEqual(legacyData, newData)) {
        inconsistencies.push({
          entity: 'user',
          id: user.id,
          differences: this.findDifferences(legacyData, newData)
        });
      }
    }
    
    return {
      sampleSize: sampleUsers.length,
      inconsistencies: inconsistencies.length,
      details: inconsistencies
    };
  }
  
  checkForAnomalies() {
    // Alert if:
    // 1. Error rate increases significantly
    // 2. Latency degrades
    // 3. Business metrics drop
    // 4. Data inconsistencies exceed threshold
    
    const errorRateIncrease = 
      (this.metrics.new.errors / this.metrics.new.requests) -
      (this.metrics.legacy.errors / this.metrics.legacy.requests);
    
    if (errorRateIncrease > 0.05) { // 5% increase
      this.triggerAlert('Error rate increased in new system');
    }
    
    if (this.metrics.dataConsistency.inconsistencies > 10) {
      this.triggerAlert('High data inconsistency detected');
    }
  }
}
```

### **8. Rollback Strategies**

**Feature Flag Rollback:**
```javascript
class RollbackManager {
  constructor() {
    this.rollbackTriggers = [
      {
        condition: 'error-rate > 5%',
        action: 'reduce-traffic',
        severity: 'high'
      },
      {
        condition: 'p95-latency > 2x',
        action: 'reduce-traffic',
        severity: 'medium'
      },
      {
        condition: 'business-metrics-drop > 10%',
        action: 'full-rollback',
        severity: 'critical'
      }
    ];
  }
  
  async checkAndRollback(feature, metrics) {
    for (const trigger of this.rollbackTriggers) {
      if (this.evaluateCondition(trigger.condition, metrics)) {
        console.log(`Trigger activated: ${trigger.condition}`);
        await this.executeRollbackAction(trigger.action, feature);
        return true;
      }
    }
    return false;
  }
  
  async executeRollbackAction(action, feature) {
    switch (action) {
      case 'reduce-traffic':
        // Reduce traffic to new system
        await this.adjustTrafficPercentage(feature, 0);
        break;
        
      case 'full-rollback':
        // Completely disable new implementation
        await this.disableFeature(feature);
        // Notify teams
        await this.notifyTeams(feature, 'full-rollback');
        break;
        
      case 'pause-migration':
        // Stop further migration steps
        await this.pauseMigration(feature);
        break;
    }
  }
  
  async adjustTrafficPercentage(feature, percentage) {
    // Update feature flags
    const config = this.getFeatureConfig(feature);
    config.percentage = percentage;
    
    // Log the change
    console.log(`Rollback: Set ${feature} traffic to ${percentage}%`);
    
    // Notify monitoring
    await this.logRollbackEvent(feature, percentage);
  }
}
```

### **9. Benefits & Advantages**

**Risk Mitigation Benefits:**
1. **Gradual Migration:** Low risk compared to big bang
2. **Rollback Capability:** Easy to revert if issues arise
3. **Continuous Delivery:** New features can still be delivered
4. **A/B Testing:** Compare old vs new performance

**Business Benefits:**
1. **Zero Downtime:** Migration without business disruption
2. **Incremental Value:** Benefits realized gradually
3. **Team Learning:** Skills develop alongside migration
4. **Budget Flexibility:** Spread costs over time

**Technical Benefits:**
- Modern architecture incrementally
- Test new system with real traffic
- Validate performance at scale
- Maintain system stability

### **10. Common Pitfalls & Anti-Patterns**

**Implementation Mistakes:**
1. **Inadequate Testing:** Not testing integration thoroughly
2. **Poor Monitoring:** No visibility into migration progress
3. **Data Inconsistency:** Not handling sync properly
4. **Scope Creep:** Trying to migrate too much at once

**Design Anti-Patterns:**
1. **Big Bang Strangler:** Defeating the purpose
2. **Tight Coupling:** New system too dependent on old
3. **No Rollback Plan:** Assuming everything will work
4. **Ignoring Business Metrics:** Only watching technical metrics

### **11. When to Use Strangler Pattern**

**Good Fit For:**
1. **Monolithic to Microservices:** Breaking down large apps
2. **Technology Stack Migration:** Moving to new platforms
3. **Cloud Migration:** Moving from on-prem to cloud
4. **Legacy Modernization:** Updating old systems

**Poor Fit For:**
1. **Small Applications:** Over-engineering
2. **Short-term Systems:** Not worth the effort
3. **Complete Rewrites:** When strangle not possible
4. **Time-critical Replacements:** When gradual not acceptable

### **12. Interview-Specific Considerations**

**Common Interview Questions:**
1. "How do you decide what to migrate first?"
2. "What metrics do you track during migration?"
3. "How do you handle data synchronization?"
4. "When would you choose strangler vs rewrite?"

**Design Discussion Points:**
- Prioritization of migration features
- Data consistency strategies
- Risk assessment and mitigation
- Team organization for migration

---

## **Before-Interview Checklist**

**✅ Core Concept Understanding**
- [ ] Strangler fig analogy and phases
- [ ] Difference from big bang rewrite
- [ ] Risk mitigation strategies
- [ ] Gradual migration benefits

**✅ Implementation Strategies**
- [ ] Feature flag based routing
- [ ] API Gateway as strangler facade
- [ ] Data synchronization approaches
- [ ] Traffic shifting techniques

**✅ Migration Planning**
- [ ] Feature prioritization criteria
- [ ] Risk assessment methods
- [ ] Timeline estimation
- [ ] Team organization

**✅ Monitoring & Observability**
- [ ] Key metrics to track
- [ ] Anomaly detection
- [ ] Business metric monitoring
- [ ] Data consistency checks

**✅ Rollback & Risk Management**
- [ ] Rollback trigger conditions
- [ ] Rollback procedures
- [ ] Communication plans
- [ ] Post-mortem processes

---

## **60-Second Recap (Bullet Format)**

**Core Concept:**
- Gradually replace legacy system with new implementation
- Route traffic incrementally from old to new
- Inspired by strangler fig plant that grows around host tree

**Three Phases:**
1. **Transform:** Create facade, intercept calls
2. **Coexist:** Run old and new in parallel
3. **Eliminate:** Retire legacy system

**Key Implementation:**
- **Strangler Facade:** Routes requests (API Gateway, proxy)
- **Feature Flags:** Control traffic routing percentage
- **Dual Writes:** Synchronize data between systems
- **Monitoring:** Track migration metrics

**Benefits:**
- Minimal risk compared to big bang rewrite
- Zero downtime migration
- Incremental value delivery
- Continuous delivery possible

**When to Use:**
- Large monolithic applications
- High-risk system replacements
- Need for business continuity
- Complex integration landscapes

**When to Avoid:**
- Small, simple systems
- Emergency replacements
- When complete rewrite is simpler
- Lack of migration bandwidth

**Best Practices:**
- Migrate by business capability
- Implement comprehensive monitoring
- Maintain rollback capability
- Communicate progress to stakeholders

**Common Challenges:**
- Data synchronization complexity
- Managing dual systems overhead
- Team coordination
- Scope creep

**Success Factors:**
- Strong product ownership
- Comprehensive testing
- Good monitoring and alerting
- Clear migration roadmap

---

## **Sources & References**

**Primary Source:**
- Microsoft Azure Architecture Center: Strangler Fig Pattern
- URL: https://learn.microsoft.com/en-us/azure/architecture/patterns/strangler-fig

**Additional References:**
- Martin Fowler's Strangler Fig Application article
- "Monolith to Microservices" by Sam Newman
- Legacy system migration case studies
- Feature flag management resources

**Related Azure Patterns:**
- Anti-Corruption Layer Pattern
- Gateway Aggregation Pattern
- Ambassador Pattern
- Sidecar Pattern

**Interview Citation Format:**
- "As described in Microsoft's Strangler Fig pattern documentation..."
- "Following the incremental migration approach for legacy systems..."
- "The pattern enables risk-minimized replacement as outlined in Azure's architecture guidance..."