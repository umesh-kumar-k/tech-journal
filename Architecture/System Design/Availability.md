### **High Availability System Design Basics**

**Source:** Design Gurus Blog | [High Availability System Design Basics](https://www.designgurus.io/blog/high-availability-system-design-basics)

**Main Idea:** **High Availability (HA)** is the ability of a system to remain **operational and accessible** for a high percentage of time, typically measured as **uptime percentage (e.g., 99.99%)**. Achieving HA requires designing for **fault tolerance, redundancy, and rapid recovery** through distributed architectures, load balancing, and automated failover mechanisms.

---

#### **Notes: Core Concepts & Measurement**

**1. What is High Availability?**
*   **Definition:** A system characteristic that ensures **minimal downtime** and **continuous service** despite failures.
*   **Key Metric:** **Uptime Percentage** (e.g., 99.9% = "three nines").
*   **Availability Levels:**
    *   99% (Two Nines): ~3.65 days downtime/year
    *   99.9% (Three Nines): ~8.76 hours downtime/year
    *   99.99% (Four Nines): ~52.6 minutes downtime/year
    *   99.999% (Five Nines): ~5.26 minutes downtime/year
*   **Service Level Objective (SLO):** The target availability percentage defined in a service contract.

**2. Key Principles of HA Design**
*   **Eliminate Single Points of Failure (SPOF):** No component whose failure would bring down the entire system.
*   **Redundancy:** Duplicate critical components (servers, databases, network paths, data centers).
*   **Fault Tolerance:** System continues operating despite component failures.
*   **Automatic Failover:** Seamless switching to backup systems without manual intervention.
*   **Geographic Distribution:** Deploy across multiple regions/zones to survive regional outages.

**3. Essential HA Patterns & Components**

**A. Load Balancers**
*   **Purpose:** Distribute traffic across multiple servers to prevent overload and enable zero-downtime deployments.
*   **Types:**
    *   **Layer 4 (Transport):** IP/TCP level (faster, less intelligent).
    *   **Layer 7 (Application):** HTTP/HTTPS level (smarter routing, SSL termination).
*   **Health Checks:** Regularly probe servers and remove unhealthy ones from pool.
*   **Algorithms:** Round-robin, least connections, IP hash, weighted.

**B. Redundant Servers & Auto-Scaling**
*   **Multiple Application Instances:** Deploy across **Availability Zones (AZs)** within a region.
*   **Auto-scaling Groups:** Automatically add/remove instances based on load (scale-out vs. scale-in).
*   **Stateless Design:** Critical for easy scaling and failover (store session state externally).

**C. Database Redundancy**
*   **Primary-Replica (Master-Slave):**
    *   **Synchronous replication:** Strong consistency, used for failover (CP).
    *   **Asynchronous replication:** Higher performance, potential data loss (AP).
*   **Multi-Master:** Multiple nodes accept writes (complex conflict resolution).
*   **Database Sharding:** Horizontal partitioning for scalability.

**D. Data Redundancy & Backup**
*   **RAID configurations** at disk level.
*   **Regular backups** to separate storage with point-in-time recovery.
*   **Replication across regions** for disaster recovery.

**E. Caching for Performance & Resilience**
*   **Distributed caches** (Redis Cluster, Memcached) with replication.
*   **Read-through/write-through** patterns.
*   **Cache-aside** pattern with fallback to database.

**4. Monitoring & Observability**
*   **Health Checks:** Application-level endpoints (`/health`, `/ready`) for load balancers.
*   **Comprehensive Monitoring:** Track metrics (CPU, memory, disk, network, application metrics).
*   **Alerting:** Proactive notification of issues before they cause downtime.
*   **Distributed Tracing:** Track requests across services to identify bottlenecks.
*   **Log Aggregation:** Centralized logging for debugging.

**5. Disaster Recovery (DR) Strategies**
*   **Backup & Restore:** Simple but slow recovery (hours/days).
*   **Pilot Light:** Minimal version running in DR region, scale up when needed.
*   **Warm Standby:** Scaled-down but functional version in DR region.
*   **Multi-Region Active-Active:** Full deployment in multiple regions, highest availability.

**6. Common Pitfalls & Best Practices**
*   **Pitfalls:**
    *   Ignoring **cascading failures** (one failure triggers others).
    *   **Testing inadequately** (not testing failover procedures).
    *   **Complex failure modes** in distributed systems.
    *   **Human error** as major cause of downtime.
*   **Best Practices:**
    *   **Design for failure** (assume everything will fail).
    *   **Implement circuit breakers** to prevent cascading failures.
    *   **Use chaos engineering** to test resilience.
    *   **Automate everything** (deployment, failover, recovery).
    *   **Regularly test backup/restore** and failover procedures.

---

#### **Summary / Key Takeaways for Interview:**

*   **HA is About Minimizing Downtime:** Achieved through **redundancy, distribution, and automated recovery**. It's measured in "nines" of uptime percentage.
*   **Eliminate ALL Single Points of Failure:** Every component (servers, databases, load balancers, network, DNS, even people/processes) must have redundancy.
*   **Stateless Design Enables HA:** Stateless applications are **infinitely horizontally scalable** and can failover instantly. State must be externalized (databases, caches).
*   **Automation is Non-Negotiable:** Manual failover is too slow. **Auto-scaling, automatic failover, and self-healing** are essential for high nines.
*   **Testing is Critical:** HA systems must be **tested under failure conditions** (chaos engineering, failover drills). You can't claim HA without testing it.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"How would you design a highly available web application?"** → Structure answer: 1) **Multiple regions/zones**, 2) **Load balancers** with health checks, 3) **Stateless app servers** in auto-scaling groups, 4) **Database with primary-replica failover**, 5) **Distributed caching**, 6) **CDN for static assets**, 7) **Monitoring/alerting**, 8) **Automated disaster recovery**.
*   **"What's the difference between 99.9% and 99.99% availability?"** → **99.9%** allows ~8.76 hours downtime/year. **99.99%** allows ~52.6 minutes/year. The extra "9" requires **more redundancy, better automation, faster detection/recovery**, and often **multi-region** deployment.
*   **"How do you handle database high availability?"** → **Primary-Replica with automatic failover:** 1) Synchronous/asynchronous replication, 2) **Automatic failover** via monitoring + consensus, 3) **VIP/DNS update** to redirect traffic, 4) **Multi-AZ deployment**, 5) **Regular backups + point-in-time recovery**. For extreme HA: **Multi-region active-active** with conflict resolution.
*   **"What are common single points of failure in systems?"** → 1) **Single load balancer** (solution: active-passive pair), 2) **Single database server** (solution: replication), 3) **Single data center/region** (solution: multi-region), 4) **Shared storage** (solution: distributed storage), 5) **DNS provider** (solution: multiple providers), 6) **Deployment scripts/people** (solution: automation).
*   **"How does stateless design enable high availability?"** → Stateless servers can be: 1) **Easily replaced** (any server can handle any request), 2) **Horizontally scaled** infinitely, 3) **Failed over instantly** (load balancer redirects traffic), 4) **Updated without downtime** (rolling deployments). State is moved to **shared services** (databases, caches, object storage) that are themselves made HA.


