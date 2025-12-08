
## CDN System Design Interview Checklist

- **Core Components**
    
    |Component|Role|
    |---|---|
    |**PoP**|Physical locations with edge servers|
    |**Edge Server**|Caches/serves content near users|
    |**Origin Server**|Source of truth for original content|
    
- **Key Mechanisms**
    
    |Mechanism|Purpose|
    |---|---|
    |**Anycast Routing**|Directs to nearest PoP by network topology|
    |**Cache Warming**|Pre-populate popular content|
    |**TTL**|Cache expiration time|
    |**Cache Invalidation**|Remove/update stale content|
    |**Cache Purging**|Force remove specific content|
    
- **Routing Strategies**
    
    |Strategy|How It Works|
    |---|---|
    |**DNS-based**|Returns optimal edge IP|
    |**GeoIP**|Routes by user location|
    |**Anycast**|Shared IP → network routes nearest|
    
- **Network Topologies**
    
    |Topology|Characteristics|
    |---|---|
    |**Flat**|All edges → origin (simple, doesn't scale)|
    |**Hierarchical**|Tiered caching (edge → parent → origin)|
    |**Mesh**|Edges peer/share content|
    |**Hybrid**|Mix for optimization|
    
- **Benefits Quantified**
    
    - **Latency:** 50-80% reduction via edge proximity
        
    - **Origin Offload:** 70-90% traffic reduction
        
    - **Availability:** Multi-PoP redundancy
        
    - **Security:** DDoS/WAF at edge
        
- **Popular CDNs & Features**
    
    |CDN|Key Features|
    |---|---|
    |**Cloudflare**|Anycast, WAF, edge compute|
    |**Akamai**|Enterprise, video streaming|
    |**AWS CloudFront**|S3 integration, Lambda@Edge|
    |**Fastly**|Real-time purging, VCL|
    

## 60-Second Recap

- **CDN Flow:** User → Anycast/DNS → Edge (cache hit/miss) → Origin → Cache → User.
    
- **Key Tech:** PoPs (global), TTL/invalidation, anycast routing.
    
- **Topologies:** Hierarchical/mesh for scale.
    
- **Benefits:** Latency ↓, origin offload, DDoS protection.
    
- **Gold:** CloudFront + Lambda@Edge + cache warming.
    

**Reference**: [CDN System Design Basics](https://www.designgurus.io/blog/content-delivery-network-cdn-system-design-basics)[designgurus](https://www.designgurus.io/blog/content-delivery-network-cdn-system-design-basics)​

1. [https://www.designgurus.io/blog/content-delivery-network-cdn-system-design-basics](https://www.designgurus.io/blog/content-delivery-network-cdn-system-design-basics)
### **Cornell Notes: Content Delivery Network (CDN) System Design Basics**

**Source:** Design Gurus Blog | [Content Delivery Network (CDN) System Design Basics](https://www.designgurus.io/blog/content-delivery-network-cdn-system-design-basics)

**Main Idea:** A **Content Delivery Network (CDN)** is a **globally distributed network of proxy servers** that caches and serves content from locations geographically closer to users. Its primary purpose is to **reduce latency, decrease origin server load, improve availability, and enhance security** for web applications.

---

#### **Notes: Core Concepts & Architecture**

**1. What Problem Does a CDN Solve?**
*   **High Latency:** Users far from origin server experience slow load times.
*   **Origin Server Overload:** Popular content causes traffic spikes that overwhelm origin.
*   **Single Points of Failure:** Origin server downtime affects all users globally.
*   **High Bandwidth Costs:** Repeatedly serving same content from origin is expensive.

**2. How a CDN Works (Step-by-Step)**
1.  **First Request:** User requests `example.com/image.jpg`.
2.  **DNS Resolution:** DNS directs user to **nearest CDN edge server** (via GeoDNS).
3.  **Cache Check:** Edge server checks if content is cached locally.
4.  **Cache Miss:** Edge server fetches from **origin server** or **parent CDN node**, caches it, then serves to user.
5.  **Cache Hit:** Edge server serves cached content directly (fast).
6.  **Subsequent Requests:** Same region users get content from edge cache.

**3. CDN Architecture Components**
*   **Origin Server:** Original source of content (your application servers).
*   **Edge Servers/PoPs (Points of Presence):** Distributed servers that cache and serve content. Located in **internet exchange points (IXPs)**.
*   **CDN Network:**
    *   **Tier 1:** Core backbone (high-capacity, few locations).
    *   **Tier 2:** Regional distribution.
    *   **Tier 3:** Edge locations (closest to users, many locations).
*   **Request Routing System:** Uses **Anycast, GeoDNS, BGP routing** to direct users to optimal edge server.

**4. Key CDN Features & Capabilities**
*   **Caching Strategies:**
    *   **TTL-based:** Content cached for specified time.
    *   **Cache Control Headers:** `Cache-Control`, `Expires`, `ETag`.
    *   **Purge/Invalidation:** Manually clear cached content.
*   **Content Optimization:**
    *   **Image Optimization:** Resizing, compression, WebP conversion.
    *   **Minification:** CSS, JS, HTML.
    *   **Compression:** Gzip, Brotli.
    *   **HTTP/2, HTTP/3 support.**
*   **Security Features:**
    *   **DDoS Protection:** Absorbs attack traffic at edge.
    *   **Web Application Firewall (WAF):** Filters malicious requests.
    *   **SSL/TLS Termination:** Offloads encryption/decryption.
    *   **Bot Mitigation.**

**5. CDN Caching Patterns**
*   **Pull CDN (Passive):**
    *   Content pulled from origin on first request (cache miss).
    *   **Pros:** Simple setup, no pre-pushing required.
    *   **Cons:** First user experiences latency (cold cache).
*   **Push CDN (Active):**
    *   Content pushed to CDN proactively (before requests).
    *   **Pros:** Guaranteed cache presence, best performance.
    *   **Cons:** More complex, requires storage management.
*   **Hybrid:** Critical content pushed, less critical pulled.

**6. When to Use a CDN**
*   **Static Content:** Images, CSS, JS, videos, downloads.
*   **Dynamic Content Acceleration:** API responses, personalized content (via edge computing).
*   **Live & On-Demand Video Streaming.**
*   **Software/Game Downloads.**
*   **Global Web Applications** with users worldwide.

**7. Popular CDN Providers**
*   **Cloudflare:** Security-focused, generous free tier.
*   **Amazon CloudFront:** Tight AWS integration.
*   **Akamai:** Enterprise, large scale.
*   **Fastly:** Real-time purging, edge compute.
*   **Google Cloud CDN:** Google network integration.

**8. Design Considerations for CDN Integration**
*   **Cache Invalidation Strategy:**
    *   Versioned URLs (`image-v2.jpg`).
    *   Query strings with cache busting (`?v=timestamp`).
    *   Manual purge APIs.
*   **Origin Shield/Sharding:** Protect origin from thundering herd (many edge servers hitting origin simultaneously).
*   **Cache Hierarchy:** Multi-tier caching (edge → regional → origin).
*   **Monitoring & Analytics:** Cache hit ratios, bandwidth, latency, origin load.

**9. Cost Considerations**
*   **Pricing Models:** Pay-per-GB, monthly plans, request-based.
*   **Cost Factors:** Bandwidth, requests, purge operations, advanced features.
*   **Optimization:** Cache hit ratio improvement directly reduces costs.

---

#### **Summary / Key Takeaways for Interview:**

*   **CDN = Distributed Caching Network:** Serves content from **edge locations close to users** to **reduce latency** and **offload origin servers**.
*   **Cache Hit Ratio is Critical:** Higher cache hit ratio = better performance & lower costs. Design for high cacheability.
*   **Not Just for Static Content:** Modern CDNs support **dynamic content acceleration, edge computing, and security services**.
*   **Multiple Tiers:** Edge PoPs (many, close to users) → Regional → Core → Origin. Requests routed via **GeoDNS/Anycast**.
*   **Strategic Integration Required:** Need **cache invalidation strategy, versioning, monitoring, and origin protection** for effective CDN use.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"How does a CDN work and why is it important?"** → Explain the **cache hit/miss flow**: User request → DNS to nearest edge → cache check → serve or fetch from origin. **Importance:** 1) **Reduces latency** (content closer), 2) **Reduces origin load** (caching), 3) **Improves availability** (distributed), 4) **Enhances security** (DDoS protection at edge).
*   **"What's the difference between pull and push CDNs?"** → **Pull CDN:** Content cached **on first request** (cache miss). Simple but cold start latency. **Push CDN:** Content **pre-loaded** to CDN before requests. Better performance but requires active management. Most use **pull for dynamic, push for critical static assets**.
*   **"How do you handle cache invalidation when content changes?"** → **Strategies:** 1) **Versioned URLs** (`/assets/image-v2.jpg`), 2) **Cache busting query strings** (`?v=20230101`), 3) **Manual purge** via CDN API, 4) **Short TTL** for frequently changing content, 5) **Surrogate keys/tags** for bulk purging related content.
*   **"What metrics would you monitor for CDN performance?"** → 1) **Cache hit ratio** (critical), 2) **Latency/response time** by region, 3) **Bandwidth usage**, 4) **Origin requests** (should be low), 5) **Error rates**, 6) **Purge operations**, 7) **Cost per GB/request**.
*   **"When would a CDN NOT be beneficial?"** → When: 1) **Users are geographically concentrated** near origin, 2) **Content is highly dynamic/personalized** with low cacheability, 3) **Small scale** where CDN costs outweigh benefits, 4) **Real-time applications** requiring direct connections (websockets, gaming), though modern CDNs support some real-time features.


