
**Source:** Microsoft Azure Architecture Center | [CI/CD for Microservices](https://learn.microsoft.com/en-us/azure/architecture/microservices/ci-cd)

**Main Idea:** Implementing effective CI/CD for microservices requires a **multi-repository, pipeline-per-service approach** with standardized templates and patterns to enable independent deployment while maintaining governance and security across potentially hundreds of services.

---

#### **Notes: Core Patterns & Azure Implementation**

**1. CI/CD Pipeline Structure for Microservices**
*   **Pipeline per Service:** Each microservice has its own **independent CI/CD pipeline**, enabling teams to build, test, and deploy autonomously without affecting other services.
*   **Multi-Repository Strategy:** Each service lives in its own source code repository (e.g., separate Git repos in Azure Repos). This is essential for independent versioning and deployment.
*   **Standardized Pipeline Templates:** To avoid duplication and ensure compliance, use **YAML templates** (in Azure Pipelines) or **task groups** to define standardized build and release stages that individual service pipelines inherit.

**2. Key Stages of the Pipeline**
*   **Continuous Integration (CI) Stage:**
    *   **Trigger:** Code push to the service's repository.
    *   **Activities:** Build container image, run **unit tests**, **static code analysis**, **dependency scanning**, and **container vulnerability scanning**.
    *   **Output:** A versioned container image pushed to a registry (**Azure Container Registry**).
*   **Continuous Delivery (CD) Stage:**
    *   **Trigger:** Successful CI build, often gated.
    *   **Activities:** Deploy to environments (dev → staging → production) using **infrastructure as code**.
    *   **Key Practice:** **Deploy the same immutable container image** across all environments; only configuration changes.

**3. Critical Testing Strategy for Microservices**
*   **Testing Pyramid Applied:**
    *   **Unit Tests:** Extensive coverage within each service.
    *   **Integration Tests:** Test service with its **external dependencies mocked** (e.g., databases, other services).
    *   **Contract Tests:** **Most important for microservices.** Verify API compatibility between consumer and provider services using tools like **Pact**.
    *   **End-to-End Tests:** Minimal; focus on critical user journeys. Avoid broad E2E tests as they are brittle and slow.
*   **Shift-Left Security:** Integrate security scans (SAST, SCA, container scanning) directly into the CI stage to fail fast on vulnerabilities.

**4. Deployment Strategies & Environment Management**
*   **Orchestrated Deployment:** Use **Kubernetes manifests** (deployments, services) or **Helm charts** managed via the CD pipeline to deploy to **Azure Kubernetes Service (AKS)**.
*   **Infrastructure as Code (IaC):** Provision the underlying infrastructure (AKS, networking, databases) using **ARM templates**, **Bicep**, or **Terraform**. Keep IaC in separate, centrally managed pipelines for platform components.
*   **Safe Deployment Practices:**
    *   **Blue-Green or Canary Deployments:** Implemented using Azure DevOps release gates or service mesh (Istio) traffic splitting in AKS.
    *   **Feature Flags:** Use services like **Azure App Configuration** to toggle features without redeploying.

**5. Observability & Governance in the Pipeline**
*   **Pipeline Telemetry:** Instrument pipelines to track deployment frequency, lead time, and failure rates.
*   **Mandatory Quality Gates:** Enforce policies in the release pipeline:
    *   **Security Scan Pass**
    *   **Vulnerability Thresholds**
    *   **Performance/Load Test Results**
    *   **Approvals for Production**
*   **Configuration Management:** Store service configuration (not secrets) in **Azure App Configuration** or as environment-specific config files. Secrets go to **Azure Key Vault**.

**6. Azure DevOps Services & Tools Integration**
*   **Azure Pipelines:** Primary CI/CD orchestration service.
*   **Azure Container Registry:** Private Docker registry for storing service images.
*   **Azure Monitor/Application Insights:** Integrated for deployment validation and post-deployment monitoring.
*   **Azure Policy & Blueprints:** For governance and compliance at the infrastructure level.

---

#### **Summary / Key Takeaways for Interview:**

*   **Independence via Standardization:** The core tension is enabling **team autonomy** (pipeline-per-service) while maintaining **organizational control** (through standardized templates, mandatory gates, and centralized IaC for platform components).
*   **Contract Testing is the Glue:** In a distributed system where services change independently, **contract testing is the primary safety mechanism** that replaces integrated E2E testing and prevents breaking changes.
*   **Everything as Code:** The entire delivery mechanism is codified: **application code, container definitions, infrastructure, pipelines, and policies**. This enables reproducibility and automation at scale.
*   **Pipeline Design Reflects Architecture:** The **multi-repo, multi-pipeline** structure directly mirrors the **loosely coupled, decentralized architecture** of the microservices themselves.
*   **Azure Native Toolchain:** The guide presents a fully integrated Azure-native CI/CD story using Azure DevOps, ACR, AKS, and App Configuration/Key Vault.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"How do you structure CI/CD for 50+ microservices?"** → Advocate for **pipeline-per-service** using **standardized YAML templates** from a central library. This gives teams autonomy while enforcing organizational standards for security, quality, and deployment.
*   **"What's the most important test type for microservices CI/CD?"** → **Contract Testing.** Explain that it ensures API compatibility between independently changing services and is the key to enabling safe, frequent deployments without extensive integration environments.
*   **"How do you manage secrets and configuration for microservices in Azure?"** → **Configuration** in **Azure App Configuration** (feature flags, settings). **Secrets** in **Azure Key Vault**, injected at runtime via managed identities or pipeline variables.
*   **"How do you perform safe deployments to AKS?"** → Describe using **Kubernetes rollout strategies** (blue-green, canary) managed either natively via K8s manifests with pipeline gates or, for advanced traffic splitting, using a **service mesh (Istio)** integrated with the release pipeline.
*   **"How do you prevent a team from deploying a vulnerable container image?"** → Explain **quality gates in Azure Pipelines:** The CI stage must include container vulnerability scanning (e.g., with Trivy integrated) and the CD release stage should have a gate that fails if critical vulnerabilities are found, blocking the deployment.