### **System Availability Fundamentals**

**Source:** Thinh Dang Group | [System Availability](https://thinhdanggroup.github.io/availability/)

**Main Idea:** **System Availability** quantifies how **operational and accessible** a system is over time, measured as a percentage of uptime. It's determined by **Mean Time Between Failures (MTBF)** and **Mean Time To Repair (MTTR)**. High availability requires reducing failures and, critically, minimizing recovery time when failures occur.

---

#### **Notes: Core Concepts & Mathematics**

**1. Availability Definition & Formula**
*   **Definition:** The probability that a system is **operational and accessible** when needed.
*   **Basic Formula:** `Availability = Uptime / (Uptime + Downtime)`
*   **Time-Based Formula:** `Availability = MTBF / (MTBF + MTTR)`
    *   **MTBF (Mean Time Between Failures):** Average time system operates between failures.
    *   **MTTR (Mean Time To Repair):** Average time to restore system after failure.
*   **Interpretation:** Availability improves by **increasing MTBF** (more reliable) or **decreasing MTTR** (faster recovery).

**2. The "Nines" of Availability**
*   **Percentage vs. Downtime Per Year:**
    *   90% (One Nine): ~36.5 days downtime/year
    *   99% (Two Nines): ~3.65 days/year
    *   99.9% (Three Nines): ~8.76 hours/year
    *   99.99% (Four Nines): ~52.6 minutes/year
    *   99.999% (Five Nines): ~5.26 minutes/year
*   **Key Insight:** Each additional "9" becomes **exponentially harder** to achieve, often requiring **geographic redundancy** and **extreme automation**.

**3. Improving Availability: MTBF vs. MTTR**
*   **Increasing MTBF (Prevent Failures):**
    *   Use higher quality, more reliable components.
    *   Implement thorough testing (unit, integration, chaos).
    *   Proactive maintenance and monitoring.
    *   **Limitation:** There's a **physical/economic limit** to component reliability.
*   **Decreasing MTTR (Recover Faster):**
    *   **Faster detection:** Comprehensive monitoring, alerting.
    *   **Faster diagnosis:** Good logging, distributed tracing.
    *   **Faster recovery:** Automated failover, rollback mechanisms, backup/restore.
    *   **Key Insight:** For high availability (e.g., 99.99+%), **focus on MTTR** is often more effective and economical than chasing perfect MTBF.

**4. Modeling Availability in Complex Systems**
*   **Series Systems:** Components depend sequentially (all must work).
    *   `Total Availability = A₁ × A₂ × ... × Aₙ`
    *   **Example:** Web Server (99.9%) × App Server (99.9%) × DB (99.9%) = ~99.7% total.
    *   **Implication:** **Adding components in series reduces overall availability.**
*   **Parallel Systems (Redundancy):** Components are redundant (any one can work).
    *   `Total Availability = 1 - [(1 - A₁) × (1 - A₂) × ... × (1 - Aₙ)]`
    *   **Example:** Two redundant servers at 99% each = 99.99% total.
    *   **Implication:** **Redundancy dramatically improves availability.**

**5. Practical Design Principles**
*   **Eliminate Single Points of Failure (SPOF):** Identify any component whose failure causes system failure.
*   **Design for Redundancy:** Critical components should have backups (N+1, 2N, etc.).
*   **Implement Graceful Degradation:** When failures occur, system provides reduced functionality rather than complete failure.
*   **Automate Recovery:** Manual recovery is too slow for high availability. Use auto-scaling, auto-failover, self-healing.
*   **Monitor Everything:** You cannot manage what you cannot measure. Real-time monitoring is essential.
*   **Plan for Disaster Recovery (DR):** Have a documented, tested plan for catastrophic failures.

**6. Availability vs. Reliability**
*   **Availability:** **Probability** system is operational **at a specific point in time**.
*   **Reliability:** **Probability** system operates **without failure over a time interval**.
*   **Analogy:** A car that starts every morning but breaks down frequently has **high availability** (starts when you need it) but **low reliability** (fails often during operation).

**7. Service Level Agreements (SLAs)**
*   **Contractual commitment** specifying minimum availability percentage.
*   Often include **penalties** (service credits) for violations.
*   **SLO (Service Level Objective):** Internal target (more stringent than SLA).
*   **SLI (Service Level Indicator):** Measured metric (e.g., error rate, latency).

---

#### **Summary / Key Takeaways for Interview:**

*   **Availability = MTBF/(MTBF+MTTR):** The fundamental equation. Improve by making systems fail less (MTBF) or recover faster (MTTR).
*   **Focus on MTTR for High "Nines":** Beyond ~99.9%, improving MTTR (faster recovery) is often more feasible than improving MTBF (perfect reliability).
*   **Series vs. Parallel Systems:** **Series systems multiply** failure probabilities (bad). **Parallel systems (redundancy) multiply** success probabilities (good). Design with redundancy for critical paths.
*   **"Nines" Require Exponential Effort:** Each additional 9 requires significantly more investment in redundancy, automation, and geographic distribution.
*   **Availability != Reliability:** A system can be available at any given moment (starts up) but unreliable (crashes frequently during use). Both are important but measure different things.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"How is system availability calculated?"** → `Availability = MTBF / (MTBF + MTTR)`. Explain terms: MTBF is average time between failures, MTTR is average repair time. Also: `Uptime / Total Time`.
*   **"How would you improve a system from 99% to 99.9% availability?"** → Focus on **reducing MTTR**: 1) Implement **automated monitoring & alerting**, 2) **Automated failover** for critical components, 3) **Better diagnostics** (logging, tracing), 4) **Rollback capabilities** for bad deployments. Also improve MTBF: better testing, redundancy for SPOFs.
*   **"Why is redundancy more effective than perfect components for availability?"** → Use the **parallel systems formula**: Two 99% reliable components in parallel yield 99.99% availability. Perfect components (100%) are impossible/expensive. Redundancy with imperfect components is cheaper and more effective for high availability.
*   **"What's the difference between availability and reliability?"** → **Availability**: Probability system is **up at a random point in time** (e.g., website loads now). **Reliability**: Probability system operates **continuously over a period** without failure (e.g., website stays up for 24h). A system can have high availability (starts when checked) but low reliability (crashes often between checks).
*   **"How do you model availability for a system with a load balancer and two app servers?"** → This is a **series-parallel system**: Load balancer (A_LB) in series with two parallel app servers (A_App). Total Availability = `A_LB × [1 - (1 - A_App)²]`. If LB=99.9% and each app=99%, total = 0.999 × 0.9999 ≈ 99.89%. Show how redundancy (parallel) improves the weak link (app servers).