### **A-Z of Content Delivery Networks (CDN) - Medium Article**

**Source:** Medium | [A-Z of Content Delivery Networks (CDN): Making the Internet Faster and More Reliable](https://medium.com/@roopa.kushtagi/a-z-of-content-delivery-networks-cdn-making-the-internet-faster-and-more-reliable-57786b46a058)

**Main Idea:** CDNs are **critical internet infrastructure** that accelerate content delivery by strategically caching it at edge locations globally. They solve fundamental web performance problems through **geographic distribution, intelligent caching, and traffic optimization**, making the internet faster, more reliable, and more secure for everyone.

---

#### **Notes: Comprehensive CDN Concepts**

**1. The Core CDN Value Proposition**
*   **Latency Reduction:** Brings content **physically closer** to end-users via distributed edge servers.
*   **Bandwidth Savings:** Reduces traffic on origin servers through caching.
*   **Improved Reliability:** Distributed architecture provides **fault tolerance** against server failures and DDoS attacks.
*   **Enhanced Security:** Provides a **protective shield** for origin servers against attacks.

**2. How CDNs Actually Work: The Detailed Flow**
1.  **User** requests content via browser.
2.  **Local DNS** resolves to CDN's DNS (not origin server's DNS).
3.  **CDN's DNS** uses **GeoIP intelligence** to identify user location.
4.  **Optimal Edge Server** is selected based on: network proximity, server health, load.
5.  **Edge Server** checks local cache:
    *   **Cache Hit:** Serves immediately.
    *   **Cache Miss:** Fetches from **origin server** or **parent CDN node** (hierarchical caching).
