
## DNS System Design Interview Checklist

- **DNS Hierarchy (Root → Leaf)**
    
    |Level|Role|Count/Notes|
    |---|---|---|
    |**Root**|TLD referrals|13 logical (1K+ instances globally)|
    |**TLD**|(.com, .io) → Authoritative NS|~1,500 TLDs|
    |**Authoritative**|Organization's final IP records|Per-domain|
    |**Resolver**|Client-side query initiator|ISP/Local/Browser/OS|
    
- **Resource Records (RRs)**
    
    |Type|Purpose|Example|
    |---|---|---|
    |**A**|Hostname → IPv4|`educative.io → 104.18.2.119`|
    |**NS**|Domain → Authoritative NS|`educative.io → dns.educative.io`|
    |**CNAME**|Alias → Canonical|`www → server1.primary`|
    |**MX**|Mail server mapping|`mail → mailserver1.backup`|
    
- **Query Resolution**
    
    |Type|Flow|Load Impact|
    |---|---|---|
    |**Iterative**|Resolver asks Root→TLD→Auth|Preferred (distributes load)|
    |**Recursive**|Resolver does full lookup|Higher resolver load|
    
- **Caching Strategy**
    
    |Layer|TTL Control|Hit Rate Impact|
    |---|---|---|
    |**Browser**|Short|30-50% local hits|
    |**OS**|Medium|Reduces resolver calls|
    |**Resolver/ISP**|Configurable|80%+ global reduction|
    |**Authoritative**|Domain owner|Propagation delay|
    
- **Scalability & Reliability**
    
    |Feature|Benefit|
    |---|---|
    |**Global replication**|1K+ root instances|
    |**UDP protocol**|Low latency (1 RTT)|
    |**Anycast routing**|Nearest server|
    |**Eventual consistency**|High availability|
    
- **DNSREC Tooling**
    
    |Category|Tools|
    |---|---|
    |**Resolvers**|BIND, Unbound, PowerDNS|
    |**Authoritative**|NSD, Knot, Cloudflare 1.1.1.1|
    |**Monitoring**|dnsperf, Zabbix DNS|
    |**CDC/Outbox**|Debezium (for app→DNS updates)|
    

## 60-Second Recap

- **Hierarchy:** Resolver → Root (13) → TLD → Authoritative → IP.
    
- **Records:** A (IP), NS (auth), CNAME (alias), MX (mail).
    
- **Flow:** Iterative queries + multi-layer caching (browser→ISP).
    
- **Scale:** UDP, Anycast, replication, eventual consistency.
    
- **Gold:** Resolver caching + low TTL for critical records + BIND/Unbound.
    

