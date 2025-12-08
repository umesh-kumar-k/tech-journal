# **Bcrypt - Senior Architect Summary**

## **Core Definition & Purpose**
- **Bcrypt:** Adaptive cryptographic **password hashing function** based on the Blowfish cipher.
- **Created by:** Niels Provos and David Mazières (1999) for OpenBSD.
- **Primary Purpose:** **Resist brute-force attacks** via **configurable slowness**.
- **Key Insight:** Makes password verification intentionally slow and computationally expensive, while remaining fast enough for legitimate users.

## **How It Works - The Adaptive Cost Mechanism**
1. **Key Setup Phase (EksBlowfish):** Expensive key derivation using salt and cost factor.
2. **Multiple Rounds:** Encrypts 192-bit magic value using Blowfish in 64-bit blocks.
3. **Output Format:** `$2a$[cost]$[22-character salt][31-character hash]`
   - Example: `$2a$12$R9h/cIPz0gi.URNNX3kh2OPST9/PgBkqquzi.Ss7KIUgO2t0jWMUW`
   - Versions: `$2a$`, `$2b$`, `$2y$` (minor bug fixes).

## **Critical Design Features**
- **Work Factor (Cost Parameter):** Integer (4-31) defining number of iterations (2^cost). 
  - Cost 12 = 2^12 = 4,096 iterations (common default).
  - Doubling cost doubles computation time.
- **Built-in Salt Generation:** Automatically generates and stores random 16-byte (128-bit) salt.
- **Adaptive Nature:** Can increase work factor over time to combat hardware improvements.
- **Fixed Output Length:** 184-bit hash encoded as 31 characters.

## **Security Advantages & Properties**
1. **Time-Adaptive:** Can be made slower as hardware improves (increase work factor).
2. **Resistant to GPU/ASIC Attacks:** Based on Blowfish which uses S-boxes dependent on key state - hard to parallelize.
3. **Built-in Salt:** Prevents rainbow table attacks; ensures same passwords hash differently.
4. **Memory Efficient:** Unlike scrypt/Argon2, uses minimal memory (~4KB).
5. **Battle-Tested:** Used since 1999 in OpenBSD, widely implemented and audited.

## **Comparison with Other Password Hashes**
| **Algorithm** | **Primary Defense** | **Memory Usage** | **Parallelization** | **Adaptive** |
|---------------|---------------------|------------------|---------------------|--------------|
| **PBKDF2** | Iteration count | Low | Easily parallelized | No |
| **Bcrypt** | **Time cost + Blowfish rounds** | Low | **Resistant** | **Yes** |
| **Scrypt** | Memory hardness | High | Memory-bound | Yes |
| **Argon2** | Memory & parallelism | High | Configurable | Yes |

## **Architectural Applications**
### **1. Password Storage (Primary Use)**
- **Default choice** for many frameworks (Ruby on Rails, Django, Spring Security).
- **Verification Process:** Extract salt and cost from stored hash; re-hash input password; compare.
- **Migration Strategy:** Can verify old hashes while generating new ones with higher cost.

### **2. Key Derivation**
- Can be used to derive encryption keys from passwords (though designed for passwords specifically).

## **Implementation & Operational Considerations**
- **Cost Factor Selection:** 
  - **Balance:** User experience vs. security (typically 250ms-500ms verification time).
  - **Modern Baseline:** Cost 12-14 for web applications.
- **Upgrade Strategy:**
  1. Check stored hash's cost factor.
  2. If below threshold, re-hash with higher cost during successful authentication.
- **Version Awareness:** Use `$2b$` or `$2y$` variants to avoid early implementation bugs.
- **Stateless:** Like all hashes, no database lookup beyond retrieving stored hash.

## **Limitations & Modern Context**
- **No Memory-Hardness:** Vulnerable to FPGA/ASIC attacks more than scrypt/Argon2 (though still resistant).
- **Fixed Algorithm:** Based on Blowfish which isn't GPU-optimized but also not memory-intensive.
- **Not the Latest Standard:** Argon2 won the Password Hashing Competition (2015) and is recommended for new systems.
- **Still Highly Secure:** Remains excellent choice, especially for existing systems.

## **Security Best Practices**
1. **Never Use Default Cost:** Increase from default (often 10) to at least 12.
2. **Automatic Re-hashing:** Implement background upgrade to higher cost factors.
3. **Combine with Other Protections:** Rate limiting, account lockout, monitoring.
4. **Use Established Libraries:** `bcrypt` npm, `bcrypt` Ruby gem, `BCryptPasswordEncoder` in Spring.
5. **Store Properly:** Entire hash string (including algorithm, cost, salt) in VARCHAR(60+) field.

---

## **Interview Checklist**
- [ ] Can explain **bcrypt's adaptive nature** and work factor concept.
- [ ] Knows the **hash format structure** (`$2a$12$...`).
- [ ] Can compare **bcrypt vs PBKDF2 vs scrypt vs Argon2**.
- [ ] Understands **why bcrypt resists GPU/ASIC attacks** (Blowfish key schedule).
- [ ] Knows **how to select appropriate cost factor** for current hardware.
- [ ] Can design **password hash migration/upgrade strategy**.
- [ ] Aware of **bcrypt's limitations** (no memory-hardness, older than Argon2).
- [ ] Knows **real-world usage**: default in many frameworks, battle-tested since 1999.
- [ ] Understands **built-in salt generation** and its importance.
- [ ] Can explain **verification process** and stateless nature.

---