6.  **Content Served** with CDN optimizations (compression, image optimization).

**3. CDN Architecture Deep Dive**
*   **Points of Presence (PoPs):** Physical data centers containing edge servers.
*   **Edge Servers:** The actual caching servers within PoPs.
*   **Origin Server:** The original content source.
*   **CDN Backbone:** High-speed private network connecting PoPs (faster than public internet).
*   **Request Routing Infrastructure:**
    *   **Anycast Routing:** Same IP address announced from multiple locations; BGP routes to nearest.
    *   **GeoDNS:** Returns different IPs based on requester location.
    *   **Load Balancers:** Distribute traffic within PoPs.

**4. Caching Strategies & Mechanisms**
*   **Time-to-Live (TTL):** Duration content stays cached. Configurable per content type.
*   **Cache-Control Headers:** `max-age`, `no-cache`, `must-revalidate`.
*   **Cache Invalidation Methods:**
    *   **TTL Expiry:** Automatic after time period.
    *   **Manual Purging:** Force clear via CDN dashboard/API.
    *   **Versioning:** Change URLs (`style-v2.css`).
*   **Cache Hierarchy:**
    *   **Edge Cache:** Local PoP cache.
    *   **Mid-tier/Regional Cache:** Serves multiple PoPs.
    *   **Origin Shield:** Dedicated cache layer protecting origin.

**5. Types of Content CDNs Deliver**
*   **Static Content:** Images, CSS, JS, fonts, PDFs (primary use case).
*   **Dynamic Content:** Personalized pages, API responses (via **dynamic site acceleration** techniques).
*   **Streaming Media:** Video-on-demand (VOD), live streaming.
*   **Large Files:** Software downloads, game patches, OS updates.
*   **Secure Content:** SSL/TLS encrypted content.