**Reference**: [DNS System Design](https://www.educative.io/courses/system-design-interview-prep-crash-course/domain-name-system-dns)[educative](https://www.educative.io/courses/system-design-interview-prep-crash-course/domain-name-system-dns)​

1. [https://www.educative.io/courses/system-design-interview-prep-crash-course/domain-name-system-dns](https://www.educative.io/courses/system-design-interview-prep-crash-course/domain-name-system-dns)
### **Domain Name System (DNS) - System Design Interview Prep**

**Source:** Educative - System Design Interview Prep Crash Course | [Domain Name System (DNS)](https://www.educative.io/courses/system-design-interview-prep-crash-course/domain-name-system-dns)

**Main Idea:** The **Domain Name System (DNS)** is a **hierarchical, distributed database** that translates human-readable domain names (e.g., `google.com`) into machine-readable IP addresses (e.g., `172.217.164.110`). It's a **critical infrastructure component** of the internet that enables scalability, fault tolerance, and efficient web navigation.

---

#### **Notes: Core Concepts & Architecture**

**1. What Problem Does DNS Solve?**
*   **Name-to-Address Translation:** Humans remember names (`google.com`), but computers use IP addresses (`172.217.164.110`).
*   **Scalability:** Centralized name-to-IP mapping (like a hosts file) doesn't scale to billions of devices.
*   **Administrative Decentralization:** Different organizations manage their own naming.

**2. Key DNS Components**
*   **Domain Names:** Hierarchical structure (right-to-left increasing specificity):
    *   **Root Domain:** `.` (implied)
    *   **Top-Level Domain (TLD):** `.com`, `.org`, `.net`, `.io`, `.country-codes`
    *   **Second-Level Domain:** `google` in `google.com`
    *   **Subdomain:** `www` in `www.google.com`
*   **Name Servers:** Servers that store DNS records.
*   **DNS Resolver:** Client-side software (often provided by ISP) that queries DNS servers.
*   **DNS Records:** Data entries in DNS database:
    *   **A Record:** Maps name to IPv4 address.
    *   **AAAA Record:** Maps name to IPv6 address.
    *   **CNAME Record:** Alias of one name to another.
    *   **MX Record:** Mail exchange (email routing).
    *   **NS Record:** Authoritative name server for domain.
    *   **TXT Record:** Text information (often for verification).
    *   **SOA Record:** Start of Authority (administrative info).

**3. DNS Resolution Process (Iterative Query)**
1.  **User** enters `www.google.com` in browser.
2.  **Local Resolver** checks its cache → If not found, queries **Root DNS Server**.
3.  **Root Server** responds with referral to **.com TLD Server**.
4.  **Resolver** queries **.com TLD Server**.
5.  **TLD Server** responds with referral to **Google's Authoritative Name Server**.
6.  **Resolver** queries **Google's Authoritative Server**.
7.  **Authoritative Server** returns `A record` (IP address) for `www.google.com`.
8.  **Resolver** caches the result and returns IP to browser.

**4. DNS Caching (Critical for Performance)**
*   **Multiple Levels:**
    *   **Browser Cache:** OS-level DNS cache.
    *   **OS Cache:** System-level DNS cache.
    *   **Resolver Cache:** ISP/local resolver cache.
    *   **Authoritative Cache:** TTL-based caching at authoritative servers.
*   **TTL (Time-To-Live):** Each DNS record has TTL (seconds) specifying how long it can be cached. Common values: 300s (5 min) to 86400s (24 hours).

**5. Load Distribution with DNS**
*   **Round-Robin DNS:** Return multiple IP addresses for a domain in rotating order. Simple but has limitations (no health checks, uneven distribution).
*   **GeoDNS/GeoIP Routing:** Return different IPs based on user's geographic location. Used by CDNs (Cloudflare, Akamai) to route to nearest data center.
*   **DNS-based Load Balancing:** Limitations:
    *   No server health awareness.
    *   Caching can bypass load distribution.
    *   Not true load balancing (no real-time metrics).

**6. DNS Security Considerations**
*   **DNS Spoofing/Cache Poisoning:** Attackers inject false DNS records into resolver cache.
*   **DNSSEC (DNS Security Extensions):** Adds cryptographic authentication to DNS responses (prevents spoofing but adds complexity).
*   **DDoS Attacks:** Attack DNS infrastructure (e.g., October 2016 Dyn attack).
*   **DNS Tunneling:** Using DNS queries to exfiltrate data or bypass firewalls.

**7. DNS in System Design Interviews**
*   **Common Questions:** "What happens when you type `google.com` in browser?" (DNS is first step).
*   **Design Considerations:**
    *   **Redundancy:** Multiple authoritative servers.
    *   **Global Distribution:** Anycast routing for root/TLD servers.
    *   **Caching Strategy:** Appropriate TTLs (shorter for dynamic services).
    *   **Failover:** DNS can be part of disaster recovery (update DNS to point to backup site).

**8. Modern DNS Enhancements**
*   **EDNS (Extension Mechanisms for DNS):** Supports larger responses, DNSSEC.
*   **DNS over HTTPS (DoH) / DNS over TLS (DoT):** Encrypt DNS queries for privacy.
*   **Anycast Routing:** Multiple servers share same IP; routers direct to nearest.
*   **Cloud DNS Services:** Amazon Route 53, Google Cloud DNS, Azure DNS.

---

#### **Summary / Key Takeaways for Interview:**

*   **Hierarchical & Distributed:** DNS is **decentralized** by design—different organizations manage different parts of hierarchy (root, TLD, domain). This enables scalability and administrative independence.
*   **Caching is Fundamental:** DNS heavily relies on **caching at multiple levels** (browser, OS, resolver) to reduce latency and load on authoritative servers. TTL controls cache duration.
*   **Resolution is Iterative:** The resolver queries **hierarchically** (root → TLD → authoritative) unless cached. This distributes query load.
*   **More Than Simple Translation:** DNS enables **load distribution** (round-robin, GeoDNS), **email routing** (MX records), and **domain verification** (TXT records).
*   **Critical Infrastructure:** DNS is a **single point of failure** for internet. Attacks on DNS can take down major services. Redundancy and security (DNSSEC) are essential.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"Explain what happens in DNS when you type a URL."** → Walk through the **8-step iterative resolution process**: browser cache → OS cache → resolver → root → TLD → authoritative → response with IP → caching. Mention TTL and hierarchy.
*   **"How does DNS support scalability and high availability?"** → **Scalability:** Through **hierarchy** (distributed management), **caching** (reduces queries), and **multiple root/TLD servers** (13 root server clusters globally). **High Availability:** **Redundant authoritative servers**, **anycast routing**, **caching** provides fault tolerance.
*   **"What are the limitations of DNS for load balancing?"** → 1) **No health checks** (returns IP even if server dead), 2) **Caching bypasses distribution** (clients use cached IP), 3) **Uneven distribution** (simple round-robin), 4) **Slow propagation** (TTL delays changes), 5) **No real metrics** (can't consider server load). Hence, DNS is often **first-level** distribution with more sophisticated LB behind.
*   **"How would you design a DNS-based failover system?"** → 1) **Monitor primary site health**, 2) **Use low TTL** (e.g., 60s) for rapid propagation, 3) **Automated DNS update** (API to DNS provider like Route 53) to switch to backup IPs, 4) **Health checks at backup site**, 5) Consider **multi-region anycast** with failover routing. Note: DNS failover has **propagation delay** due to TTL.
*   **"What is DNSSEC and why is it important?"** → **DNSSEC** adds **cryptographic signatures** to DNS records to verify authenticity and integrity. It prevents **DNS cache poisoning/spoofing** attacks where attackers redirect traffic to malicious sites. Important for security but adds complexity and larger response sizes.

