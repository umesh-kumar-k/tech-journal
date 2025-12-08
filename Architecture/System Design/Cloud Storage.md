
## Cloud Storage Types Interview Checklist

- **Core Storage Types**
    
    |Type|Structure|Access Method|Best For|
    |---|---|---|---|
    |**Block**|Fixed-size blocks|Block device (iSCSI)|Databases, VMs, low-latency|
    |**File**|Hierarchical (folders/files)|File path (NFS/SMB)|Shared files, collaboration|
    |**Object**|Flat namespace + metadata|REST API (HTTP)|Unstructured data, scale|
    
- **Access & Performance**
    
    |Aspect|Block|File|Object|
    |---|---|---|---|
    |**Latency**|Lowest|Low|Higher|
    |**IOPS**|Highest|Medium|Lowest|
    |**Concurrency**|Low|High|Very High|
    |**Scalability**|Vertical|Moderate|Horizontal (exabyte+)|
    
- **Data Structure Fit**
    
    |Data Type|Block|File|Object|
    |---|---|---|---|
    |**Structured**|✅ DBs|✅ Spreadsheets|❌|
    |**Documents**|❌|✅ Word/Excel|✅|
    |**Media**|❌|✅ Shared folders|✅ Videos/Images|
    |**Archives**|❌|❌|✅ Backups/Logs|
    
- **Use Case Mapping**
    
    |Workload|Recommended|
    |---|---|
    |**Databases**|Block (EBS, Persistent Disk)|
    |**File Sharing**|File (EFS, FSx, NetApp)|
    |**Big Data/ML**|Object (S3, GCS, Azure Blob)|
    |**VMs/Containers**|Block|
    
- **Cloud Examples**
    
    |Provider|Block|File|Object|
    |---|---|---|---|
    |**AWS**|EBS|EFS, FSx|S3|
    |**GCP**|Persistent Disk|Filestore|Cloud Storage|
    |**Azure**|Managed Disks|Files|Blob Storage|
    |**IBM**|Block Storage|File Storage|Cloud Object Storage|
    
- **Hybrid Strategies**
    
    - **Databases:** Block for data + Object for backups/logs.
        
    - **Analytics:** Object (S3) → File (EFS) → Compute.
        
    - **Tiering:** Hot data (Block/File) → Cold (Object).
        

## 60-Second Recap

- **Block:** Low-latency, structured (DBs/VMs) → EBS/Persistent Disk.
    
- **File:** Hierarchical sharing (NFS/SMB) → EFS/Filestore.
    
- **Object:** Massive scale, unstructured (S3/Blob) → API access.
    
- **Choose:** Latency/IOPS (Block) → Collaboration (File) → Scale (Object).
    
- **Gold:** Hybrid—Block (active) + Object (archive) + auto-tiering.
    

