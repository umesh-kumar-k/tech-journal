# **TCP vs UDP - Quick Reference**

**Source:** AlgoMaster, "TCP vs UDP in System Design"  
**URL:** https://algomaster.io/learn/system-design/tcp-vs-udp

## **1. Essential Question**
*What are the fundamental differences between TCP and UDP, when should you choose each, and what are their trade-offs in system design?*

---

## **2. Main Ideas & Key Details**

### **A. Core Differences at a Glance**

| **Aspect** | **TCP (Transmission Control Protocol)** | **UDP (User Datagram Protocol)** |
|------------|----------------------------------------|----------------------------------|
| **Connection** | Connection-oriented (handshake) | Connectionless (no setup) |
| **Reliability** | Guaranteed delivery | Best effort (may lose packets) |
| **Ordering** | In-order delivery | No ordering guarantee |
| **Speed** | Slower (overhead) | Faster (minimal overhead) |
| **Congestion Control** | Yes (adjusts rate) | No (sends at line rate) |
| **Use Cases** | Web, email, file transfer | Video streaming, DNS, gaming |

### **B. TCP Deep Dive**
#### **Connection Establishment (3-Way Handshake):**
```
1. SYN: Client → Server (I want to connect)
2. SYN-ACK: Server → Client (OK, let's connect)
3. ACK: Client → Server (Great, connected!)
```
- **Result:** Established connection with sequence numbers
- **Time:** 1.5 RTT (Round Trip Time)

#### **Reliability Mechanisms:**
1. **Sequence Numbers:** Track order of bytes
2. **Acknowledgments (ACK):** Confirm receipt
3. **Retransmission:** Resend lost packets after timeout
4. **Flow Control:** Prevent overwhelming receiver (sliding window)
5. **Congestion Control:** Adjust rate based on network conditions

#### **TCP Header (20+ bytes):**
- Source/destination ports
- Sequence/Acknowledgment numbers
- Window size (flow control)
- Flags (SYN, ACK, FIN, RST)
- Checksum for error detection

#### **Good For:**
- **Web browsing** (HTTP/HTTPS)
- **Email** (SMTP, IMAP)
- **File transfers** (FTP, SCP)
- **Remote access** (SSH)
- **When data integrity** is critical

### **C. UDP Deep Dive**
#### **Connectionless Nature:**
- **No handshake** - just send packets
- **No state** maintained between sender/receiver
- **Fire-and-forget** approach
- **Stateless** at protocol level

#### **Header (8 bytes only):**
- Source/destination ports
- Length
- Checksum (optional in IPv4)

#### **Key Characteristics:**
- **No delivery guarantees** (packets may be lost, duplicated, reordered)
- **No flow/congestion control** (can flood network)
- **Lower latency** (no retransmission delays)
- **Lower overhead** (smaller headers)

#### **Good For:**
- **Real-time video/audio** (Zoom, YouTube Live)
- **DNS lookups**
- **Online gaming** (FPS, MMOs)
- **VoIP** (Voice over IP)
- **Live streaming** (Twitch)
- **IoT sensor data**
- **When speed > perfect reliability**

### **D. System Design Decision Framework**

#### **Choose TCP When:**
1. **Data must arrive** (file transfer, database replication)
2. **Order matters** (web pages, documents)
3. **Can tolerate latency** but not errors
4. **Need congestion awareness** (fair network sharing)
5. **Application doesn't handle** reliability itself