### **How the Domain Name System Works (Grokking System Design)**

**Source:** Educative - Grokking the System Design Interview | [How the Domain Name System Works](https://www.educative.io/courses/grokking-the-system-design-interview/how-the-domain-name-system-works)

**Main Idea:** The Domain Name System (DNS) is the **phonebook of the internet**—a **hierarchical, decentralized naming system** that translates human-friendly domain names into machine-readable IP addresses. Its distributed architecture enables **scalability, fault tolerance, and efficient global operation**.

---

#### **Notes: DNS Architecture & Operation**

**1. DNS Hierarchy (Like an Inverted Tree)**
*   **Root Level (.)** - Managed by **13 root server clusters** (A-M) operated by various organizations (Verisign, NASA, USC-ISI, etc.).
*   **Top-Level Domains (TLDs)** - Two types:
    *   **Generic TLDs (gTLDs):** `.com`, `.org`, `.net`, `.edu`
    *   **Country Code TLDs (ccTLDs):** `.us`, `.uk`, `.in`
*   **Second-Level Domains** - The registered name (e.g., `google` in `google.com`).
*   **Subdomains** - Further subdivisions (e.g., `www`, `mail`, `api`).

**2. Key Components & Their Roles**
*   **DNS Resolver (Recursive Resolver):** Usually provided by ISP. Acts on behalf of client to traverse DNS hierarchy. **Caches responses**.
*   **Root Name Servers:** Know where to find TLD servers. Return **referrals** (not final answers).
*   **TLD Name Servers:** Know authoritative servers for each domain in their TLD.
*   **Authoritative Name Servers:** Final authority for a domain's DNS records. **Owned by domain registrar/host**.
*   **DNS Caching Server:** Optional intermediary that caches DNS responses to reduce load.

**3. DNS Resolution Process (Detailed)**
1.  **User** types `www.example.com`.
2.  **Browser** checks local cache → If not found, queries **OS resolver**.
3.  **OS Resolver** checks its cache → If not found, sends request to **configured DNS resolver** (ISP/Public DNS like 8.8.8.8).
4.  **DNS Resolver** checks its cache → If not found, begins **iterative resolution**:
    *   **Query 1:** Root server → "Where is `.com` TLD?"
    *   **Query 2:** `.com` TLD server → "Where is `example.com` authoritative server?"
    *   **Query 3:** `example.com` authoritative server → Returns **A record** for `www.example.com`.
5.  Resolver **caches** the result (respecting TTL).
6.  Resolver returns IP address to OS → to Browser.

**4. DNS Record Types (Critical for System Design)**
*   **A / AAAA:** IPv4 / IPv6 address mapping.
*   **CNAME:** Alias/canonical name (points one name to another). Example: `www.example.com` → `example.com`.
*   **MX:** Mail exchange - directs email to mail servers.
*   **NS:** Name server - delegates subdomain to different authoritative servers.
*   **TXT:** Text records (SPF, DKIM for email security, domain verification).
*   **SOA:** Start of Authority - administrative info (primary name server, contact, serial number, refresh intervals).

**5. DNS Caching & TTL**
*   **Purpose:** Reduces DNS traffic, improves response time, decreases load on authoritative servers.
*   **TTL (Time To Live):** Specified in seconds in DNS response. Indicates **how long a record can be cached**.
    *   **Low TTL (e.g., 300s):** For services needing quick DNS changes (failover, load balancing updates).
    *   **High TTL (e.g., 86400s):** For stable services (reduces queries).
*   **Caching Hierarchy:** Browser → OS → ISP Resolver → Intermediate caching servers.

**6. Load Balancing with DNS**
*   **Round Robin DNS:** Return multiple IP addresses in rotating order.
    *   **Limitation:** No health checking, uneven distribution due to caching.
*   **GeoDNS:** Return different IPs based on user's geographic location.
*   **Weighted Round Robin:** Assign weights to servers (return some IPs more frequently).
*   **DNS for Failover:** Update DNS records to point to backup site during outages (requires low TTL).

**7. DNS Security & Attacks**
*   **DNS Spoofing/Cache Poisoning:** Injecting false DNS data into resolver cache.
*   **DDoS on DNS:** Overwhelm DNS servers (e.g., 2016 Dyn attack affecting Twitter, GitHub).
*   **DNS Amplification Attack:** Use DNS servers to amplify DDoS traffic.
*   **Protections:**
    *   **DNSSEC:** Cryptographically signs DNS records to ensure authenticity.
    *   **Rate Limiting** on DNS servers.
    *   **Anycast:** Distribute DNS servers globally.

**8. Design Considerations for Interviews**
*   **Scalability:** DNS is inherently scalable due to **hierarchy, caching, and distributed servers**.
*   **Availability:** **13 root server clusters** (actually hundreds of instances via anycast), **multiple authoritative servers** per domain.
*   **Latency:** Caching at multiple levels reduces latency for repeated queries.
*   **Consistency:** **Eventual consistency** due to TTL-based caching. Updates propagate as caches expire.
*   **Management:** Hierarchical delegation allows independent management of each domain level.

---

#### **Summary / Key Takeaways for Interview:**

*   **Hierarchical & Distributed by Design:** DNS avoids centralization through a **tree-like hierarchy** where responsibility is delegated (root → TLD → domain). This enables **global scalability**.
*   **Caching is the Performance Secret:** **Multi-level caching** (browser, OS, resolver) reduces 80-90% of DNS queries reaching root/TLD servers. **TTL controls cache duration**.
*   **Resolution is Iterative & Cached:** The resolver does the "legwork" iteratively querying root→TLD→authoritative, then **caches results** for future use.
*   **More Than Name Resolution:** DNS enables **load distribution, email routing, failover, and geographic routing**. It's a **critical infrastructure layer**.
*   **Eventual Consistency Model:** DNS updates propagate **as caches expire** (TTL-based). This means DNS changes aren't instant worldwide.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"Walk me through DNS resolution step-by-step."** → Use the **6-step process** from browser cache → OS cache → resolver → iterative queries (root → TLD → authoritative) → caching → returning IP. Emphasize **caching at every level**.
*   **"How does DNS scale globally?"** → 1) **Hierarchical structure** distributes management, 2) **Caching** reduces load on authoritative servers, 3) **13 root server clusters** with **anycast** (actually hundreds of instances), 4) **Multiple authoritative servers** per domain, 5) **Distributed TLD servers**.
*   **"How would you use DNS for load balancing and what are the limitations?"** → **Techniques:** Round robin, GeoDNS, weighted round robin. **Limitations:** 1) **No health checks** (DNS returns IP even if server dead), 2) **Caching bypasses distribution**, 3) **Uneven load** (some clients make more requests), 4) **TTL delays** updates. **Solution:** Use DNS as **first-level distributor** with proper load balancers behind.
*   **"What is DNS caching and how does TTL work?"** → **Caching:** Storing DNS responses at various levels (browser, OS, resolver) to avoid repeated queries. **TTL:** Time-To-Live in seconds, specifies **how long a record can be cached**. After TTL expires, cache is invalidated and fresh query made. **Trade-off:** Low TTL = faster updates but more queries; High TTL = fewer queries but slower propagation.
*   **"How does DNS support high availability?"** → 1) **Multiple root servers** (13 clusters, geographically distributed), 2) **Redundant TLD servers**, 3) **Multiple authoritative servers** per domain, 4) **Anycast routing** directs to nearest server instance, 5) **Caching** provides fallback during outages (serves stale cached records if servers unreachable).