**6. Key Performance Optimizations**
*   **Image Optimization:** Automatic resizing, format conversion (WebP), lazy loading.
*   **Minification & Compression:** CSS/JS minification, Gzip/Brotli compression.
*   **Protocol Optimization:** HTTP/2, HTTP/3 (QUIC) support.
*   **TCP Optimization:** Better congestion control, persistent connections.
*   **Prefetching/Preloading:** Anticipate user needs.
*   **Lazy Loading:** Load below-the-fold content later.

**7. Security Features of Modern CDNs**
*   **DDoS Protection:** Absorb attack traffic at edge.
*   **Web Application Firewall (WAF):** Filter malicious requests.
*   **SSL/TLS Termination:** Offloads encryption overhead from origin.
*   **Bot Management:** Distinguish legitimate vs. malicious bots.
*   **Rate Limiting:** Prevent abuse.
*   **DNSSEC:** DNS security extensions.

**8. Popular CDN Providers & Their Specialties**
*   **Cloudflare:** Security-focused, extensive free tier, global network.
*   **Akamai:** Pioneer, enterprise-scale, advanced security.
*   **Amazon CloudFront:** Tight AWS integration, pay-as-you-go.
*   **Fastly:** Real-time purging, edge computing (Compute@Edge).
*   **Google Cloud CDN:** Leverages Google's global network.
*   **Microsoft Azure CDN:** Azure ecosystem integration.

**9. Implementation Considerations**
*   **Cache Hit Ratio:** Target >90% for static content. Measure and optimize.
*   **Cost Structure:** Understand pricing (bandwidth, requests, features).
*   **Monitoring & Analytics:** Track performance metrics, user experience.
*   **Content Segmentation:** Different TTLs for different content types.
*   **Fallback Mechanisms:** What happens if CDN fails? Origin should still serve.

**10. Future CDN Trends**
*   **Edge Computing:** Run applications at edge (AWS Lambda@Edge, Cloudflare Workers).
*   **5G Optimization:** CDNs adapting for 5G networks.
*   **IoT Support:** Serving content to billions of IoT devices.
*   **AI/ML Integration:** Intelligent routing, predictive caching.
*   **Zero Trust Security:** Security implemented at edge.

---

#### **Summary / Key Takeaways for Interview:**

*   **CDNs Are Distributed Caching Systems:** They solve the **"last mile" problem** by placing content closer to users through globally distributed edge servers.
*   **Multiple Layers of Optimization:** Beyond simple caching, modern CDNs provide **image optimization, compression, protocol upgrades, and security**—all at the edge.
*   **Architecture Matters:** Effective CDNs use **anycast routing, hierarchical caching, and private backbones** to outperform direct origin connections.
*   **Not Just Static Content:** Through **dynamic site acceleration and edge computing**, CDNs now accelerate personalized content and applications.
*   **Critical Internet Infrastructure:** CDNs have become essential for **performance, security, and reliability** of modern web applications at global scale.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"Explain how a CDN reduces latency for a user in Australia accessing a US-based website."** → The CDN **caches content at edge servers in Australia/Southeast Asia**. When the Australian user requests content, they're routed to the nearest edge server (via GeoDNS/Anycast) which serves cached content **locally** instead of traversing the Pacific to the US origin. This reduces **network hops and physical distance**.
*   **"What's the difference between anycast and GeoDNS routing in CDNs?"** → **Anycast:** Same IP address announced from multiple locations; BGP routes to nearest. **Faster, simpler for users**. **GeoDNS:** DNS returns different IPs based on requester's location. **More control, but dependent on DNS caching**. Modern CDNs often use **both**: GeoDNS for coarse geographic routing, anycast for fine-grained optimization within regions.
*   **"How would you ensure a CDN serves the latest version of your website's CSS files?"** → **Multiple strategies:** 1) **Versioned filenames** (`styles-v2.1.css`), 2) **Cache-busting query strings** (`styles.css?v=2.1`), 3) **Low TTL** for frequently changing assets (e.g., 5 min), 4) **Manual purge** via CDN API after deployment, 5) **Surrogate keys** to group related content for bulk purging.
*   **"What metrics indicate a CDN is working effectively?"** → 1) **High cache hit ratio** (>90%), 2) **Reduced origin load** (bandwidth/requests to origin), 3) **Lower latency** by geography (measured via RTT), 4) **Improved page load times** (Web Vitals), 5) **Reduced bandwidth costs**, 6) **High availability** (uptime).
*   **"When would you NOT recommend using a CDN?"** → 1) **Entirely local audience** near origin, 2) **Highly dynamic, personalized content** with near-zero cacheability, 3) **Real-time applications** requiring direct persistent connections (some websocket use cases), 4) **Regulatory constraints** requiring data to stay in specific regions, 5) **Very low traffic sites** where CDN costs outweigh benefits.

