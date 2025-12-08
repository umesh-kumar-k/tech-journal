## Microservices Service Catalog Interview Checklist

- **Core Purpose**
    
    - Centralized repository of all microservices providing **single source of truth** for service inventory, dependencies, ownership, and metadata.[opslevel](https://www.opslevel.com/resources/why-you-need-a-microservice-catalog)​
        
    - Enables service discovery, reduces duplication, accelerates onboarding, supports governance.
        
- **Key Components**
    
    - **Service Metadata:** Name, version, description, API specs (OpenAPI), endpoints.
        
    - **Ownership:** Team/individual responsible for development, maintenance, SLAs.
        
    - **Dependencies:** Maps service-to-service relationships, data flows.
        
    - **Health & Performance:** SLA status, uptime, latency metrics.
        
    - **Documentation:** Usage guides, changelogs, contact info.
        
- **Business Benefits**
    
    - **Developer Productivity:** Quick service discovery, reduces "reinventing the wheel".
        
    - **Governance:** Enforce standards, track deprecated services, compliance.
        
    - **Platform Engineering:** SRE/DevOps visibility into system complexity.
        
    - **Onboarding:** New team members ramp up faster with complete service map.
        
- **Implementation Best Practices**
    
    - **Consistent IDs:** Unified service identifiers across monitoring, CI/CD, orchestration tools.
        
    - **Automation-First:** Auto-populate via service registration, GitOps, OpenTelemetry.
        
    - **Standardized Metadata:** Service name, owner, tier (gold/silver/bronze), contact.
        
    - **Search & Filtering:** Tags, categories, full-text search capabilities.
        
- **Tools & Frameworks**
    
    |Category|Tools|
    |---|---|
    |**Enterprise**|OpsLevel, Port.io, Backstage|
    |**Open Source**|Cortex, Roadie (Backstage), ServiceTree|
    |**Cloud**|Azure Service Catalog, AWS Service Catalog|
    |**API-Focused**|SwaggerHub + custom catalog|
    
- **Architectural Integration**
    
    - **Service Mesh:** Istio/Linkerd auto-discovers services for catalog population.
        
    - **GitOps:** ArgoCD/Flux syncs deployments → catalog updates.
        
    - **Observability:** Prometheus/Grafana metrics feed health status.
        
    - **CI/CD:** Jenkins/GitHub Actions tags services on deploy.
        

## 60-Second Recap

- **Service Catalog =** Centralized inventory of services + ownership + dependencies + health.
    
- **Value:** Discoverability, governance, onboarding, complexity management.
    
- **Success:** Auto-population, consistent IDs, standardized metadata.
    
- **Tools:** OpsLevel/Port.io (enterprise), Backstage (open source).
    
- **Gold:** Integrate with service mesh + GitOps + observability stack.
    

**Reference**: [What is a Microservice Catalog](https://thenewstack.io/microservices/what-is-a-microservice-catalog-and-why-do-you-need-one/)[opslevel](https://www.opslevel.com/resources/why-you-need-a-microservice-catalog)​

1. [https://www.opslevel.com/resources/why-you-need-a-microservice-catalog](https://www.opslevel.com/resources/why-you-need-a-microservice-catalog)
2. [https://www.cortex.io/post/microservices-catalog-definition-use-cases-benefits](https://www.cortex.io/post/microservices-catalog-definition-use-cases-benefits)
3. [https://www.port.io/blog/microservice-catalog](https://www.port.io/blog/microservice-catalog)
4. [https://x.com/thenewstack/status/1907479642597945349](https://x.com/thenewstack/status/1907479642597945349)
5. [https://www.enov8.com/blog/what-is-a-microservice-catalog/](https://www.enov8.com/blog/what-is-a-microservice-catalog/)
6. [https://www.graphapp.ai/blog/building-an-effective-microservices-catalogue-best-practices-and-tools](https://www.graphapp.ai/blog/building-an-effective-microservices-catalogue-best-practices-and-tools)
7. [https://www.atlassian.com/microservices/microservices-architecture/microservices-tools](https://www.atlassian.com/microservices/microservices-architecture/microservices-tools)
8. [https://www.youtube.com/watch?v=3WqDbU_Xnu4](https://www.youtube.com/watch?v=3WqDbU_Xnu4)
9. [https://www.osohq.com/learn/microservices-best-practices](https://www.osohq.com/learn/microservices-best-practices)
10. [https://swimm.io/learn/microservices/top-36-microservices-tools-for-2025](https://swimm.io/learn/microservices/top-36-microservices-tools-for-2025)
11. [https://dev.to/joeyparsons/what-is-a-microservice-catalog-fh5](https://dev.to/joeyparsons/what-is-a-microservice-catalog-fh5)
12. [https://www.tatvasoft.com/blog/microservices-best-practices/](https://www.tatvasoft.com/blog/microservices-best-practices/)
13. [https://www.opslevel.com/resources/a-complete-guide-to-microservice-orchestration](https://www.opslevel.com/resources/a-complete-guide-to-microservice-orchestration)
14. [https://www.port.io/usecases/microservice-catalog](https://www.port.io/usecases/microservice-catalog)
15. [https://www.bmc.com/blogs/microservices-best-practices/](https://www.bmc.com/blogs/microservices-best-practices/)
16. [https://www.graphapp.ai/blog/top-microservices-catalog-tools-for-efficient-service-management](https://www.graphapp.ai/blog/top-microservices-catalog-tools-for-efficient-service-management)
17. [https://www.opslevel.com/microservice-catalog](https://www.opslevel.com/microservice-catalog)
18. [https://microservices.io/patterns/microservices.html](https://microservices.io/patterns/microservices.html)
19. [https://www.faciletechnolab.com/blog/25-tools-for-building-microservices-with-aspnet-core/](https://www.faciletechnolab.com/blog/25-tools-for-building-microservices-with-aspnet-core/)
20. [https://dev.to/icepanel/microservice-catalogs-and-the-best-tools-for-the-job-2p1](https://dev.to/icepanel/microservice-catalogs-and-the-best-tools-for-the-job-2p1)
### **Cornell Notes: What is a Microservice Catalog and Why Do You Need One?**

**Source:** The New Stack | [https://thenewstack.io/microservices/what-is-a-microservice-catalog-and-why-do-you-need-one/](https://thenewstack.io/microservices/what-is-a-microservice-catalog-and-why-do-you-need-one/)

**Main Idea:** As the number of microservices grows, organizations face discovery, management, and governance chaos. A **Microservice Catalog** (or Service Catalog) is a centralized, searchable metadata repository that acts as a single source of truth for all services, enabling control, collaboration, and efficient operations at scale.

---

#### **Notes: Core Concepts & Rationale**

**1. What is a Microservice Catalog?**
*   **Definition:** A centralized, self-service portal that provides a **living inventory** of all microservices within an organization.
*   **Core Function:** It stores and displays **metadata** about each service, answering key questions for developers, operators, and architects.
*   **Analogy:** It is the **"Google for your microservices"** or an internal "app store" for service consumers.

**2. The "Why": Problems it Solves (The Catalog as a Necessity)**
*   **Discovery Crisis:** "What services already exist?" Developers waste time rebuilding functionality that already exists because they can't find it.
*   **Ownership & On-Call Chaos:** "Who owns this service? Who do I contact when it's broken?" Lack of clear ownership slows incident response.
*   **Inconsistent Standards:** "How do I expose metrics? What's our logging standard?" Without a central reference, each service deviates, hurting observability.
*   **Compliance & Security Risk:** "Which services handle PII? Are they all patched?" Unable to track critical attributes across the estate.
*   **Dependency Mapping Blindness:** "If I change this API, who will it break?" Unknown service-to-service dependencies make changes risky.

**3. Key Metadata a Catalog Should Track**
*   **Basic Identity:** Service name, unique ID, description.
*   **Ownership:** Team name, product owner, Slack channel, repository link.
*   **Operational:** Health status, deployment environment (dev/stage/prod), SLA/SLOs, on-call runbook link.
*   **Technical:** API documentation (OpenAPI/Swagger), communication protocols, data schemas, tech stack.
*   **Business & Compliance:** Business capability, data classification (PII, PCI), regulatory requirements.

**4. Core Benefits & Use Cases**
*   **For Developers (Consumers):** Self-service discovery of existing APIs to reuse, not rebuild. Easier integration.
*   **For Service Owners:** Showcases their service, documents APIs, and establishes clear contact points.
*   **For Platform/SRE Teams:** Enforces standards (e.g., requiring a `/health` endpoint). Provides a dashboard for governance, security audits, and lifecycle management (track deprecated services).
*   **For Incident Response:** Immediately identifies the owning team and points to runbooks during an outage.
*   **For Architectural Governance:** Visualizes service dependencies to assess impact and manage technical debt.

**5. Implementation & Tooling**
*   **Key Principle:** **Automate population.** The catalog should be populated via CI/CD pipelines and service registries—**not** manual entry, which becomes stale.
*   **How to Populate:**
    *   **CI/CD Integration:** Push metadata (owner, repo, descriptions) on each deployment.
    *   **Service Mesh Integration:** Automatically pull runtime data (health, dependencies) from a service mesh (Istio, Linkerd) or service discovery.
    *   **API Specs:** Automatically ingest OpenAPI specs from source code.
*   **Example Tools:**
    *   **Backstage** (Open Source, CNCF): The leading, full-featured platform for building developer portals with integrated service catalogs.
    *   **ServiceNow CMDB:** Often used in large enterprises for broader IT asset management.
    *   **Custom-Built:** Using a wiki (Confluence) or internal website, though these often lack automation and become outdated.

---

#### **Summary / Key Takeaways for Interview:**

*   **Catalogs Enable Scale:** Beyond ~20-30 services, informal knowledge breaks down. A catalog is a **necessary platform tool** for scaling microservices management, not a "nice-to-have."
*   **It's About Metadata, Not Code:** The catalog doesn't store the service binary. It stores the **context** about the service—the who, what, how, and why—that is otherwise lost in tribal knowledge.
*   **Automation is Non-Negotiable:** A manually updated catalog is a **liability** (it's wrong). Its value hinges on **automated, pipeline-driven updates** to ensure accuracy.
*   **Multi-Audience Tool:** It serves critical, distinct needs for **developers** (discovery), **operators** (incident response), and **management** (governance, compliance).

#### **Potential Interview Questions & How to Use These Notes:**

*   **"At what point does an organization need a service catalog?"** → Answer: When they experience **"discovery pain"**—developers duplicating work, incidents with unclear ownership, or a loss of architectural oversight due to service sprawl (often around 20-50 services).
*   **"What's the difference between a Service Registry and a Service Catalog?"** → **Registry** (e.g., Eureka, Consul) is a **runtime** tool for service *discovery* (finding IPs/ports of *running* instances). **Catalog** (e.g., Backstage) is a **design-time/operational** tool for service *management* (documentation, ownership, standards, lifecycle).
*   **"How would you implement a catalog to ensure it stays accurate?"** → Emphasize **automation:** 1) Enforce metadata (owner, SLA) as required fields in CI/CD pipeline, 2) Integrate with Git repos for docs/APIs, 3) Pull runtime health from the service mesh or monitoring system.
*   **"What is Backstage?"** → Describe it as an **open-source developer platform framework** created by Spotify, now a CNCF project. Its core feature is a **Service Catalog** that can be automatically populated via "Entity" YAML files in source code, providing a unified developer portal.
*   **"Who is the primary user of a catalog?"** → **All of engineering.** Developers use it to find APIs, SREs use it for operations and standards, architects use it for governance, and product managers use it to understand capabilities.