### **DNS Load Balancing**

**Source:** Cloudflare Learning | [What is DNS Load Balancing?](https://www.cloudflare.com/learning/performance/what-is-dns-load-balancing/)

**Main Idea:** **DNS load balancing** distributes traffic across multiple servers by returning different IP addresses in response to DNS queries. It's a **simple, first-layer distribution method** that operates at the **DNS resolution level**, but has significant limitations compared to more sophisticated application-layer load balancers.

---

#### **Notes: Core Concepts & Mechanisms**

**1. How DNS Load Balancing Works**
*   **Basic Mechanism:** A DNS server is configured with **multiple IP addresses** for a single domain name (e.g., `www.example.com` points to `192.0.2.1`, `192.0.2.2`, `192.0.2.3`).
*   **Response Patterns:**
    *   **Round Robin:** Returns IP addresses in rotating order (most common).
    *   **Weighted Round Robin:** Returns some IP addresses more frequently based on weights.
    *   **Geographic/GeoDNS:** Returns different IPs based on the requester's geographic location.
*   **Client Behavior:** The client (browser) receives multiple IPs and typically tries the **first one**, falling back to others if the first fails.

**2. Types of DNS Load Balancing**
*   **Standard DNS Load Balancing (Round Robin):**
    *   Simple rotation of IP addresses in DNS responses.
    *   No awareness of server health or current load.
*   **Dynamic/Intelligent DNS Load Balancing:**
    *   Monitors server health/load and adjusts responses accordingly.
    *   Can remove unhealthy servers from rotation.
    *   More sophisticated but still DNS-based.
*   **GeoDNS/GeoIP Load Balancing:**
    *   Returns IPs based on user's geographic location.
    *   Directs users to nearest data center for lower latency.
    *   Commonly used by CDNs (like Cloudflare).

**3. Key Benefits**
*   **Simplicity:** Easy to implement at DNS level.
*   **Cost-Effective:** No specialized hardware/software load balancer needed.
*   **Distributed Architecture:** No single point of failure (if DNS infrastructure is redundant).
*   **Global Distribution:** Can direct traffic to servers in different geographic regions.
*   **First Layer of Distribution:** Can work in conjunction with other load balancers.

**4. Critical Limitations & Drawbacks**
*   **No Health Checking:** DNS returns IPs even if servers are down (unless using dynamic DNS with health checks).
*   **Caching Defeats Distribution:** DNS responses are **cached** by resolvers, browsers, and OS. A client may use the same IP for **TTL duration** (minutes to hours), bypassing distribution.
*   **Uneven Load Distribution:**
    *   Some clients make more requests than others.
    *   Round robin at DNS level doesn't equalize actual request volume.
*   **No Session Persistence:** Can't maintain sticky sessions (a user's subsequent requests may go to different servers).
*   **Limited Intelligence:** Cannot consider:
    *   Current server load (CPU, memory).
    *   Application-layer metrics.
    *   Real-time traffic conditions.