### **Pull CDN vs Push CDN**

**Source:** ArvanCloud Blog | [Pull CDN vs Push CDN](https://www.arvancloud.ir/blog/en/pull-cdn-vs-push-cdn/)

**Main Idea:** CDNs use two primary caching models—**Pull (origin pull)** and **Push (origin push)**—that differ in **how content gets to edge servers**. The choice depends on **content type, update frequency, traffic patterns, and operational complexity**. Understanding both models is key to optimizing CDN performance and cost.

---

#### **Notes: Core Models & Comparison**

**1. Pull CDN (Origin Pull / Passive CDN)**
*   **How it Works:**
    1.  **First Request:** User requests content from edge server.
    2.  **Cache Miss:** Edge server **pulls/fetches** content from origin server.
    3.  **Cache & Serve:** Edge caches content and serves it to user.
    4.  **Subsequent Requests:** Served from edge cache until TTL expires.
*   **Characteristics:**
    *   **Lazy Loading:** Content cached only when requested.
    *   **TTL-Based:** Cache duration controlled by HTTP headers (`Cache-Control`, `Expires`).
    *   **Automatic Updates:** Edge re-fetches when cache expires.
*   **Pros:**
    *   **Simple Setup:** Just point CDN to origin URL.
    *   **Low Maintenance:** No need to manually upload content.
    *   **Storage Efficient:** Only caches requested content.
    *   **Handles Dynamic Content:** Can cache API responses, personalized pages.
*   **Cons:**
    *   **Cold Start Latency:** First user experiences delay (cache miss penalty).
    *   **Origin Load on Misses:** Cache misses hit origin server.
    *   **TTL Constraints:** Updates delayed until cache expires.
    *   **Thundering Herd:** Many cache misses simultaneously can overload origin.

**2. Push CDN (Origin Push / Upload CDN)**
*   **How it Works:**
    1.  **Proactive Upload:** Developer **actively uploads/pushes** content to CDN.
    2.  **CDN Distribution:** CDN distributes to all edge locations.
    3.  **Ready State:** Content available at edges before any user requests.
    4.  **Direct Serving:** User requests served immediately from edge.
*   **Characteristics:**
    *   **Proactive Distribution:** Content deployed before requests.
    *   **Explicit Management:** Manual or automated uploads via API/FTP.
    *   **Version Control:** Developer controls exactly what's in CDN.
*   **Pros:**
    *   **No Cold Start:** Content pre-loaded, fastest first-byte time.
    *   **Origin Protection:** Never hits origin for cached content.
    *   **Precise Control:** Exact content versioning and distribution.
    *   **Better for Large Files:** Efficient distribution of big assets.
*   **Cons:**
    *   **Complex Setup:** Requires upload/integration workflow.
    *   **Storage Management:** Need to manage CDN storage limits.
    *   **Manual Updates:** Must push updates when content changes.
    *   **Inefficient for Rarely Accessed Content:** Wastes cache space.

**3. Head-to-Head Comparison**
| **Aspect** | **Pull CDN** | **Push CDN** |
|------------|--------------|--------------|
| **Initial Setup** | Simple (just CNAME) | Complex (upload workflow) |
| **Content Update** | Automatic (TTL-based) | Manual/API push required |
| **First Request Latency** | Higher (cache miss) | Lowest (pre-cached) |
| **Origin Load** | High during cache misses | Zero for cached content |
| **Storage Management** | CDN manages automatically | You manage CDN storage |
| **Best For** | Dynamic content, websites | Static assets, large files, releases |
| **Cost Model** | Bandwidth + requests | Storage + bandwidth |

**4. Hybrid Approach**
*   **Combination Strategy:** Use both models for different content types.
*   **Example:**
    *   **Push CDN** for core assets: CSS, JS, fonts, hero images.
    *   **Pull CDN** for user-generated content, product images, dynamic pages.
*   **Intelligent Tiering:** Some CDNs automatically optimize based on access patterns.

**5. Decision Factors: When to Use Each**
*   **Choose PULL CDN When:**
    *   Content changes frequently.
    *   You have a large, unpredictable catalog (e-commerce product images).
    *   You want minimal operational overhead.
    *   Serving dynamic or personalized content.
    *   Content is accessed infrequently.
*   **Choose PUSH CDN When:**
    *   Performance is critical (no cold starts acceptable).
    *   Content is stable (CSS/JS libraries, app binaries).
    *   You need precise version control.
    *   Serving large files (software downloads, videos).
    *   You want zero origin load for cached content.

**6. Technical Implementation Details**
*   **Pull CDN Setup:**
    *   Configure CNAME record pointing to CDN.
    *   Set origin server URL in CDN config.
    *   Configure TTL/cache rules.
*   **Push CDN Setup:**
    *   Upload files via FTP/SFTP/API/SDK.
    *   Organize content with directory structure.
    *   Set up CI/CD pipeline for automated pushes.
*   **Cache Invalidation:**
    *   **Pull:** Versioned URLs, cache purge API, TTL expiry.
    *   **Push:** Upload new version, delete old files.

**7. Real-World Examples**
*   **Pull CDN Use Cases:**
    *   News websites (frequently updated articles).
    *   E-commerce sites (product images, descriptions).
    *   Social media platforms (user uploads).
    *   API responses with caching headers.
*   **Push CDN Use Cases:**
    *   Mobile app binary distribution.
    *   Game patch/update delivery.
    *   JavaScript libraries (jQuery, React CDN).
    *   Operating system ISO downloads.
    *   Movie/TV show streaming (pre-positioned content).

---

#### **Summary / Key Takeaways for Interview:**

*   **Fundamental Difference:** **Pull = CDN fetches on demand**, **Push = You upload proactively**. This distinction drives all other trade-offs.
*   **Performance vs. Complexity Trade-off:** **Push CDN offers better performance** (no cold starts) but **requires more operational work**. **Pull CDN is simpler** but suffers from cache miss penalties.
*   **Content Characteristics Dictate Choice:** **Static, versioned, critical-path assets** → Push. **Dynamic, frequently changing, unpredictable content** → Pull.
*   **Origin Protection Matters:** **Push CDN completely shields origin** (zero requests). **Pull CDN exposes origin** to cache misses, requiring scaling and protection measures.
*   **Most Real Systems Use Both:** A sophisticated CDN strategy often employs **push for core static assets** and **pull for dynamic/user content**—a hybrid approach.

#### **Potential Interview Questions & How to Use These Notes:**

*   **"Explain the difference between pull and push CDNs with examples."** → **Pull CDN:** Like a library that orders a book when someone requests it (first requester waits). **Push CDN:** Like stocking all libraries with bestsellers before anyone asks (immediate availability). Real example: jQuery CDN (push) vs. news article images (pull).
*   **"When would you choose push CDN over pull CDN?"** → Choose **push** when: 1) **Performance is critical** (no cold start acceptable), 2) **Content is stable** (CSS/JS libraries), 3) **Large file distribution** (software downloads), 4) **Need precise version control**, 5) **Want zero origin load** for cached content. Example: Delivering iOS app updates.
*   **"What are the challenges of using a pull CDN for a high-traffic news site?"** → 1) **Thundering herd problem** when breaking news publishes (many cache misses simultaneously), 2) **Origin overload** during traffic spikes, 3) **Stale content** if TTL too high, 4) **Cache fragmentation** across edges for rarely read articles. **Solutions:** Pre-warming cache, lower TTL for breaking news, origin scaling.
*   **"How would you implement a hybrid CDN strategy for an e-commerce site?"** → **Push to CDN:** Core assets (CSS, JS, fonts, UI images) via CI/CD pipeline. **Pull CDN:** Product images, user reviews, inventory data (dynamic). **Special handling:** Flash sale product images might be **pre-pushed** before sale starts. Use **versioned URLs** for push assets, **cache purge API** for critical updates.
*   **"What's the 'cold start' problem in pull CDNs and how do you mitigate it?"** → **Problem:** First request for uncached content hits origin, causing high latency for that user. **Mitigations:** 1) **Pre-warming cache** by programmatically requesting critical assets, 2) **Lower TTL + stale-while-revalidate** patterns, 3) **Predictive caching** based on user patterns, 4) **For critical content, use push CDN instead**, 5) **Implement origin shield** to reduce origin load during misses.

