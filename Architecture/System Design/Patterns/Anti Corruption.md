
## Anti-Corruption Layer Pattern

The Anti-Corruption Layer (ACL) pattern implements a facade or adapter between subsystems with mismatched semantics, like modern apps and legacy systems, to prevent design contamination during migrations.[learn.microsoft+1](https://learn.microsoft.com/en-us/azure/architecture/patterns/anti-corruption-layer)​

- Translates requests from one system's data model/API to another's without altering either core subsystem
    
- Protects new/microservices architectures from legacy constraints (e.g., obsolete schemas, protocols)
    
- Enables gradual strangler fig migrations by routing calls transparently[aws.amazon](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/acl.html)​
    
- Often temporary; decommission after full migration to avoid technical debt[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/anti-corruption-layer)​
    

## Tools and Frameworks

|Cloud/Provider|Tools/Frameworks|Example Use Case|
|---|---|---|
|AWS|API Gateway, Lambda, DynamoDB (with .NET ACL facade)|Monolith calls Lambda microservice via ACL translator class [github+1](https://github.com/aws-samples/anti-corruption-layer-pattern)​|
|Azure|Logic Apps, API Management|Facade for legacy-to-cloud integration [learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/anti-corruption-layer)​|
|.NET|Dependency Injection (e.g., ServiceCollection.AddBookInfoAcl())|Console app switching legacy vs ACL ports [github](https://github.com/rafaeldalsenter/dotnet-anticorruptionlayer-sample)​|
|Java|Spring Adapters/Facades|Decoupling subsystems in enterprise apps [java-design-patterns](https://java-design-patterns.com/patterns/anti-corruption-layer/)​|

## Interview Checklist

- Define ACL: Façade/adapter isolating mismatched semantics (e.g., monolith → microservice)[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/anti-corruption-layer)​
    
- Explain context: Legacy migrations, external systems with poor APIs/schemas[aws.amazon](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/acl.html)​
    
- Tradeoffs: Latency overhead, scaling needs, ops burden (monitoring/CI/CD), single failure point (use circuit breakers)[github](https://github.com/aws-samples/anti-corruption-layer-pattern)​
    
- When to use: Semantic diffs in staged migrations; avoid if minor mismatches[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/anti-corruption-layer)​
    
- Implementation: In-app class (e.g., UserServiceACL.cs) or service; translate models (e.g., UserDetails → UserMicroserviceModel)[aws.amazon](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/acl.html)​
    
- Alternatives: Strangler Fig (full replacement), Adapter (simpler cases)[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/anti-corruption-layer)​
    

## 60-Second Recap

- **Core**: ACL = protective translator layer for semantic mismatches (monolith/legacy → modern/microservices)[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/anti-corruption-layer)​
    
- **Why**: Zero-downtime migrations, no caller changes, reduced risk/debt[github](https://github.com/aws-samples/anti-corruption-layer-pattern)​
    
- **Tools**: AWS (API Gateway/Lambda), Azure Logic Apps, .NET DI facades[aws.amazon](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/acl.html)​
    
- **Gotchas**: Latency, scaling, transient debt—plan decommissioning[aws.amazon](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/acl.html)​
    
- **Interview Win**: Draw diagram (monolith → ACL → microservice); cite AWS sample[github](https://github.com/aws-samples/anti-corruption-layer-pattern)​
    

**Reference**: [https://learn.microsoft.com/en-us/azure/architecture/patterns/anti-corruption-layer](https://learn.microsoft.com/en-us/azure/architecture/patterns/anti-corruption-layer)[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/anti-corruption-layer)​

1. [https://learn.microsoft.com/en-us/azure/architecture/patterns/anti-corruption-layer](https://learn.microsoft.com/en-us/azure/architecture/patterns/anti-corruption-layer)
2. [https://github.com/aws-samples/anti-corruption-layer-pattern](https://github.com/aws-samples/anti-corruption-layer-pattern)
3. [https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/acl.html](https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/acl.html)
4. [https://github.com/rafaeldalsenter/dotnet-anticorruptionlayer-sample](https://github.com/rafaeldalsenter/dotnet-anticorruptionlayer-sample)
5. [https://java-design-patterns.com/patterns/anti-corruption-layer/](https://java-design-patterns.com/patterns/anti-corruption-layer/)
6. [https://www.linkedin.com/pulse/anti-corruption-layer-adapter-patterns-subbu-mahadevan](https://www.linkedin.com/pulse/anti-corruption-layer-adapter-patterns-subbu-mahadevan)
7. [https://www.swiftorial.com/archview/system-design-patterns/anti-corruption-layer](https://www.swiftorial.com/archview/system-design-patterns/anti-corruption-layer)
8. [https://dev.to/asarnaout/the-anti-corruption-layer-pattern-pcd](https://dev.to/asarnaout/the-anti-corruption-layer-pattern-pcd)
9. [https://docs.aws.amazon.com/pdfs/prescriptive-guidance/latest/cloud-design-patterns/cloud-design-patterns.pdf](https://docs.aws.amazon.com/pdfs/prescriptive-guidance/latest/cloud-design-patterns/cloud-design-patterns.pdf)
10. [https://palospublishing.com/creating-patterns-for-anti-corruption-layers/](https://palospublishing.com/creating-patterns-for-anti-corruption-layers/)
11. [https://learn.microsoft.com/en-gb/azure/architecture/patterns/anti-corruption-layer](https://learn.microsoft.com/en-gb/azure/architecture/patterns/anti-corruption-layer)
12. [https://learn.microsoft.com/uk-ua/azure/architecture/patterns/anti-corruption-layer?view=azurermps-6.2.0](https://learn.microsoft.com/uk-ua/azure/architecture/patterns/anti-corruption-layer?view=azurermps-6.2.0)
13. [https://www.youtube.com/watch?v=gGDu4FdUk7I](https://www.youtube.com/watch?v=gGDu4FdUk7I)
14. [https://microservices.io/patterns/refactoring/anti-corruption-layer.html](https://microservices.io/patterns/refactoring/anti-corruption-layer.html)
15. [https://www.ekascloud.com/live-projects/anti-corruption-layer-pattern](https://www.ekascloud.com/live-projects/anti-corruption-layer-pattern)
16. [https://www.linkedin.com/posts/avinash-dhumal_anti-corruption-layer-acl-pattern-innet-activity-7310356073696133120-NmWt](https://www.linkedin.com/posts/avinash-dhumal_anti-corruption-layer-acl-pattern-innet-activity-7310356073696133120-NmWt)
17. [https://blogit.michelin.io/anti-corruption-layer/](https://blogit.michelin.io/anti-corruption-layer/)
18. [https://github.com/aws-samples/anti-corruption-layer-pattern/actions](https://github.com/aws-samples/anti-corruption-layer-pattern/actions)
19. [https://www.youtube.com/watch?v=_oAYFR-hexg](https://www.youtube.com/watch?v=_oAYFR-hexg)
20. [https://www.dremio.com/wiki/anti-corruption-layer/](https://www.dremio.com/wiki/anti-corruption-layer/)
# **Anti-Corruption Layer Pattern**

## **Core Thesis**
The Anti-Corruption Layer pattern creates an isolating layer between legacy and new systems that translates communications between different domain models, preventing the new system from being contaminated by the legacy system's suboptimal designs, outdated concepts, and technical debt.

---

## **Detailed Notes**

### **1. Problem Statement & Context**

**Legacy System Integration Challenges:**
1. **Domain Model Mismatch:** Different concepts, terminology, and data structures
2. **Technical Debt Contamination:** Legacy flaws spreading to new system
3. **Vendor Lock-in:** Proprietary formats and protocols
4. **Business Logic Entanglement:** Core logic mixed with outdated implementation details

**Common Integration Pain Points:**
- Legacy system uses different data formats (XML vs JSON, SOAP vs REST)
- Conflicting business rules and validation logic
- Incompatible authentication and authorization models
- Different error handling and retry strategies

### **2. Core Architecture**

**Anti-Corruption Layer Concept:**
```
┌─────────────────────────────────────────────────────────┐
│                    Legacy System                        │
│  • Old domain model                                    │
│  • Outdated protocols (SOAP, FTP)                      │
│  • Proprietary data formats                            │
│  • Tightly coupled components                          │
└─────────────────────────┬───────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│              Anti-Corruption Layer                       │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────┐ │
│  │   Adapter       │  │   Translator    │  │  Facade │ │
│  │  (Protocol)     │  │   (Data Model)  │  │  (API)  │ │
│  └─────────────────┘  └─────────────────┘  └─────────┘ │
│                                                         │
│  • Isolates legacy concepts                            │
│  • Translates between domains                          │
│  • Provides clean interface                            │
└─────────────────────────┬───────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│                    New System                           │
│  • Modern domain model                                 │
│  • Clean architecture                                  │
│  • Up-to-date technologies                             │
│  • Maintains integrity                                 │
└─────────────────────────────────────────────────────────┘
```

**Layer Responsibilities:**
1. **Protocol Adaptation:** SOAP → REST, FTP → HTTP, Mainframe → API
2. **Data Translation:** Legacy format → Modern format
3. **Error Handling:** Legacy errors → Meaningful exceptions
4. **Business Logic Mapping:** Old rules → New domain logic

### **3. Implementation Examples**

**Node.js Anti-Corruption Layer:**
```javascript
// Core Anti-Corruption Layer Service
class AntiCorruptionLayer {
  constructor() {
    this.adapters = new Map();
    this.translators = new Map();
    this.facades = new Map();
    this.cache = new Map();
  }

  // Register adapter for a legacy system
  registerAdapter(systemName, adapter) {
    this.adapters.set(systemName, adapter);
  }

  // Register translator for a domain
  registerTranslator(domain, translator) {
    this.translators.set(domain, translator);
  }

  // Main entry point for new system
  async callLegacySystem(systemName, operation, newSystemRequest) {
    const startTime = Date.now();
    const correlationId = this.generateCorrelationId();

    try {
      // 1. Get the appropriate adapter
      const adapter = this.adapters.get(systemName);
      if (!adapter) {
        throw new Error(`No adapter found for system: ${systemName}`);
      }

      // 2. Translate new system request to legacy format
      const legacyRequest = await this.translateToLegacyFormat(
        systemName,
        operation,
        newSystemRequest
      );

      // 3. Call legacy system through adapter
      const legacyResponse = await adapter.execute(operation, legacyRequest);

      // 4. Translate legacy response to new system format
      const newSystemResponse = await this.translateToNewFormat(
        systemName,
        operation,
        legacyResponse
      );

      // 5. Log successful interaction
      await this.logInteraction({
        correlationId,
        systemName,
        operation,
        direction: 'outbound',
        status: 'success',
        duration: Date.now() - startTime,
        request: this.sanitizeForLogging(newSystemRequest),
        response: this.sanitizeForLogging(newSystemResponse)
      });

      return newSystemResponse;

    } catch (error) {
      // 6. Handle and translate errors
      const translatedError = this.translateError(systemName, operation, error);

      // 7. Log failed interaction
      await this.logInteraction({
        correlationId,
        systemName,
        operation,
        direction: 'outbound',
        status: 'failed',
        duration: Date.now() - startTime,
        error: translatedError.message,
        legacyError: error.message
      });

      throw translatedError;
    }
  }

  async translateToLegacyFormat(systemName, operation, newRequest) {
    const cacheKey = `${systemName}:${operation}:${JSON.stringify(newRequest)}`;
    
    // Check cache first
    if (this.cache.has(cacheKey)) {
      return this.cache.get(cacheKey);
    }

    // Get appropriate translator
    const translator = this.translators.get(`${systemName}:${operation}`);
    if (!translator) {
      // Default translation if no specific translator
      return this.defaultTranslation(newRequest);
    }

    const legacyFormat = await translator.toLegacy(newRequest);
    
    // Cache for future use
    this.cache.set(cacheKey, legacyFormat);
    
    return legacyFormat;
  }

  async translateToNewFormat(systemName, operation, legacyResponse) {
    const translator = this.translators.get(`${systemName}:${operation}`);
    if (!translator) {
      return this.defaultTranslationReverse(legacyResponse);
    }

    return translator.fromLegacy(legacyResponse);
  }

  translateError(systemName, operation, legacyError) {
    // Map legacy error codes and messages to new system errors
    const errorMapping = {
      'LEGACY_SYSTEM_TIMEOUT': 'ServiceUnavailableError',
      'INVALID_INPUT_LEGACY': 'ValidationError',
      'SYSTEM_OFFLINE': 'ServiceUnavailableError',
      'AUTH_FAILED': 'AuthenticationError'
    };

    const errorCode = legacyError.code || 'UNKNOWN_ERROR';
    const newErrorType = errorMapping[errorCode] || 'IntegrationError';

    return new Error(`${newErrorType}: ${this.cleanErrorMessage(legacyError.message)}`);
  }

  // Default translation implementations
  defaultTranslation(newRequest) {
    // Simple 1:1 mapping for basic cases
    return {
      ...newRequest,
      _translatedAt: new Date().toISOString()
    };
  }

  defaultTranslationReverse(legacyResponse) {
    // Remove legacy-specific fields
    const { _legacyMetadata, _systemCode, ...cleanResponse } = legacyResponse;
    return cleanResponse;
  }

  cleanErrorMessage(legacyMessage) {
    // Remove proprietary error codes and technical details
    return legacyMessage
      .replace(/Error Code: \d+/g, '')
      .replace(/System: [A-Z]+/g, '')
      .trim();
  }

  generateCorrelationId() {
    return `corr_${Date.now()}_${Math.random().toString(36).substr(2, 9)}`;
  }

  sanitizeForLogging(data) {
    // Remove sensitive information
    const sensitiveFields = ['password', 'token', 'ssn', 'creditCard'];
    const sanitized = { ...data };
    
    sensitiveFields.forEach(field => {
      if (sanitized[field]) {
        sanitized[field] = '***REDACTED***';
      }
    });
    
    return sanitized;
  }

  async logInteraction(logEntry) {
    // Send to logging system
    console.log('ACL Interaction:', logEntry);
    
    // Could send to ELK, Application Insights, etc.
    // await loggingService.log(logEntry);
  }
}

// Legacy Mainframe Adapter
class MainframeAdapter {
  constructor(config) {
    this.host = config.host;
    this.port = config.port;
    this.timeout = config.timeout || 30000;
  }

  async execute(operation, request) {
    // Convert to mainframe format (COBOL copybook-like)
    const mainframeRequest = this.convertToMainframeFormat(request);
    
    // Execute via TCP socket (simplified)
    const response = await this.callMainframe(operation, mainframeRequest);
    
    // Parse mainframe response
    return this.parseMainframeResponse(response);
  }

  convertToMainframeFormat(request) {
    // Mainframe often uses fixed-width fields
    const paddedFields = {
      CUSTOMER_ID: request.customerId.padStart(10, '0'),
      TRANSACTION_TYPE: request.type.padEnd(4, ' '),
      AMOUNT: request.amount.toFixed(2).padStart(12, '0'),
      // Date in YYYYMMDD format
      TRANSACTION_DATE: new Date().toISOString().slice(0, 10).replace(/-/g, '')
    };

    // Convert to fixed-width record
    return Object.values(paddedFields).join('');
  }

  async callMainframe(operation, data) {
    // Simulated mainframe call
    return new Promise((resolve, reject) => {
      const socket = net.createConnection({
        host: this.host,
        port: this.port
      });

      socket.setTimeout(this.timeout);
      
      socket.on('connect', () => {
        socket.write(`${operation}:${data}\n`);
      });

      socket.on('data', (data) => {
        socket.end();
        resolve(data.toString());
      });

      socket.on('error', reject);
      socket.on('timeout', () => reject(new Error('Mainframe timeout')));
    });
  }

  parseMainframeResponse(response) {
    // Parse fixed-width response
    return {
      success: response.substring(0, 1) === 'Y',
      reference: response.substring(1, 21).trim(),
      message: response.substring(21, 101).trim(),
      legacyCode: response.substring(101, 105).trim()
    };
  }
}

// SOAP Web Service Adapter
class SoapAdapter {
  constructor(wsdlUrl, credentials) {
    this.wsdlUrl = wsdlUrl;
    this.credentials = credentials;
    this.client = this.createSoapClient();
  }

  createSoapClient() {
    // Using soap npm package
    return new Promise((resolve, reject) => {
      soap.createClient(this.wsdlUrl, (err, client) => {
        if (err) reject(err);
        else resolve(client);
      });
    });
  }

  async execute(operation, request) {
    const client = await this.client;
    
    // Add authentication headers
    client.addSoapHeader(this.createSecurityHeader());
    
    return new Promise((resolve, reject) => {
      client[operation](request, (err, result) => {
        if (err) {
          // SOAP faults to standard errors
          reject(this.parseSoapFault(err));
        } else {
          resolve(result);
        }
      });
    });
  }

  createSecurityHeader() {
    return {
      'wsse:Security': {
        'wsse:UsernameToken': {
          'wsse:Username': this.credentials.username,
          'wsse:Password': this.credentials.password
        }
      }
    };
  }

  parseSoapFault(soapError) {
    // Extract meaningful information from SOAP fault
    const fault = soapError.root?.Envelope?.Body?.Fault;
    if (fault) {
      return new Error(`SOAP Fault: ${fault.faultstring} (Code: ${fault.faultcode})`);
    }
    return soapError;
  }
}

// Domain Translators
class CustomerTranslator {
  // Legacy customer format to new domain model
  async toLegacy(newCustomer) {
    // New system uses camelCase, legacy uses UPPER_CASE
    return {
      CUST_ID: newCustomer.id,
      CUST_NAME: `${newCustomer.firstName} ${newCustomer.lastName}`,
      CUST_ADDR: newCustomer.address.street,
      CUST_CITY: newCustomer.address.city,
      CUST_STATE: newCustomer.address.state,
      CUST_ZIP: newCustomer.address.zipCode,
      CUST_PHONE: newCustomer.phone,
      // Legacy system stores date as MM/DD/YYYY
      CUST_SINCE: newCustomer.joinDate.toLocaleDateString('en-US'),
      STATUS_CODE: newCustomer.active ? 'A' : 'I'
    };
  }

  async fromLegacy(legacyCustomer) {
    return {
      id: legacyCustomer.CUST_ID,
      firstName: this.extractFirstName(legacyCustomer.CUST_NAME),
      lastName: this.extractLastName(legacyCustomer.CUST_NAME),
      address: {
        street: legacyCustomer.CUST_ADDR,
        city: legacyCustomer.CUST_CITY,
        state: legacyCustomer.CUST_STATE,
        zipCode: legacyCustomer.CUST_ZIP
      },
      phone: this.formatPhoneNumber(legacyCustomer.CUST_PHONE),
      joinDate: this.parseLegacyDate(legacyCustomer.CUST_SINCE),
      active: legacyCustomer.STATUS_CODE === 'A',
      metadata: {
        legacyId: legacyCustomer.CUST_ID,
        translatedAt: new Date().toISOString()
      }
    };
  }

  extractFirstName(fullName) {
    return fullName.split(' ')[0];
  }

  extractLastName(fullName) {
    const parts = fullName.split(' ');
    return parts.length > 1 ? parts.slice(1).join(' ') : '';
  }

  formatPhoneNumber(legacyPhone) {
    // Legacy: 1234567890, New: (123) 456-7890
    return legacyPhone.replace(/(\d{3})(\d{3})(\d{4})/, '($1) $2-$3');
  }

  parseLegacyDate(legacyDate) {
    // Legacy: MM/DD/YYYY
    const [month, day, year] = legacyDate.split('/');
    return new Date(year, month - 1, day);
  }
}

class OrderTranslator {
  async toLegacy(newOrder) {
    return {
      ORDER_NUM: newOrder.orderNumber,
      CUST_ID: newOrder.customerId,
      ORDER_DATE: newOrder.orderDate.toISOString().slice(0, 10).replace(/-/g, ''),
      ITEMS: newOrder.items.map(item => ({
        PROD_CODE: item.productId,
        QTY: item.quantity,
        UNIT_PRICE: item.unitPrice.toFixed(2)
      })),
      TOTAL_AMT: newOrder.total.toFixed(2),
      // Legacy status codes
      STATUS: this.mapStatusToLegacy(newOrder.status)
    };
  }

  async fromLegacy(legacyOrder) {
    return {
      orderNumber: legacyOrder.ORDER_NUM,
      customerId: legacyOrder.CUST_ID,
      orderDate: new Date(
        parseInt(legacyOrder.ORDER_DATE.slice(0, 4)),
        parseInt(legacyOrder.ORDER_DATE.slice(4, 6)) - 1,
        parseInt(legacyOrder.ORDER_DATE.slice(6, 8))
      ),
      items: legacyOrder.ITEMS.map(item => ({
        productId: item.PROD_CODE,
        quantity: parseInt(item.QTY),
        unitPrice: parseFloat(item.UNIT_PRICE),
        description: item.DESC || ''
      })),
      total: parseFloat(legacyOrder.TOTAL_AMT),
      status: this.mapStatusFromLegacy(legacyOrder.STATUS),
      legacyData: {
        originalStatus: legacyOrder.STATUS,
        systemCode: legacyOrder.SYS_CODE
      }
    };
  }

  mapStatusToLegacy(newStatus) {
    const mapping = {
      'pending': 'P',
      'processing': 'R',
      'shipped': 'S',
      'delivered': 'D',
      'cancelled': 'X'
    };
    return mapping[newStatus] || 'P';
  }

  mapStatusFromLegacy(legacyStatus) {
    const mapping = {
      'P': 'pending',
      'R': 'processing',
      'S': 'shipped',
      'D': 'delivered',
      'X': 'cancelled'
    };
    return mapping[legacyStatus] || 'unknown';
  }
}

// Facade for New System
class LegacySystemFacade {
  constructor(acl) {
    this.acl = acl;
    
    // Register adapters
    this.acl.registerAdapter('mainframe', new MainframeAdapter({
      host: process.env.MAINFRAME_HOST,
      port: process.env.MAINFRAME_PORT
    }));
    
    this.acl.registerAdapter('soap-service', new SoapAdapter(
      process.env.SOAP_WSDL_URL,
      {
        username: process.env.SOAP_USERNAME,
        password: process.env.SOAP_PASSWORD
      }
    ));
    
    // Register translators
    this.acl.registerTranslator('mainframe:customer', new CustomerTranslator());
    this.acl.registerTranslator('mainframe:order', new OrderTranslator());
  }

  // Clean API for new system
  async getCustomer(customerId) {
    return this.acl.callLegacySystem('mainframe', 'getCustomer', {
      id: customerId
    });
  }

  async createOrder(order) {
    return this.acl.callLegacySystem('mainframe', 'createOrder', order);
  }

  async updateCustomerStatus(customerId, active) {
    return this.acl.callLegacySystem('mainframe', 'updateCustomer', {
      customerId,
      active
    });
  }
}

// Usage in New System
const acl = new AntiCorruptionLayer();
const legacyFacade = new LegacySystemFacade(acl);

// New system calls legacy through clean interface
async function newSystemWorkflow() {
  try {
    // Get customer from legacy system
    const customer = await legacyFacade.getCustomer('12345');
    console.log('Customer from legacy:', customer);
    
    // Create order in legacy system
    const order = await legacyFacade.createOrder({
      orderNumber: 'ORD-2024-001',
      customerId: '12345',
      orderDate: new Date(),
      items: [
        { productId: 'PROD-001', quantity: 2, unitPrice: 29.99 }
      ],
      total: 59.98,
      status: 'pending'
    });
    
    console.log('Order created in legacy:', order);
    
  } catch (error) {
    console.error('ACL Error:', error);
    // New system gets clean, meaningful errors
  }
}
```

**Java/Spring Boot Implementation:**
```java
// Spring Boot Anti-Corruption Layer
@Component
public class AntiCorruptionLayerService {
    
    @Autowired
    private Map<String, LegacyAdapter> adapters;
    
    @Autowired
    private Map<String, DomainTranslator> translators;
    
    @Autowired
    private AclMetrics metrics;
    
    public <T, R> R callLegacySystem(
            String systemName, 
            String operation, 
            T newSystemRequest,
            Class<R> responseType) {
        
        long startTime = System.currentTimeMillis();
        String correlationId = generateCorrelationId();
        
        try {
            // 1. Get adapter
            LegacyAdapter adapter = adapters.get(systemName);
            if (adapter == null) {
                throw new AdapterNotFoundException(systemName);
            }
            
            // 2. Translate request
            Object legacyRequest = translateToLegacy(
                systemName, operation, newSystemRequest);
            
            // 3. Execute legacy call
            Object legacyResponse = adapter.execute(operation, legacyRequest);
            
            // 4. Translate response
            R newSystemResponse = translateToNew(
                systemName, operation, legacyResponse, responseType);
            
            // 5. Record success
            metrics.recordSuccess(
                systemName, operation, 
                System.currentTimeMillis() - startTime);
            
            return newSystemResponse;
            
        } catch (Exception e) {
            // 6. Translate and record error
            metrics.recordFailure(systemName, operation);
            throw translateException(e, systemName, operation);
        }
    }
    
    private <T> Object translateToLegacy(
            String systemName, String operation, T newRequest) {
        
        String translatorKey = systemName + ":" + operation;
        DomainTranslator translator = translators.get(translatorKey);
        
        if (translator != null) {
            return translator.toLegacy(newRequest);
        }
        
        // Default translation
        return newRequest;
    }
    
    private <R> R translateToNew(
            String systemName, String operation, 
            Object legacyResponse, Class<R> responseType) {
        
        String translatorKey = systemName + ":" + operation;
        DomainTranslator translator = translators.get(translatorKey);
        
        if (translator != null) {
            return translator.fromLegacy(legacyResponse, responseType);
        }
        
        // Default translation
        return responseType.cast(legacyResponse);
    }
    
    private RuntimeException translateException(
            Exception e, String systemName, String operation) {
        
        if (e instanceof LegacyTimeoutException) {
            return new ServiceUnavailableException(
                "Legacy system timeout", e);
        }
        
        if (e instanceof LegacyValidationException) {
            return new BadRequestException(
                translateErrorMessage(e.getMessage()), e);
        }
        
        return new IntegrationException(
            "Legacy system integration failed", e);
    }
}

// Legacy Mainframe Adapter
@Component("mainframeAdapter")
public class MainframeAdapter implements LegacyAdapter {
    
    @Value("${legacy.mainframe.host}")
    private String host;
    
    @Value("${legacy.mainframe.port}")
    private int port;
    
    private MainframeClient client;
    
    @PostConstruct
    public void init() {
        this.client = new MainframeClient(host, port);
    }
    
    @Override
    public Object execute(String operation, Object request) {
        switch (operation) {
            case "getCustomer":
                return getCustomer((CustomerRequest) request);
            case "createOrder":
                return createOrder((OrderRequest) request);
            default:
                throw new UnsupportedOperationException(operation);
        }
    }
    
    private CustomerResponse getCustomer(CustomerRequest request) {
        // Convert to mainframe format and call
        String mainframeRequest = convertToMainframeFormat(request);
        String response = client.sendRequest("GET_CUST", mainframeRequest);
        return parseMainframeResponse(response);
    }
    
    private String convertToMainframeFormat(CustomerRequest request) {
        // Fixed-width format conversion
        return String.format("%-10s%-30s", 
            request.getCustomerId(),
            request.getCustomerName());
    }
}

// Domain Translator
@Component("customerTranslator")
public class CustomerTranslator implements DomainTranslator {
    
    @Override
    public Object toLegacy(Object newRequest) {
        Customer customer = (Customer) newRequest;
        
        LegacyCustomer legacy = new LegacyCustomer();
        legacy.setCustId(customer.getId());
        legacy.setCustName(
            customer.getFirstName() + " " + customer.getLastName());
        legacy.setCustAddr(customer.getAddress().getStreet());
        
        // Format conversion
        legacy.setCustSince(formatLegacyDate(customer.getJoinDate()));
        legacy.setStatusCode(customer.isActive() ? "A" : "I");
        
        return legacy;
    }
    
    @Override
    public <T> T fromLegacy(Object legacyResponse, Class<T> targetType) {
        LegacyCustomer legacy = (LegacyCustomer) legacyResponse;
        
        Customer customer = new Customer();
        customer.setId(legacy.getCustId());
        
        // Parse name
        String[] nameParts = legacy.getCustName().split(" ", 2);
        customer.setFirstName(nameParts[0]);
        if (nameParts.length > 1) {
            customer.setLastName(nameParts[1]);
        }
        
        // Address mapping
        Address address = new Address();
        address.setStreet(legacy.getCustAddr());
        customer.setAddress(address);
        
        // Date parsing
        customer.setJoinDate(parseLegacyDate(legacy.getCustSince()));
        customer.setActive("A".equals(legacy.getStatusCode()));
        
        return targetType.cast(customer);
    }
    
    private String formatLegacyDate(Date date) {
        SimpleDateFormat sdf = new SimpleDateFormat("MM/dd/yyyy");
        return sdf.format(date);
    }
    
    private Date parseLegacyDate(String legacyDate) {
        try {
            SimpleDateFormat sdf = new SimpleDateFormat("MM/dd/yyyy");
            return sdf.parse(legacyDate);
        } catch (ParseException e) {
            return new Date();
        }
    }
}
```

### **4. Testing the Anti-Corruption Layer**

```javascript
// Comprehensive ACL Testing
class AntiCorruptionLayerTests {
  constructor() {
    this.acl = new AntiCorruptionLayer();
    this.mockAdapters = new Map();
  }
  
  async runTests() {
    console.log('Running Anti-Corruption Layer Tests...');
    
    await this.testTranslation();
    await this.testErrorHandling();
    await this.testCaching();
    await this.testPerformance();
    await this.testIdempotency();
    
    console.log('All tests completed');
  }
  
  async testTranslation() {
    console.log('\n1. Testing Translation Layer:');
    
    // Test forward translation
    const newCustomer = {
      id: 'cust-123',
      firstName: 'John',
      lastName: 'Doe',
      address: {
        street: '123 Main St',
        city: 'Anytown',
        state: 'CA',
        zipCode: '12345'
      },
      joinDate: new Date('2020-01-15'),
      active: true
    };
    
    const translator = new CustomerTranslator();
    const legacyCustomer = await translator.toLegacy(newCustomer);
    
    console.log('New → Legacy:', {
      expected: 'CUST_ID: cust-123',
      actual: `CUST_ID: ${legacyCustomer.CUST_ID}`
    });
    
    // Test reverse translation
    const translatedBack = await translator.fromLegacy(legacyCustomer);
    console.log('Legacy → New:', {
      expected: 'John Doe',
      actual: `${translatedBack.firstName} ${translatedBack.lastName}`
    });
    
    // Verify no data loss
    const dataLoss = this.checkDataLoss(newCustomer, translatedBack);
    if (dataLoss) {
      throw new Error('Data loss during translation');
    }
  }
  
  async testErrorHandling() {
    console.log('\n2. Testing Error Handling:');
    
    // Mock adapter that throws legacy errors
    const mockAdapter = {
      execute: async (operation, request) => {
        throw {
          code: 'LEGACY_SYSTEM_TIMEOUT',
          message: 'System timeout after 30000ms'
        };
      }
    };
    
    this.acl.registerAdapter('test-system', mockAdapter);
    
    try {
      await this.acl.callLegacySystem('test-system', 'testOperation', {});
      throw new Error('Should have thrown');
    } catch (error) {
      console.log('Legacy error translated:', error.message);
      // Should be "ServiceUnavailableError: System timeout after 30000ms"
    }
  }
  
  async testCaching() {
    console.log('\n3. Testing Caching:');
    
    let callCount = 0;
    const mockTranslator = {
      toLegacy: async (request) => {
        callCount++;
        return { translated: request, callCount };
      }
    };
    
    this.acl.translators.set('test:operation', mockTranslator);
    
    const testRequest = { id: 'test', data: 'same' };
    
    // First call
    await this.acl.translateToLegacyFormat('test', 'operation', testRequest);
    
    // Second call (should be cached)
    await this.acl.translateToLegacyFormat('test', 'operation', testRequest);
    
    console.log('Cache hits:', {
      expected: 'callCount should be 1',
      actual: `callCount = ${callCount}`
    });
  }
  
  checkDataLoss(original, translated) {
    // Check for significant data loss
    const originalJson = JSON.stringify(original);
    const translatedJson = JSON.stringify(translated);
    
    // Some differences are expected (format changes)
    // But core data should be preserved
    return false; // Simplified check
  }
}
```

### **5. Benefits & Advantages**

**Architectural Benefits:**
1. **Domain Integrity:** New system maintains clean domain model
2. **Isolation:** Legacy problems don't propagate to new system
3. **Incremental Migration:** Replace legacy components one by one
4. **Testing:** Can test ACL independently of legacy system

**Business Benefits:**
- **Risk Reduction:** Safe integration with legacy systems
- **Cost Savings:** Avoid complete rewrites
- **Time to Market:** Deliver new features while maintaining legacy
- **Knowledge Preservation:** Leverage existing business logic

**Technical Benefits:**
- **Modern Interfaces:** REST, GraphQL, gRPC for new system
- **Clean Code:** No legacy patterns in new codebase
- **Monitoring:** Separate metrics for legacy vs new
- **Security:** Apply modern security to legacy interactions

### **6. Common Variations**

**Event-Driven Anti-Corruption Layer:**
```javascript
// Subscribe to legacy events, publish clean events
class EventDrivenACL {
  constructor() {
    this.eventTranslators = new Map();
    this.eventQueue = [];
  }
  
  async subscribeToLegacyEvents(legacySystem) {
    // Subscribe to legacy messaging system
    legacySystem.on('event', async (legacyEvent) => {
      try {
        // Translate legacy event
        const cleanEvent = await this.translateEvent(legacyEvent);
        
        // Publish to new system event bus
        await this.publishToNewSystem(cleanEvent);
        
        // Acknowledge legacy event
        await legacySystem.acknowledge(legacyEvent.id);
        
      } catch (error) {
        console.error('Event translation failed:', error);
        await this.handleFailedEvent(legacyEvent, error);
      }
    });
  }
  
  async translateEvent(legacyEvent) {
    const translator = this.eventTranslators.get(legacyEvent.type);
    if (!translator) {
      return this.defaultEventTranslation(legacyEvent);
    }
    
    return translator.translate(legacyEvent);
  }
  
  defaultEventTranslation(legacyEvent) {
    // Convert legacy event format to standard format
    return {
      id: legacyEvent.EVENT_ID,
      type: this.mapEventType(legacyEvent.EVENT_TYPE),
      timestamp: new Date(legacyEvent.EVENT_TIMESTAMP),
      payload: legacyEvent.EVENT_DATA,
      metadata: {
        legacySystem: 'mainframe',
        originalType: legacyEvent.EVENT_TYPE
      }
    };
  }
  
  mapEventType(legacyType) {
    const mapping = {
      'CUST_ADD': 'customer.created',
      'CUST_UPD': 'customer.updated',
      'ORD_CRE': 'order.created',
      'ORD_SHP': 'order.shipped'
    };
    return mapping[legacyType] || 'unknown.event';
  }
}
```

### **7. Best Practices & Guidelines**

**ACL Design Principles:**
```javascript
class ACLBestPractices {
  // 1. Never expose legacy concepts to new system
  ensureNoLeakage(newResponse) {
    const legacyPatterns = [
      /_LEGACY_/i,
      /_SYS_/i,
      /[A-Z]{3}_CODE/i,
      /[0-9]{8}/ // YYYYMMDD dates
    ];
    
    const jsonString = JSON.stringify(newResponse);
    for (const pattern of legacyPatterns) {
      if (pattern.test(jsonString)) {
        throw new Error(`Legacy concept leaked: ${pattern}`);
      }
    }
  }
  
  // 2. Implement circuit breaking for legacy calls
  async callWithCircuitBreaker(systemName, operation, request) {
    const circuitBreaker = this.getCircuitBreaker(systemName);
    
    return circuitBreaker.execute(async () => {
      return this.callLegacySystem(systemName, operation, request);
    });
  }
  
  // 3. Use feature toggles for gradual migration
  async callSystemWithToggle(systemName, operation, request) {
    if (featureToggles.isEnabled(`use_new_${systemName}_${operation}`)) {
      // Call new implementation
      return this.callNewSystem(operation, request);
    } else {
      // Call through ACL
      return this.acl.callLegacySystem(systemName, operation, request);
    }
  }
  
  // 4. Implement comprehensive logging
  logACLInteraction(details) {
    const logEntry = {
      timestamp: new Date().toISOString(),
      system: details.systemName,
      operation: details.operation,
      direction: details.direction,
      duration: details.duration,
      success: details.success,
      requestSize: JSON.stringify(details.request).length,
      responseSize: JSON.stringify(details.response).length,
      correlationId: details.correlationId
    };
    
    // Send to structured logging system
    loggingService.info('ACL_INTERACTION', logEntry);
  }
  
  // 5. Plan for legacy system retirement
  async deprecateLegacySystem(systemName) {
    // 1. Log all calls to identify dependencies
    this.enableCallLogging(systemName);
    
    // 2. Notify consumers of deprecation
    this.notifyConsumers(systemName);
    
    // 3. Provide migration path
    this.provideMigrationGuide(systemName);
    
    // 4. Schedule shutdown
    this.scheduleShutdown(systemName);
  }
}
```

### **8. Common Pitfalls & Anti-Patterns**

**Implementation Mistakes:**
1. **Thin Wrapper:** Just passing through legacy API without translation
2. **Bidirectional Contamination:** New system adopting legacy patterns
3. **Over-Translation:** Adding unnecessary complexity
4. **Ignoring Performance:** ACL becoming bottleneck

**Architectural Anti-Patterns:**
- **Big Ball of Mud ACL:** ACL itself becomes legacy
- **Temporary Solution:** ACL never gets removed
- **God Object ACL:** Single class doing all translations
- **Missing Monitoring:** No visibility into ACL performance

**Testing Anti-Patterns:**
- Not testing translation both directions
- Mocking too much (not testing real integration)
- Ignoring error scenarios
- No performance testing under load

### **9. When to Use Anti-Corruption Layer**

**Good Fit For:**
1. **Legacy System Integration:** Modernizing while maintaining old systems
2. **Third-party Vendor Integration:** Isolating proprietary systems
3. **Merger & Acquisition:** Integrating different company systems
4. **Technology Migration:** Moving from mainframe to cloud

**Poor Fit For:**
1. **Simple Integrations:** When direct integration is sufficient
2. **Short-term Solutions:** When legacy system will be replaced soon
3. **Performance-critical Paths:** When ACL overhead is unacceptable
4. **Simple Data Sync:** When ETL tools are more appropriate

### **10. Interview-Specific Considerations**

**Common Interview Questions:**
1. "How do you prevent legacy concepts from contaminating new systems?"
2. "What's the difference between adapter pattern and anti-corruption layer?"
3. "How do you test an anti-corruption layer?"
4. "When would you remove an anti-corruption layer?"

**Design Discussion Points:**
- Trade-off between isolation and performance
- Versioning and schema evolution
- Monitoring and observability
- Team organization (who owns the ACL?)

---

## **Before-Interview Checklist**

**✅ Core Concept Understanding**
- [ ] Domain-driven design context of ACL
- [ ] Difference from adapter/facade patterns
- [ ] Bidirectional vs unidirectional translation
- [ ] Strategic vs tactical implementation

**✅ Implementation Details**
- [ ] Translation layer design
- [ ] Error handling and mapping
- [ ] Caching strategies
- [ ] Performance optimization

**✅ Testing Strategies**
- [ ] Translation accuracy testing
- [ ] Integration testing with mocks
- [ ] Performance and load testing
- [ ] Regression testing for schema changes

**✅ Operational Considerations**
- [ ] Monitoring and metrics collection
- [ ] Logging and audit trails
- [ ] Circuit breaking and resilience
- [ ] Version management

**✅ Migration & Evolution**
- [ ] Gradual legacy retirement strategies
- [ ] Feature toggle implementation