*   **Slow Failover:** Changes propagate slowly due to TTL caching.

**5. DNS Load Balancing vs. Application Load Balancing**
| **Aspect** | **DNS Load Balancing** | **Application Load Balancing** |
|------------|------------------------|-------------------------------|
| **Layer** | DNS/Transport (L3-4) | Application (L7) |
| **Health Checks** | Limited or none | Advanced (HTTP status, custom endpoints) |
| **Traffic Distribution** | Simple round robin | Advanced algorithms (least connections, weighted) |
| **Session Persistence** | No | Yes (cookies, IP hash) |
| **SSL Termination** | No | Yes |
| **Real-time Adjustments** | Slow (TTL-dependent) | Immediate |
| **Caching Impact** | Major (bypasses distribution) | Minimal |

**6. Best Practices & Use Cases**
*   **Use DNS Load Balancing For:**
    *   **Geographic distribution** (GeoDNS).
    *   **First-level traffic routing** to different data centers.
    *   **Simple failover** between sites (with low TTL).
    *   **Distributing static content** across multiple servers.
*   **Combine with Other Solutions:**
    *   Use DNS to route to **regions/data centers**, then use **application load balancers** within each region.
    *   Implement **health checks** at application layer behind DNS.
*   **TTL Considerations:**
    *   **Low TTL (30-60s):** For dynamic environments, faster failover.
    *   **High TTL (hours-days):** For stable services, better caching.
    *   **Trade-off:** Lower TTL = faster changes but more DNS queries.