#### **Choose UDP When:**
1. **Speed is critical** (real-time applications)
2. **Some data loss acceptable** (video frame, voice packet)
3. **Application handles** reliability if needed
4. **Broadcast/multicast** needed (UDP supports, TCP doesn't)
5. **Stateless communication** (DNS queries)

#### **Hybrid Approaches:**
- **QUIC:** UDP-based with TCP-like reliability (HTTP/3)
- **Custom protocols:** UDP with application-level reliability
- **TCP for control, UDP for data:** Video conferencing setups

### **E. Performance Comparison**

#### **Latency:**
- **TCP:** Higher due to handshake, ACKs, retransmissions
- **UDP:** Lower, immediate transmission

#### **Throughput:**
- **TCP:** Adapts to network conditions, fair sharing
- **UDP:** Can achieve line rate (may congest network)

#### **Reliability:**
- **TCP:** 100% reliable (with retries until success/timeout)
- **UDP:** Unreliable by design (application handles if needed)

#### **Resource Usage:**
- **TCP:** More memory (connection state), CPU (processing)
- **UDP:** Less resources, simpler implementation

### **F. Common Use Case Examples**

#### **Video Streaming (UDP):**
- **Problem:** Need low latency, some frame loss acceptable
- **Solution:** UDP for video packets
- **Why:** Dropped frames less noticeable than buffering delay
- **Example:** Zoom, YouTube Live (with buffering at app level)

#### **Online Gaming (UDP):**
- **Problem:** Real-time movement updates critical
- **Solution:** UDP for position updates
- **Trade-off:** Accept occasional glitch vs laggy gameplay
- **Example:** Call of Duty, Fortnite

#### **DNS (UDP):**
- **Problem:** Fast simple queries, small responses
- **Solution:** UDP (falls back to TCP for large responses)
- **Why:** Single request-response, small size
- **Port:** 53

#### **HTTP/Web (TCP):**
- **Problem:** Need reliable page loading
- **Solution:** TCP (HTTP/1.1, HTTP/2)
- **Exception:** HTTP/3 uses QUIC (UDP-based)
- **Why:** Can't have missing pieces of web page

### **G. Modern Developments**

#### **QUIC (HTTP/3):**
- **Runs on UDP** with built-in reliability
- **0-RTT handshake** (faster than TCP's 1.5 RTT)
- **Multiplexing** without head-of-line blocking
- **Encryption** built-in (TLS 1.3)
- **Used by:** Google, Cloudflare, major CDNs

#### **WebRTC:**
- **Uses both** TCP and UDP
- **UDP for media** (video/audio)
- **TCP for data channel** (file transfer, chat)
- **Adaptive:** Can switch based on network conditions

#### **IoT Protocols:**
- **MQTT:** Typically TCP-based (reliable messaging)
- **CoAP:** UDP-based (constrained devices)
- **Choice depends on** power constraints, reliability needs

---

## **3. Summary / Synthesis**
**TCP** = Reliable, ordered, connection-oriented. Choose for **data integrity** over speed.  
**UDP** = Fast, connectionless, unreliable. Choose for **real-time performance** over perfection.  

**TCP** adds overhead for reliability; **UDP** lets applications handle reliability if needed.  
Modern protocols like **QUIC** combine UDP speed with TCP-like reliability.

---

## **4. Key Terms**
- **ACK:** Acknowledgment (TCP confirmations)
- **RTT:** Round Trip Time (ping time)
- **Handshake:** Connection setup (TCP: 3-way)
- **Congestion Control:** Adjusting send rate to network conditions
- **Head-of-Line Blocking:** One delayed packet holds up others
- **MTU:** Maximum Transmission Unit (packet size limit)

---

## **5. Quick Decision Checklist**

#### **Ask these questions:**
1. **Can data be lost?** Yes → UDP, No → TCP
2. **Need in-order delivery?** Yes → TCP, No → UDP
3. **Real-time requirement?** Yes → UDP, No → TCP
4. **Application handles reliability?** Yes → UDP, No → TCP
5. **Broadcast/multicast needed?** Yes → UDP, No → TCP

#### **Default rules:**
- **When in doubt,** use TCP
- **For media streaming,** use UDP
- **For control channels,** use TCP
- **For data channels,** evaluate requirements

---

## **6. Interview Quick Answers**

**Q: "When would you choose UDP over TCP?"**
```
"Choose UDP when:
1. Real-time requirement (video, voice, gaming)
2. Some data loss acceptable (better to skip than buffer)
3. Application handles reliability itself
4. Need broadcast/multicast
5. Connection overhead unacceptable

Example: Video call uses UDP for media, TCP for signaling"
```

**Q: "How does TCP ensure reliability?"**
```
"Four main mechanisms:
1. Sequence numbers - track packet order
2. Acknowledgments (ACKs) - confirm receipt
3. Retransmission - resend lost packets after timeout
4. Checksums - detect corruption

Plus flow control (window size) and congestion control"
```

**Q: "What's QUIC and why use it?"**
```
"QUIC = Quick UDP Internet Connections
- UDP-based but with TCP-like reliability
- 0-RTT handshake (faster than TCP)
- Built-in encryption (TLS 1.3)
- No head-of-line blocking
- Used by HTTP/3 for faster web
- Trade-off: More complex than plain UDP"
```

---

## **7. Port Numbers to Know**
- **TCP 80:** HTTP
- **TCP 443:** HTTPS
- **TCP 25:** SMTP (email)
- **TCP 22:** SSH
- **UDP 53:** DNS
- **UDP 123:** NTP (time)
- **UDP 67/68:** DHCP
- **UDP 5060:** SIP (VoIP)

---

## **8. Troubleshooting Tips**

#### **TCP Issues:**
- **Slow connections:** Check congestion window, packet loss
- **Connection refused:** Server not listening, firewall blocking
- **Reset connections:** Check keepalive settings, timeouts

#### **UDP Issues:**
- **Lost packets:** Expected behavior, add application retry if needed
- **Firewall blocking:** UDP stateless, harder for firewalls to track
- **NAT traversal:** More complex with UDP (STUN/TURN needed)

---

## **9. Before Interview Checklist**
✅ Know 3-way handshake steps  
✅ Understand ACK/retransmission concepts  
✅ Can explain flow vs congestion control  
✅ Remember key use cases for each  
✅ Know modern developments (QUIC, HTTP/3)

---

## **10. 60-Second Recap**
1. **TCP:** Reliable, ordered, connection-oriented (web, email, files)
2. **UDP:** Fast, connectionless, unreliable (video, gaming, DNS)
3. **TCP has:** Handshake, ACKs, retransmission, flow control
4. **UDP is:** Fire-and-forget, minimal overhead
5. **Choose based on:** Reliability needs vs speed requirements
6. **Modern:** QUIC combines best of both (UDP + reliability)

**Remember:** In interviews, focus on **trade-offs** not just features. Have **concrete examples** ready (video streaming vs file transfer).

*This quick reference covers TCP vs UDP essentials for system design interviews.*