**Reference**: [Object vs File vs Block Storage](https://www.ibm.com/think/topics/object-vs-file-vs-block-storage)[ibm](https://www.ibm.com/think/topics/object-vs-file-vs-block-storage)​

1. [https://www.ibm.com/think/topics/object-vs-file-vs-block-storage](https://www.ibm.com/think/topics/object-vs-file-vs-block-storage)
# **Object vs File vs Block Storage - Quick Reference**

**Source:** IBM Think, "Object vs File vs Block Storage"  
**URL:** https://www.ibm.com/think/topics/object-vs-file-vs-block-storage

## **1. Essential Question**
*What are the key differences between object, file, and block storage, and when should you choose each in system design?*

---

## **2. Main Ideas & Key Details**

### **A. Block Storage (The Foundation)**
#### **What it is:**
- **Raw storage volumes** that act like physical hard drives
- **Divided into blocks** (512 bytes to 4KB typically)
- **OS manages** file system on top
- **Example:** SAN, iSCSI, AWS EBS, Azure Disks

#### **Key Characteristics:**
- **Fast random access** (low latency)
- **Mountable** like physical disks
- **File system** handled by OS/application
- **Good for:** Databases, VM disks, transactional systems

#### **How it works:**
- **Storage array** presents blocks to servers
- **Server OS** creates file system on blocks
- **Direct addressing** by block number
- **No inherent structure** - just raw bytes

#### **Pros:**
- ✓ **High performance** (low latency)
- ✓ **Flexible** (any file system)
- ✓ **Transactional** support (ACID)
- ✓ **Standard interface** (SCSI, iSCSI)

#### **Cons:**
- ✗ **Limited scalability** (per volume)
- ✗ **No metadata** (OS manages)
- ✗ **Complex management** (RAID, LUNs)
- ✗ **Expensive** per GB

### **B. File Storage (The Organized)**
#### **What it is:**
- **Hierarchical storage** with files and folders
- **Shared access** via network protocols
- **Example:** NAS, NFS, SMB/CIFS, AWS EFS

#### **Key Characteristics:**
- **Directory structure** (folders/subfolders)
- **File-level operations** (open, read, write, close)
- **Network accessible** to multiple clients
- **Good for:** Shared documents, home directories, media

#### **How it works:**
- **Storage device** has built-in file system
- **Clients access** via network protocols
- **File locking** for concurrency control
- **Permissions** at file/directory level

#### **Protocols:**
- **NFS:** Unix/Linux (Network File System)
- **SMB/CIFS:** Windows (Server Message Block)
- **AFP:** Apple (Apple Filing Protocol)

#### **Pros:**
- ✓ **Easy to use** (familiar file/folder structure)
- ✓ **Shared access** (multiple clients simultaneously)
- ✓ **Built-in security** (permissions, ACLs)
- ✓ **Good for collaboration**

#### **Cons:**
- ✗ **Performance limitations** (network overhead)
- ✗ **Scalability limits** (directory size, file count)
- ✗ **Location dependent** (path matters)
- ✗ **Metadata limited** (basic attributes only)

### **C. Object Storage (The Scalable)**
#### **What it is:**
- **Flat namespace** of objects (no hierarchy)
- **Rich metadata** associated with each object
- **HTTP/REST API** access
- **Example:** AWS S3, Google Cloud Storage, Azure Blob

#### **Key Characteristics:**
- **Objects** = Data + Metadata + Unique ID
- **RESTful API** (GET, PUT, DELETE)
- **Massively scalable** (exabytes possible)
- **Good for:** Unstructured data, backups, archives, web content

#### **How it works:**
- **Objects stored** in flat address space
- **Accessed via** unique identifier (not path)
- **Metadata stored** with object (custom key-value pairs)
- **Immutable** (write once, read many)

#### **Object Structure:**
1. **Data:** Actual content (any format, any size)
2. **Metadata:** Custom attributes (key-value pairs)
3. **Globally Unique ID:** Object identifier

#### **Pros:**
- ✓ **Massive scalability** (billions of objects)
- ✓ **Rich metadata** (custom attributes)
- ✓ **Cost-effective** (cheap per GB)
- ✓ **Durable** (11 9's typical)
- ✓ **HTTP accessible** (web-native)

#### **Cons:**
- ✗ **Not for transactional** data
- ✗ **No file locking** (eventual consistency)
- ✗ **Not mountable** as drive
- ✗ **Higher latency** for updates

---

## **3. Summary / Synthesis**
**Block storage** = Raw disks for performance (databases, VMs).  
**File storage** = Organized shares for collaboration (documents, media).  
**Object storage** = Scalable buckets for unstructured data (backups, web content).

Choose based on: **Performance needs, access patterns, scalability requirements**.

---

## **4. Key Terms**
- **SAN:** Storage Area Network (block)
- **NAS:** Network Attached Storage (file)
- **LUN:** Logical Unit Number (block volume)
- **Object ID:** Unique identifier for objects
- **Metadata:** Descriptive data about data
- **IOPS:** Input/Output Operations Per Second

---

## **5. Comparison Matrix**

| **Aspect** | **Block Storage** | **File Storage** | **Object Storage** |
|------------|------------------|-----------------|-------------------|
| **Access Method** | Block device (SCSI) | File protocols (NFS/SMB) | REST API (HTTP) |
| **Data Organization** | Raw blocks | Files/folders hierarchy | Flat namespace |
| **Scalability** | Limited (TB-PB) | Medium (PB) | Massive (EB) |
| **Performance** | Very high (low latency) | Medium (network overhead) | High throughput |
| **Metadata** | Minimal (OS managed) | Basic (name, size, date) | Rich (custom key-value) |
| **Cost** | Highest per GB | Medium | Lowest per GB |
| **Best For** | Databases, VMs | Shared files, home dirs | Backups, web content |

---

## **6. Decision Framework**

#### **Choose Block Storage When:**
1. **Need low latency** (database transactions)
2. **Running traditional apps** that expect disk
3. **Need random read/write** access patterns
4. **Using VMs or containers** that need volumes
5. **Example:** MySQL database, VMware VMs, Docker volumes

#### **Choose File Storage When:**
1. **Multiple users** need shared access
2. **Working with traditional** file/folder structures
3. **Need file locking** and permissions
4. **Using apps that expect** file system paths
5. **Example:** Team document sharing, home directories, media editing

#### **Choose Object Storage When:**
1. **Storing unstructured data** (images, videos, logs)
2. **Need massive scalability** (billions of files)
3. **Access via web/API** needed
4. **Cost efficiency** important
5. **Example:** Website assets, backup archives, IoT data, big data lakes

---

## **7. Hybrid & Modern Approaches**

#### **1. File on Block:**
- **Traditional approach:** OS creates file system on block storage
- **Example:** Ext4 on AWS EBS volume
- **Benefit:** Performance of block + organization of files

#### **2. Object with File Interface:**
- **S3FS, s3ql:** Mount S3 as file system
- **Azure Files on Blob:** File interface on object storage
- **Trade-off:** Convenience vs performance

#### **3. Modern File Systems:**
- **Ceph:** Unified block/file/object storage
- **GlusterFS:** Distributed file system
- **WeedFS/SeaweedFS:** Optimized for object storage with file interface

#### **4. Cloud Native:**
- **Containers:** Use CSI (Container Storage Interface) drivers
- **Serverless:** Primarily object storage (event-driven)
- **Hybrid cloud:** Sync between on-prem and cloud storage

---

## **8. Interview Quick Answers**

**Q: "When would you choose object over file storage?"**
```
"Choose object storage when:
1. Storing massive amounts of unstructured data
2. Need HTTP/REST API access
3. Cost efficiency is critical (cheaper per GB)
4. Don't need file locking or POSIX compliance
5. Rich metadata needed for search/filtering

Example: User uploads in web app → S3, not NAS"
```

**Q: "Why use block storage for databases?"**
```
"Databases need block storage because:
1. Low latency random access (high IOPS)
2. Direct control over storage layout
3. Transaction support (ACID compliance)
4. Predictable performance
5. Ability to implement own caching/buffering

File/object storage adds too much overhead"
```

**Q: "How do you decide between the three?"**
```
"Decision tree:
1. Is it a database or VM? → Block
2. Multiple users sharing files? → File
3. Massive scale, web access? → Object
4. Need POSIX compliance? → File
5. Need lowest cost per GB? → Object
6. Need highest performance? → Block

Often use multiple types in one system"
```

**Q: "What about metadata differences?"**
```
"Metadata capabilities vary:
1. Block: Minimal (OS managed - size, location)
2. File: Basic (name, size, dates, permissions)
3. Object: Rich (custom key-value pairs, e.g., 
   'uploader': 'user123', 'content-type': 'image/jpeg')

Object metadata enables smart applications without databases"
```

---

## **9. Real-World Scenarios**

#### **Scenario 1: E-commerce Platform**
- **Product images:** Object storage (S3) - massive, web-accessible
- **Database:** Block storage (EBS) - transactional, high IOPS
- **Log files:** Object storage - write-once, analyze later
- **Backup files:** Object storage - cheap, durable

#### **Scenario 2: Video Streaming Service**
- **Video files:** Object storage - massive files, CDN origin
- **Metadata DB:** Block storage - fast queries
- **Transcoded files:** Object storage - different quality versions
- **User uploads:** Object storage - scale unpredictably

#### **Scenario 3: Enterprise Document Management**
- **Shared drives:** File storage (NAS) - permissions, locking
- **Archives:** Object storage - cheaper for cold data
- **Database:** Block storage - user permissions, search index
- **Backups:** Object storage - versioning, retention policies

---

## **10. Cloud Provider Examples**
- **AWS:** EBS (block), EFS (file), S3 (object)
- **Azure:** Disks (block), Files (file), Blob (object)
- **Google Cloud:** Persistent Disk (block), Filestore (file), Cloud Storage (object)
- **IBM Cloud:** Block Storage (block), File Storage (file), Cloud Object Storage (object)

---

## **11. Before Interview Checklist**
✅ Know primary use cases for each type  
✅ Understand access method differences  
✅ Remember scalability characteristics  
✅ Know cost trade-offs  
✅ Have hybrid architecture examples ready  
✅ Understand metadata capabilities

---

## **12. 60-Second Recap**
1. **Block:** Raw disks → Databases, VMs (fast, expensive)
2. **File:** Folders/files → Shared docs (familiar, medium scale)
3. **Object:** Flat buckets → Web content (massive scale, cheap)
4. **Choose based on:** Access pattern, scale, cost, performance
5. **Modern systems use all three** for different purposes
6. **Cloud native favors object** with API access

**Remember:** In interviews, focus on **trade-offs** not just features. Ask "What problem are we solving?" to guide storage choice.

*This quick reference covers storage type essentials for system design interviews.*