## **60-Second Recap (Bullet Points)**
- **Bcrypt is adaptive password hashing** based on Blowfish cipher (1999).
- **Primary defense:** Configurable **work factor** (cost) makes verification intentionally slow.
- **Format:** `$2a$12$[salt][hash]` - includes algorithm, cost, salt, and hash.
- **Resists GPU/ASIC attacks** due to Blowfish key setup being hard to parallelize.
- **Built-in random salt** prevents rainbow table attacks.
- **Default in many frameworks**; battle-tested for over 20 years.
- **Not memory-hard** (unlike scrypt/Argon2) - uses minimal memory.
- **Modern baseline:** Cost 12-14; implement automatic re-hashing to increase over time.
- **Argon2 is newer standard**, but bcrypt remains secure and widely used.
- **Verification is stateless:** Extract parameters from stored hash; re-compute and compare.

---

**Reference Link:** [Hashing in Action: Understanding bcrypt](https://auth0.com/blog/hashing-in-action-understanding-bcrypt/)

**Memorization Mnemonic:** **BCRYPT:**
- **B** = **B**lowfish-based
- **C** = **C**ost factor adaptive
- **R** = **R**esists parallelization (GPU/ASIC)
- **Y** = **Y**ears of proven security (since 1999)
- **P** = **P**assword-specific hashing
- **T** = **T**ime-delayed verification

bCrypt is an **adaptive password hashing algorithm** based on Blowfish cipher's expensive key setup (`eksblowfish`), designed to be slow (200-500ms) with auto-salting to resist brute-force + rainbow tables.[auth0+1](https://auth0.com/blog/hashing-in-action-understanding-bcrypt/)​

---

## **bCrypt – Senior Architect Summary**

## **Core Architecture (Memorize 2 Phases)**

|Phase|Process|Purpose|
|---|---|---|
|**Phase 1: EksBlowfishSetup**|Cost + Salt + Password → Expensive key schedule|Key stretching (slow setup)|
|**Phase 2: Encrypt Magic**|Encrypt "OrpheanBeholderScryDoubt" 64x|Final 192-bit hash|

## **Output Format (Memorize: $2b$10$...)**

text

`$2b$10$//DXiVVE59p7G5k/4Klx/ezF7BI42QZKmoOD0NDvUuqxRE5bFFBLy │  │ │ │  │ └─ 22-char hash (184-bit) │  └─── 22-char salt └──── Cost (2^10 = 1024 rounds)`

## **Cost Factor Scaling**

|Cost|Rounds|Time (i7 CPU)|
|---|---|---|
|10|2¹⁰|65ms|
|12|2¹²|**254ms** ⭐|
|14|2¹⁴|1s|
|16|2¹⁶|4s|

---

## **Interview Checklist – bCrypt Mastery**

**✅ Algorithm Design**

-  Blowfish → eksblowfish → expensive key schedule
    
-  Auto-salt (22 chars embedded) + adaptive cost
    
-  `$2b$cost$salt$hash` format parsing
    

**✅ Production Tuning**

-  **Cost=12** (250ms) → balance UX/security
    
-  Yearly cost+1 migration (Moore's Law)
    
-  `bcrypt.compare()` extracts salt automatically
    

**✅ Implementation**

text

`✅ bcrypt.hash(password, 12) // Auto salt+hash ✅ bcrypt.compare(input, storedHash) // Timing-safe ❌ Manual SHA256(password + salt) // Fast + broken`

**✅ Security Posture**

-  GPU-resistant (CPU-bound key setup)
    
-  Rainbow table immune (unique salts)
    
-  Future-proof (increase cost over time)
    

---

## **60-Second Recap – "Must-Say" Bullets**

text

`"bCrypt = Blowfish-derived slow KDF (eksblowfish key schedule) Format: $2b$12$salt(22)$hash(31) → auto-salt extraction Cost=12(250ms) → UX/security balance, yearly cost+1 Flow: hash(password,12) → compare(input,stored) Security: GPU-resistant + rainbow-proof + adaptive vs SHA256: NEVER (too fast for GPUs) vs Argon2: Similar, but bCrypt more mature ecosystem"`

**Reference**: [Auth0 - Hashing in Action: Understanding bcrypt](https://auth0.com/blog/hashing-in-action-understanding-bcrypt/)[auth0](https://auth0.com/blog/hashing-in-action-understanding-bcrypt/)​

**Architect gold: cost tuning, migration strategy, production timing targets.** 🚀

1. [https://auth0.com/blog/hashing-in-action-understanding-bcrypt/](https://auth0.com/blog/hashing-in-action-understanding-bcrypt/)
2. [https://auth0.com/blog/hashing-in-action-understanding-bcrypt/](https://auth0.com/blog/hashing-in-action-understanding-bcrypt/)
3. [https://www.topcoder.com/thrive/articles/bcrypt-algorithm](https://www.topcoder.com/thrive/articles/bcrypt-algorithm)
4. [https://en.wikipedia.org/wiki/Bcrypt](https://en.wikipedia.org/wiki/Bcrypt)
5. [https://docs.pega.com/bundle/pega-process-fabric-313/page/platform/security/bcrypt-hashing-algorithm-for-password-property-types.html](https://docs.pega.com/bundle/pega-process-fabric-313/page/platform/security/bcrypt-hashing-algorithm-for-password-property-types.html)
6. [https://dev.to/itsvinayak/the-bcrypt-algorithm-for-secure-password-hashing-44b3](https://dev.to/itsvinayak/the-bcrypt-algorithm-for-secure-password-hashing-44b3)
7. [https://jumpcloud.com/it-index/what-is-bcrypt](https://jumpcloud.com/it-index/what-is-bcrypt)
8. [https://kinde.com/learn/authentication/passwords/bcrypt-hashing-guide/](https://kinde.com/learn/authentication/passwords/bcrypt-hashing-guide/)
9. [https://www.geeksforgeeks.org/node-js/npm-bcrypt/](https://www.geeksforgeeks.org/node-js/npm-bcrypt/)
10. [https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