**7. Cloudflare's Approach (as a CDN Provider)**
*   **Anycast Network:** Same IP address announced from all data centers globally.
*   **Intelligent Routing:** Routes to optimal data center based on real-time network conditions.
*   **Load Balancing Service:** Offers DNS-based load balancing with health checks, failover, and geographic routing.
*   **Combination:** Uses **DNS for geographic direction** + **anycast for optimal routing within network**.

---

#### **Summary / Key Takeaways for Interview:**

*   **DNS Load Balancing is Simple but Limited:** It's an **easy-to-implement first layer** of distribution but lacks intelligence and reliability for critical applications.
*   **Caching is the Achilles Heel:** **DNS caching at multiple levels** (browser, OS, ISP) means clients may **stick to one IP** for the TTL duration, defeating round-robin distribution.
*   **No Health Awareness:** Unless using advanced dynamic DNS, it has **no knowledge of server health**—can direct traffic to dead servers.
*   **Best for Geographic/Coarse Distribution:** Most effective for **routing users to nearest data center** (GeoDNS) rather than fine-grained load balancing.
*   **Part of a Larger Strategy:** Should be used **in combination with other load balancing techniques** (application load balancers, anycast) for production systems.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"What is DNS load balancing and what are its limitations?"** → Definition: Distributing traffic by returning different IPs in DNS responses. **Limitations:** 1) **No health checks** (can route to dead servers), 2) **Caching defeats distribution**, 3) **Uneven load** (clients vary in request volume), 4) **No session persistence**, 5) **Slow failover** due to TTL.
*   **"Why might DNS round-robin not actually balance load evenly?"** → Because: 1) **DNS caching** means clients reuse same IP for TTL duration, 2) **Different client behavior** (one client makes 1000 requests, another makes 1), 3) **No awareness of actual server load** (all servers treated equally regardless of current utilization).
*   **"How would you implement a more robust load balancing solution?"** → **Multi-tier approach:** 1) **DNS level:** GeoDNS to route to nearest region, 2) **Region level:** Application load balancer (ELB/ALB) with health checks and advanced algorithms, 3) **Server level:** Use **anycast** or **client-side load balancing** for microservices. DNS is just the first hop.
*   **"What's the impact of TTL on DNS load balancing?"** → **Low TTL (e.g., 60s):** Faster failover, quicker propagation of changes, but **more DNS queries** (increased load on DNS servers). **High TTL (e.g., 24h):** Fewer DNS queries, better caching, but **slow failover** and **stale routing** during changes. Choose based on volatility of backend.
*   **"When would you choose DNS load balancing over an application load balancer?"** → **Choose DNS LB when:** 1) **Geographic distribution** is primary need, 2) **Simple, cost-effective solution** for non-critical services, 3) **Distributing traffic between data centers** (not within a data center), 4) **Static content delivery** with multiple origins. **Otherwise, use application LB.**