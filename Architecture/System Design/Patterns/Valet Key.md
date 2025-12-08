## Valet Key Pattern

Valet Key pattern issues time/scope-limited tokens granting clients direct resource access (storage, queues), offloading data transfer from application servers to reduce costs/scaling needs.[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/valet-key)​

- App authenticates client → generates restricted token (specific blob/container, read/write, expiry)
    
- Client uploads/downloads directly to storage; app avoids data proxying bandwidth/CPU
    
- Tokens revocable, auditable; short TTL + granular perms minimize security risk[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/valet-key)​
    

## Tools and Frameworks

|Platform|Tools/Frameworks|Example Use Case|
|---|---|---|
|Azure Storage|Shared Access Signatures (SAS), User Delegation SAS|Time-limited blob upload tokens via Function|
|AWS S3|Pre-Signed URLs|Browser direct upload to bucket|
|Google Cloud Storage|Signed URLs|Mobile app photo uploads|
|Service Bus|SAS Tokens|Queue write access for event producers|
|MinIO/S3-compatible|STS AssumeRole + presigned URLs|Self-hosted object storage|

## Interview Checklist

- Define: App-issued limited-scope token → direct client-to-storage access[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/valet-key)​
    
- Benefits: Offload bandwidth/CPU; scale-to-zero apps; cost savings on data transfer
    
- Security: Short TTL (minutes), granular perms (create-only), HTTPS, revocable
    
- Workflow: Auth client → validate → generate SAS → client direct upload → audit logs
    
- Gotchas: No size limits, validate post-upload, log all token usage, CORS for browsers
    
- Monitoring: Token issuance vs usage correlation; storage logs for access patterns
    
- When: Frequent/large file uploads/downloads; avoid if app must transform/validate data
    
- Alternatives: Proxied uploads (Gateway), standing credentials (insecure)
    

## 60-Second Recap

- **Core**: App → limited SAS token → client direct storage access[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/valet-key)​
    
- **Why**: Offload data transfer; save compute/bandwidth costs; scale better
    
- **Security**: Short expiry, granular perms (create-only), audit token usage
    
- **Tools**: Azure SAS, AWS presigned URLs, GCS signed URLs
    
- **Ex**: Function issues 3min upload token → mobile uploads 100MB photo directly
    

**Reference**: [https://learn.microsoft.com/en-us/azure/architecture/patterns/valet-key](https://learn.microsoft.com/en-us/azure/architecture/patterns/valet-key)[learn.microsoft](https://learn.microsoft.com/en-us/azure/architecture/patterns/valet-key)​

1. [https://learn.microsoft.com/en-us/azure/architecture/patterns/valet-key](https://learn.microsoft.com/en-us/azure/architecture/patterns/valet-key)

# **Valet Key Pattern**

## **Core Thesis**
The Valet Key pattern uses tokens or keys that provide clients with restricted, time-limited direct access to specific resources or services, reducing load on the application and improving scalability by delegating data transfer directly between clients and storage services.

---

## **Detailed Notes**

### **1. Problem Statement & Context**

**Traditional Application Bottlenecks:**
1. **Proxy Overload:** Application acts as middleman for all data transfers
2. **Bandwidth Costs:** Data flows through app server unnecessarily
3. **Scalability Limits:** Application becomes bottleneck for large files
4. **Complexity:** Application handles authentication and authorization for storage

**Common Scenarios:**
- Uploading/downloading large files (images, videos, documents)
- Client-side data processing requiring direct storage access
- Mobile apps needing to transfer data efficiently
- Web applications with user-generated content

### **2. Core Architecture**

**Traditional vs Valet Key Approach:**
```
TRADITIONAL (Proxy):             VALET KEY (Direct):
┌─────────────┐                  ┌─────────────┐
│   Client    │                  │   Client    │
└──────┬──────┘                  └──────┬──────┘
       │                                │ (1) Request access
       ▼                                ▼
┌─────────────┐                  ┌─────────────┐
│ Application │                  │ Application │
│    Server   │                  │    Server   │
└──────┬──────┘                  └──────┬──────┘
       │                                │ (2) Generate valet key
       ▼                                ▼
┌─────────────┐                  ┌─────────────┐
│   Storage   │                  │   Storage   │
│   Service   │                  │   Service   │
└─────────────┘                  └─────────────┘
       │                                │ (3) Client uses key directly
       │                                ▼
       │                        ┌─────────────┐
       └────────────────────────┤   Storage   │
                                │   Service   │
                                └─────────────┘
```

**Valet Key Flow:**
```
1. Client → App: "I need to upload a file"
2. App → Client: "Here's a key for container/file1, valid 5 min"
3. Client → Storage: Uses key directly to upload
4. Storage → Client: Success/failure (App not involved)
```

### **3. Implementation Examples**

**JavaScript/Node.js Azure Blob Storage SAS Token:**
```javascript
// Valet Key Service for Azure Blob Storage
const { BlobServiceClient, generateBlobSASQueryParameters, StorageSharedKeyCredential } = require('@azure/storage-blob');
const { DefaultAzureCredential } = require('@azure/identity');

class ValetKeyService {
  constructor() {
    this.accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
    this.accountKey = process.env.AZURE_STORAGE_ACCOUNT_KEY;
    
    // Initialize blob service client
    this.blobServiceClient = BlobServiceClient.fromConnectionString(
      process.env.AZURE_STORAGE_CONNECTION_STRING
    );
  }

  /**
   * Generate a read-only SAS token for downloading
   */
  async generateReadKey(containerName, blobName, userId, expiresInMinutes = 5) {
    const containerClient = this.blobServiceClient.getContainerClient(containerName);
    const blobClient = containerClient.getBlobClient(blobName);
    
    const startsOn = new Date();
    const expiresOn = new Date(startsOn.getTime() + expiresInMinutes * 60000);
    
    // Create SAS token with read permissions
    const sasToken = generateBlobSASQueryParameters(
      {
        containerName,
        blobName,
        permissions: 'r', // Read only
        startsOn,
        expiresOn,
        contentDisposition: `attachment; filename="${blobName}"`, // Force download
        contentType: this.getContentType(blobName)
      },
      new StorageSharedKeyCredential(this.accountName, this.accountKey)
    ).toString();
    
    // Return URL with SAS token
    const sasUrl = `${blobClient.url}?${sasToken}`;
    
    // Log the key issuance (for audit trail)
    await this.logKeyIssuance({
      userId,
      containerName,
      blobName,
      permission: 'read',
      expiresOn,
      sasUrl: sasUrl.split('?')[0] // Store without token for security
    });
    
    return {
      sasUrl,
      expiresAt: expiresOn.toISOString(),
      blobName
    };
  }

  /**
   * Generate a write-only SAS token for uploading
   */
  async generateWriteKey(containerName, blobName, userId, metadata = {}, expiresInMinutes = 10) {
    const containerClient = this.blobServiceClient.getContainerClient(containerName);
    
    // Ensure container exists
    await containerClient.createIfNotExists({ access: 'blob' });
    
    const blobClient = containerClient.getBlobClient(blobName);
    
    const startsOn = new Date();
    const expiresOn = new Date(startsOn.getTime() + expiresInMinutes * 60000);
    
    // Create SAS token with write permissions
    const sasToken = generateBlobSASQueryParameters(
      {
        containerName,
        blobName,
        permissions: 'cw', // Create and write
        startsOn,
        expiresOn,
        contentType: metadata.contentType || 'application/octet-stream',
        // Optional: Add IP restrictions
        // ipRange: { start: '192.168.0.1', end: '192.168.0.254' }
      },
      new StorageSharedKeyCredential(this.accountName, this.accountKey)
    ).toString();
    
    // Construct the URL for upload
    const sasUrl = `${blobClient.url}?${sasToken}`;
    
    // Store metadata about the upcoming upload
    await this.storeUploadMetadata({
      userId,
      containerName,
      blobName,
      expectedSize: metadata.expectedSize,
      contentType: metadata.contentType,
      expiresOn,
      status: 'pending'
    });
    
    return {
      sasUrl,
      expiresAt: expiresOn.toISOString(),
      blobName,
      uploadUrl: sasUrl // Alias for clarity
    };
  }

  /**
   * Generate a SAS token for list operations
   */
  async generateListKey(containerName, userId, prefix = '', expiresInMinutes = 2) {
    const containerClient = this.blobServiceClient.getContainerClient(containerName);
    
    const startsOn = new Date();
    const expiresOn = new Date(startsOn.getTime() + expiresInMinutes * 60000);
    
    // Generate container-level SAS for listing
    const sasToken = generateBlobSASQueryParameters(
      {
        containerName,
        permissions: 'l', // List only
        startsOn,
        expiresOn
      },
      new StorageSharedKeyCredential(this.accountName, this.accountKey)
    ).toString();
    
    const sasUrl = `${containerClient.url}?${sasToken}&restype=container&comp=list`;
    
    if (prefix) {
      sasUrl += `&prefix=${encodeURIComponent(prefix)}`;
    }
    
    return {
      sasUrl,
      expiresAt: expiresOn.toISOString(),
      permission: 'list'
    };
  }

  /**
   * Generate a delete key (for specific blob deletion)
   */
  async generateDeleteKey(containerName, blobName, userId, expiresInMinutes = 1) {
    const containerClient = this.blobServiceClient.getContainerClient(containerName);
    const blobClient = containerClient.getBlobClient(blobName);
    
    const startsOn = new Date();
    const expiresOn = new Date(startsOn.getTime() + expiresInMinutes * 60000);
    
    const sasToken = generateBlobSASQueryParameters(
      {
        containerName,
        blobName,
        permissions: 'd', // Delete only
        startsOn,
        expiresOn
      },
      new StorageSharedKeyCredential(this.accountName, this.accountKey)
    ).toString();
    
    return {
      sasUrl: `${blobClient.url}?${sasToken}`,
      expiresAt: expiresOn.toISOString(),
      permission: 'delete'
    };
  }

  /**
   * Generate a key with custom permissions
   */
  async generateCustomKey(containerName, blobName, permissions, userId, options = {}) {
    // permissions: string like 'racwd' (read, add, create, write, delete)
    // options: { startsOn, expiresOn, ipRange, protocol, contentType, etc. }
    
    const containerClient = this.blobServiceClient.getContainerClient(containerName);
    const blobClient = containerClient.getBlobClient(blobName);
    
    const startsOn = options.startsOn || new Date();
    const expiresOn = options.expiresOn || 
      new Date(startsOn.getTime() + (options.expiresInMinutes || 5) * 60000);
    
    const sasOptions = {
      containerName,
      blobName,
      permissions: permissions || 'r',
      startsOn,
      expiresOn,
      ...options
    };
    
    const sasToken = generateBlobSASQueryParameters(
      sasOptions,
      new StorageSharedKeyCredential(this.accountName, this.accountKey)
    ).toString();
    
    return {
      sasUrl: `${blobClient.url}?${sasToken}`,
      expiresAt: expiresOn.toISOString(),
      permissions,
      blobName
    };
  }

  /**
   * Client-side upload using valet key
   */
  async clientSideUpload(sasUrl, file, progressCallback = null) {
    // Client-side implementation (would run in browser)
    // This is example code showing the client perspective
    
    const formData = new FormData();
    formData.append('file', file);
    
    const xhr = new XMLHttpRequest();
    
    if (progressCallback) {
      xhr.upload.addEventListener('progress', (event) => {
        if (event.lengthComputable) {
          const percentComplete = (event.loaded / event.total) * 100;
          progressCallback(percentComplete);
        }
      });
    }
    
    return new Promise((resolve, reject) => {
      xhr.onload = () => {
        if (xhr.status >= 200 && xhr.status < 300) {
          resolve(xhr.response);
        } else {
          reject(new Error(`Upload failed: ${xhr.statusText}`));
        }
      };
      
      xhr.onerror = () => reject(new Error('Upload failed'));
      
      // Use the SAS URL directly (no authentication headers needed)
      xhr.open('PUT', sasUrl);
      xhr.setRequestHeader('x-ms-blob-type', 'BlockBlob');
      xhr.setRequestHeader('Content-Type', file.type);
      xhr.send(file);
    });
  }

  /**
   * Client-side download using valet key
   */
  async clientSideDownload(sasUrl, fileName = null) {
    // Client-side implementation
    const response = await fetch(sasUrl);
    
    if (!response.ok) {
      throw new Error(`Download failed: ${response.statusText}`);
    }
    
    const blob = await response.blob();
    
    // Create download link
    const url = window.URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = fileName || 'download';
    document.body.appendChild(a);
    a.click();
    window.URL.revokeObjectURL(url);
    document.body.removeChild(a);
  }

  /**
   * Validate and revoke a SAS token (by deleting the blob or changing container policy)
   */
  async revokeKey(containerName, blobName) {
    // Note: SAS tokens cannot be individually revoked once issued
    // But we can delete the blob or change container policy
    
    const containerClient = this.blobServiceClient.getContainerClient(containerName);
    const blobClient = containerClient.getBlobClient(blobName);
    
    // Option 1: Delete the blob
    await blobClient.deleteIfExists();
    
    // Option 2: Store in blocklist (for custom validation)
    await this.blockToken(blobName);
    
    return { revoked: true, blobName };
  }

  /**
   * Audit logging
   */
  async logKeyIssuance(logEntry) {
    // Store in database or logging service
    console.log('Valet key issued:', {
      ...logEntry,
      timestamp: new Date().toISOString()
    });
    
    // Example: Store in Cosmos DB for audit trail
    // await auditLogCollection.insertOne(logEntry);
  }

  async storeUploadMetadata(metadata) {
    // Store metadata about expected uploads
    // Useful for validation and monitoring
    console.log('Upload metadata stored:', metadata);
  }

  /**
   * Helper methods
   */
  getContentType(filename) {
    const ext = filename.split('.').pop().toLowerCase();
    const types = {
      'jpg': 'image/jpeg',
      'jpeg': 'image/jpeg',
      'png': 'image/png',
      'gif': 'image/gif',
      'pdf': 'application/pdf',
      'txt': 'text/plain',
      'mp4': 'video/mp4',
      'mp3': 'audio/mpeg'
    };
    return types[ext] || 'application/octet-stream';
  }
}

// API Endpoints using Express.js
const express = require('express');
const router = express.Router();
const valetKeyService = new ValetKeyService();

// Generate upload key
router.post('/upload-key', authenticate, async (req, res) => {
  try {
    const { container = 'uploads', filename, contentType, expectedSize } = req.body;
    const userId = req.user.id;
    
    // Validate request
    if (!filename) {
      return res.status(400).json({ error: 'Filename is required' });
    }
    
    // Generate unique blob name
    const blobName = `${userId}/${Date.now()}-${filename}`;
    
    // Generate write key
    const key = await valetKeyService.generateWriteKey(
      container,
      blobName,
      userId,
      { contentType, expectedSize },
      10 // 10 minutes expiry
    );
    
    res.json({
      success: true,
      key,
      blobName,
      message: 'Upload key generated successfully'
    });
    
  } catch (error) {
    console.error('Error generating upload key:', error);
    res.status(500).json({ error: 'Failed to generate upload key' });
  }
});

// Generate download key
router.get('/download-key/:blobName', authenticate, async (req, res) => {
  try {
    const { blobName } = req.params;
    const { container = 'uploads' } = req.query;
    const userId = req.user.id;
    
    // Check permissions (user can only download their own files)
    if (!blobName.startsWith(`${userId}/`)) {
      return res.status(403).json({ error: 'Access denied' });
    }
    
    const key = await valetKeyService.generateReadKey(
      container,
      blobName,
      userId,
      5 // 5 minutes expiry
    );
    
    res.json({
      success: true,
      key,
      message: 'Download key generated successfully'
    });
    
  } catch (error) {
    console.error('Error generating download key:', error);
    res.status(500).json({ error: 'Failed to generate download key' });
  }
});

// List user's files
router.get('/list-keys', authenticate, async (req, res) => {
  try {
    const userId = req.user.id;
    const { container = 'uploads', prefix = `${userId}/` } = req.query;
    
    // Generate list key
    const listKey = await valetKeyService.generateListKey(
      container,
      userId,
      prefix,
      2 // 2 minutes expiry (list operations are quick)
    );
    
    // Client will use this key to list files directly from storage
    res.json({
      success: true,
      listKey,
      instructions: 'Use the listKey.sasUrl to list blobs directly from Azure Storage'
    });
    
  } catch (error) {
    console.error('Error generating list key:', error);
    res.status(500).json({ error: 'Failed to generate list key' });
  }
});

module.exports = router;
```

**JavaScript Frontend Client Example:**
```javascript
// Frontend client using valet keys
class StorageClient {
  constructor(apiBaseUrl) {
    this.apiBaseUrl = apiBaseUrl;
  }
  
  /**
   * Upload a file using valet key pattern
   */
  async uploadFile(file, onProgress = null) {
    try {
      // 1. Request upload key from application
      const response = await fetch(`${this.apiBaseUrl}/upload-key`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
          'Authorization': `Bearer ${localStorage.getItem('token')}`
        },
        body: JSON.stringify({
          filename: file.name,
          contentType: file.type,
          expectedSize: file.size
        })
      });
      
      const { key, blobName } = await response.json();
      
      // 2. Upload directly to Azure Storage using the SAS URL
      const xhr = new XMLHttpRequest();
      
      if (onProgress) {
        xhr.upload.addEventListener('progress', (event) => {
          if (event.lengthComputable) {
            const percentComplete = (event.loaded / event.total) * 100;
            onProgress(percentComplete, 'uploading');
          }
        });
      }
      
      return new Promise((resolve, reject) => {
        xhr.onload = () => {
          if (xhr.status >= 200 && xhr.status < 300) {
            resolve({
              success: true,
              blobName,
              url: key.sasUrl.split('?')[0] // URL without SAS token
            });
          } else {
            reject(new Error(`Upload failed: ${xhr.statusText}`));
          }
        };
        
        xhr.onerror = () => reject(new Error('Upload failed'));
        
        // Use the SAS URL directly
        xhr.open('PUT', key.sasUrl);
        xhr.setRequestHeader('x-ms-blob-type', 'BlockBlob');
        xhr.setRequestHeader('Content-Type', file.type || 'application/octet-stream');
        xhr.setRequestHeader('x-ms-blob-content-disposition', `attachment; filename="${file.name}"`);
        xhr.send(file);
      });
      
    } catch (error) {
      console.error('Upload error:', error);
      throw error;
    }
  }
  
  /**
   * Download a file using valet key
   */
  async downloadFile(blobName, filename = null) {
    try {
      // 1. Request download key
      const response = await fetch(
        `${this.apiBaseUrl}/download-key/${encodeURIComponent(blobName)}`,
        {
          headers: {
            'Authorization': `Bearer ${localStorage.getItem('token')}`
          }
        }
      );
      
      const { key } = await response.json();
      
      // 2. Download directly from Azure Storage
      const downloadResponse = await fetch(key.sasUrl);
      
      if (!downloadResponse.ok) {
        throw new Error(`Download failed: ${downloadResponse.statusText}`);
      }
      
      const blob = await downloadResponse.blob();
      const url = window.URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = filename || blobName.split('/').pop() || 'download';
      document.body.appendChild(a);
      a.click();
      window.URL.revokeObjectURL(url);
      document.body.removeChild(a);
      
      return { success: true, blobName };
      
    } catch (error) {
      console.error('Download error:', error);
      throw error;
    }
  }
  
  /**
   * List files using valet key
   */
  async listFiles(prefix = '') {
    try {
      // 1. Request list key
      const response = await fetch(
        `${this.apiBaseUrl}/list-keys?prefix=${encodeURIComponent(prefix)}`,
        {
          headers: {
            'Authorization': `Bearer ${localStorage.getItem('token')}`
          }
        }
      );
      
      const { listKey } = await response.json();
      
      // 2. List directly from Azure Storage
      const listResponse = await fetch(listKey.sasUrl);
      const xmlText = await listResponse.text();
      
      // Parse XML response (Azure Storage returns XML for list operations)
      const parser = new DOMParser();
      const xmlDoc = parser.parseFromString(xmlText, 'text/xml');
      const blobs = xmlDoc.getElementsByTagName('Blob');
      
      const files = [];
      for (let blob of blobs) {
        const name = blob.getElementsByTagName('Name')[0]?.textContent;
        const url = blob.getElementsByTagName('Url')[0]?.textContent;
        
        if (name) {
          files.push({
            name,
            url,
            displayName: name.split('/').pop()
          });
        }
      }
      
      return files;
      
    } catch (error) {
      console.error('List error:', error);
      throw error;
    }
  }
}

// Usage example
const storageClient = new StorageClient('https://api.example.com/storage');

// Upload file
const fileInput = document.getElementById('fileInput');
fileInput.addEventListener('change', async (event) => {
  const file = event.target.files[0];
  
  try {
    await storageClient.uploadFile(file, (progress) => {
      console.log(`Upload progress: ${progress}%`);
    });
    
    console.log('File uploaded successfully!');
  } catch (error) {
    console.error('Upload failed:', error);
  }
});

// Download file
async function downloadUserFile(filename) {
  try {
    await storageClient.downloadFile(`user123/${filename}`);
    console.log('File downloaded successfully!');
  } catch (error) {
    console.error('Download failed:', error);
  }
}

// List files
async function listUserFiles() {
  try {
    const files = await storageClient.listFiles('user123/');
    console.log('User files:', files);
    return files;
  } catch (error) {
    console.error('List failed:', error);
    return [];
  }
}
```

**Java/Spring Boot Azure Implementation:**
```java
// Spring Boot Valet Key Service
@Service
public class AzureValetKeyService {
    
    @Value("${azure.storage.account-name}")
    private String accountName;
    
    @Value("${azure.storage.account-key}")
    private String accountKey;
    
    @Value("${azure.storage.container-name}")
    private String containerName;
    
    private BlobServiceClient blobServiceClient;
    
    @PostConstruct
    public void init() {
        String connectionString = String.format(
            "DefaultEndpointsProtocol=https;AccountName=%s;AccountKey=%s",
            accountName, accountKey
        );
        
        this.blobServiceClient = new BlobServiceClientBuilder()
            .connectionString(connectionString)
            .buildClient();
    }
    
    public SasTokenResponse generateReadSasToken(String blobName, User user) {
        BlobContainerClient containerClient = blobServiceClient.getBlobContainerClient(containerName);
        BlobClient blobClient = containerClient.getBlobClient(blobName);
        
        // Check if blob exists and user has permission
        if (!blobClient.exists()) {
            throw new ResourceNotFoundException("Blob not found: " + blobName);
        }
        
        // Generate SAS token
        OffsetDateTime expiresOn = OffsetDateTime.now().plusMinutes(5);
        
        BlobSasPermission permission = new BlobSasPermission()
            .setReadPermission(true);
        
        BlobServiceSasSignatureValues values = new BlobServiceSasSignatureValues(
            expiresOn, permission
        );
        
        String sasToken = blobClient.generateSas(values);
        String sasUrl = blobClient.getBlobUrl() + "?" + sasToken;
        
        // Log for audit
        auditLogService.logSasTokenIssued(user.getId(), blobName, "read", expiresOn);
        
        return new SasTokenResponse(sasUrl, expiresOn, "read");
    }
    
    public SasTokenResponse generateWriteSasToken(String blobName, User user, Map<String, String> metadata) {
        BlobContainerClient containerClient = blobServiceClient.getBlobContainerClient(containerName);
        
        // Create container if it doesn't exist
        if (!containerClient.exists()) {
            containerClient.create();
        }
        
        // Generate unique blob name if not provided
        if (blobName == null) {
            blobName = String.format("%s/%s-%s",
                user.getId(),
                UUID.randomUUID().toString(),
                metadata.getOrDefault("originalFilename", "file")
            );
        }
        
        BlobClient blobClient = containerClient.getBlobClient(blobName);
        
        OffsetDateTime expiresOn = OffsetDateTime.now().plusMinutes(10);
        
        BlobSasPermission permission = new BlobSasPermission()
            .setCreatePermission(true)
            .setWritePermission(true);
        
        BlobServiceSasSignatureValues values = new BlobServiceSasSignatureValues(
            expiresOn, permission
        );
        
        // Set content type if provided
        if (metadata.containsKey("contentType")) {
            values.setContentType(metadata.get("contentType"));
        }
        
        String sasToken = blobClient.generateSas(values);
        String sasUrl = blobClient.getBlobUrl() + "?" + sasToken;
        
        // Store upload metadata
        uploadMetadataService.storePendingUpload(
            user.getId(),
            blobName,
            metadata,
            expiresOn
        );
        
        return new SasTokenResponse(sasUrl, expiresOn, "write", blobName);
    }
    
    @Data
    @AllArgsConstructor
    public static class SasTokenResponse {
        private String sasUrl;
        private OffsetDateTime expiresAt;
        private String permission;
        private String blobName;
        
        public SasTokenResponse(String sasUrl, OffsetDateTime expiresAt, String permission) {
            this(sasUrl, expiresAt, permission, null);
        }
    }
}

// REST Controller
@RestController
@RequestMapping("/api/storage")
public class ValetKeyController {
    
    @Autowired
    private AzureValetKeyService valetKeyService;
    
    @PostMapping("/upload-token")
    public ResponseEntity<?> generateUploadToken(
            @RequestBody UploadTokenRequest request,
            @AuthenticationPrincipal User user) {
        
        try {
            SasTokenResponse token = valetKeyService.generateWriteSasToken(
                request.getBlobName(),
                user,
                request.getMetadata()
            );
            
            return ResponseEntity.ok(token);
            
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body(new ErrorResponse("Failed to generate upload token"));
        }
    }
    
    @GetMapping("/download-token/{blobName}")
    public ResponseEntity<?> generateDownloadToken(
            @PathVariable String blobName,
            @AuthenticationPrincipal User user) {
        
        try {
            // Validate user has access to this blob
            if (!blobName.startsWith(user.getId() + "/")) {
                return ResponseEntity.status(HttpStatus.FORBIDDEN)
                    .body(new ErrorResponse("Access denied"));
            }
            
            SasTokenResponse token = valetKeyService.generateReadSasToken(blobName, user);
            
            return ResponseEntity.ok(token);
            
        } catch (ResourceNotFoundException e) {
            return ResponseEntity.status(HttpStatus.NOT_FOUND)
                .body(new ErrorResponse("File not found"));
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body(new ErrorResponse("Failed to generate download token"));
        }
    }
}
```

### **4. Security Considerations**

**Token Security Implementation:**
```javascript
// Enhanced security for valet keys
class SecureValetKeyService extends ValetKeyService {
  
  async generateSecureWriteKey(containerName, blobName, userId, options = {}) {
    // Additional security measures
    
    const securityOptions = {
      // IP restriction
      ipRange: options.allowedIpRange || this.getUserIpRange(userId),
      
      // Protocol restriction (HTTPS only)
      protocol: 'https',
      
      // Signed identifier (for revocation)
      signedIdentifier: `user_${userId}_${Date.now()}`,
      
      // Custom headers that must be present
      requiredHeaders: {
        'x-ms-client-request-id': uuidv4(),
        'x-ms-meta-userid': userId
      },
      
      // Encryption scope (if using customer-managed keys)
      encryptionScope: options.encryptionScope,
      
      // CORS restrictions
      allowedOrigins: options.allowedOrigins || [this.getAllowedOrigin(userId)],
      
      // Cache control
      cacheControl: 'no-cache, no-store'
    };
    
    const sasOptions = {
      ...securityOptions,
      ...options,
      permissions: 'cw',
      startsOn: new Date(),
      expiresOn: new Date(Date.now() + (options.expiresInMinutes || 5) * 60000)
    };
    
    // Generate token with security restrictions
    const sasToken = generateBlobSASQueryParameters(
      sasOptions,
      this.getCredential()
    ).toString();
    
    // Store security context
    await this.storeSecurityContext({
      sasIdentifier: securityOptions.signedIdentifier,
      userId,
      restrictions: securityOptions,
      issuedAt: new Date(),
      status: 'active'
    });
    
    return {
      sasUrl: `${this.getBlobUrl(containerName, blobName)}?${sasToken}`,
      expiresAt: sasOptions.expiresOn.toISOString(),
      securityContext: securityOptions.signedIdentifier
    };
  }
  
  async validateUploadCompletion(blobName, expectedHash, userId) {
    // After client uploads, validate the file
    
    const blobClient = this.getBlobClient(blobName);
    
    // 1. Check if blob exists
    const exists = await blobClient.exists();
    if (!exists) {
      throw new Error('Upload failed - blob not found');
    }
    
    // 2. Get blob properties
    const properties = await blobClient.getProperties();
    
    // 3. Verify metadata
    if (properties.metadata?.userId !== userId) {
      await blobClient.delete(); // Clean up unauthorized upload
      throw new Error('Upload failed - user mismatch');
    }
    
    // 4. Verify hash if provided
    if (expectedHash) {
      // Calculate or retrieve hash and compare
      const actualHash = await this.calculateBlobHash(blobClient);
      if (actualHash !== expectedHash) {
        await blobClient.delete();
        throw new Error('Upload failed - hash mismatch');
      }
    }
    
    // 5. Update upload status
    await this.updateUploadStatus(blobName, 'completed');
    
    return {
      success: true,
      blobName,
      size: properties.contentLength,
      contentType: properties.contentType,
      uploadedAt: properties.lastModified
    };
  }
  
  async revokeAllUserKeys(userId) {
    // Revoke all active keys for a user
    const activeKeys = await this.getActiveUserKeys(userId);
    
    for (const key of activeKeys) {
      // For SAS tokens, we can't revoke directly, but we can:
      // 1. Delete the blob if it exists
      // 2. Block future access through our application
      // 3. Change container policy
      
      if (key.blobName) {
        await this.deleteBlobIfExists(key.containerName, key.blobName);
      }
      
      // Mark as revoked in our system
      await this.revokeKeyInSystem(key.tokenId);
    }
    
    return { revoked: activeKeys.length, userId };
  }
}
```

### **5. Benefits & Advantages**

**Performance Benefits:**
1. **Reduced Server Load:** Application doesn't handle large file transfers
2. **Improved Throughput:** Direct client-to-storage transfers are faster
3. **Better Scalability:** Storage services scale independently of application
4. **Lower Latency:** Eliminates application server hop

**Cost Benefits:**
- Reduced bandwidth costs for application servers
- Better utilization of storage service capabilities
- Lower compute costs (CPU/memory on app servers)
- Pay-as-you-go scaling with cloud storage

**Architectural Benefits:**
1. **Simplified Application:** No file handling logic in app
2. **Better Security:** Fine-grained, time-limited access control
3. **Client Flexibility:** Clients can use optimal transfer methods
4. **Auditability:** Clear access logs at storage level

### **6. Common Variations**

**Multi-Cloud Valet Key Service:**
```javascript
// Abstract valet key service for multiple cloud providers
class MultiCloudValetKeyService {
  constructor(config) {
    this.providers = {
      'azure': new AzureValetKeyService(config.azure),
      'aws': new AWSValetKeyService(config.aws),
      'gcp': new GCPValetKeyService(config.gcp)
    };
    
    this.defaultProvider = config.defaultProvider || 'azure';
  }
  
  async generateKey(provider, operation, params) {
    const service = this.providers[provider || this.defaultProvider];
    
    switch (operation) {
      case 'upload':
        return service.generateUploadKey(params);
      case 'download':
        return service.generateDownloadKey(params);
      case 'list':
        return service.generateListKey(params);
      case 'delete':
        return service.generateDeleteKey(params);
      default:
        throw new Error(`Unsupported operation: ${operation}`);
    }
  }
  
  // Provider-agnostic interface
  async uploadFile(file, userId, options = {}) {
    // Choose provider based on strategy
    const provider = this.chooseProvider(file, options);
    
    // Generate upload key
    const key = await this.generateKey(provider, 'upload', {
      userId,
      filename: file.name,
      contentType: file.type,
      size: file.size,
      ...options
    });
    
    // Return provider-specific instructions
    return {
      provider,
      key,
      uploadInstructions: this.getUploadInstructions(provider, key)
    };
  }
}

// AWS S3 Pre-signed URL implementation
class AWSValetKeyService {
  constructor(config) {
    this.s3 = new AWS.S3(config);
    this.bucketName = config.bucketName;
  }
  
  async generateUploadKey(params) {
    const { userId, filename, contentType, expiresIn = 300 } = params;
    
    const key = `uploads/${userId}/${Date.now()}-${filename}`;
    
    const params = {
      Bucket: this.bucketName,
      Key: key,
      Expires: expiresIn,
      ContentType: contentType,
      Metadata: {
        userId: userId,
        originalFilename: filename
      }
    };
    
    const url = await this.s3.getSignedUrlPromise('putObject', params);
    
    return {
      url,
      method: 'PUT',
      expiresAt: new Date(Date.now() + expiresIn * 1000).toISOString(),
      key,
      headers: {
        'Content-Type': contentType
      }
    };
  }
  
  async generateDownloadKey(params) {
    const { key, expiresIn = 300 } = params;
    
    const params = {
      Bucket: this.bucketName,
      Key: key,
      Expires: expiresIn,
      ResponseContentDisposition: `attachment; filename="${key.split('/').pop()}"`
    };
    
    const url = await this.s3.getSignedUrlPromise('getObject', params);
    
    return {
      url,
      method: 'GET',
      expiresAt: new Date(Date.now() + expiresIn * 1000).toISOString(),
      key
    };
  }
}

// GCP Signed URL implementation
class GCPValetKeyService {
  constructor(config) {
    this.storage = new Storage(config);
    this.bucketName = config.bucketName;
  }
  
  async generateUploadKey(params) {
    const { userId, filename, contentType, expiresIn = 300 } = params;
    
    const blobName = `uploads/${userId}/${Date.now()}-${filename}`;
    const bucket = this.storage.bucket(this.bucketName);
    const file = bucket.file(blobName);
    
    const options = {
      version: 'v4',
      action: 'write',
      expires: Date.now() + expiresIn * 1000,
      contentType: contentType
    };
    
    const [url] = await file.getSignedUrl(options);
    
    return {
      url,
      method: 'PUT',
      expiresAt: new Date(Date.now() + expiresIn * 1000).toISOString(),
      blobName,
      headers: {
        'Content-Type': contentType
      }
    };
  }
}
```

### **7. Best Practices & Guidelines**

**Security Best Practices:**
```javascript
class ValetKeySecurity {
  // 1. Principle of Least Privilege
  generateLeastPrivilegeKey(userId, operation, resource) {
    const permissions = {
      'upload': 'cw',  // Create + Write only
      'download': 'r', // Read only
      'delete': 'd',   // Delete only
      'list': 'l'      // List only
    };
    
    // Grant only necessary permissions
    return this.generateKey({
      permissions: permissions[operation],
      resource,
      userId,
      expiresIn: this.getDefaultExpiry(operation)
    });
  }
  
  // 2. Short Expiry Times
  getDefaultExpiry(operation) {
    const expiryTimes = {
      'upload': 10,    // 10 minutes for upload
      'download': 5,   // 5 minutes for download
      'delete': 1,     // 1 minute for delete
      'list': 2        // 2 minutes for list
    };
    
    return expiryTimes[operation] || 5;
  }
  
  // 3. IP Restrictions
  async generateIpRestrictedKey(userId, ipAddress) {
    return this.generateKey({
      userId,
      ipRange: {
        start: ipAddress,
        end: ipAddress  // Single IP restriction
      },
      expiresIn: 5
    });
  }
  
  // 4. Audit Logging
  async auditKeyUsage(keyId, operation, userId, resource, result) {
    await auditLogService.log({
      timestamp: new Date(),
      keyId,
      operation,
      userId,
      resource,
      result,
      ipAddress: this.getClientIp(),
      userAgent: this.getUserAgent()
    });
    
    // Alert on suspicious patterns
    this.detectSuspiciousActivity(userId, operation);
  }
  
  // 5. Key Rotation and Revocation
  async implementKeyRotation() {
    // Automatically rotate storage account keys
    const oldKey = this.getCurrentStorageKey();
    const newKey = await this.generateNewStorageKey();
    
    // Update application configuration
    await this.updateKeyInKeyVault(newKey);
    
    // Graceful transition period
    await this.allowBothKeysTemporarily(oldKey, newKey);
    
    // Revoke old key after transition
    await this.revokeStorageKey(oldKey);
  }
}
```

### **8. Common Pitfalls & Anti-Patterns**

**Security Mistakes:**
1. **Overly Permissive Tokens:** Granting more permissions than needed
2. **Long Expiry Times:** Tokens valid for days instead of minutes
3. **No IP Restrictions:** Allowing access from anywhere
4. **Missing Audit Logs:** No tracking of token usage

**Implementation Anti-Patterns:**
1. **Application Still Proxying:** Defeating the purpose of valet key
2. **No Size Limits:** Allowing unlimited uploads without validation
3. **Hardcoded Storage Credentials:** Storing keys in application code
4. **Ignoring CORS:** Not configuring CORS properly for web clients

**Performance Issues:**
- Generating tokens synchronously blocking requests
- Not caching token generation for repeated requests
- Creating new containers for every upload
- Not cleaning up expired or unused tokens

### **9. When to Use Valet Key Pattern**

**Good Fit For:**
1. **File Upload/Download:** Large media files, documents
2. **Mobile Applications:** Bandwidth-constrained environments
3. **Progressive Web Apps:** Client-side file handling
4. **Multi-tenant SaaS:** Isolating customer data
5. **CDN Integration:** Direct upload to CDN origins

**Poor Fit For:**
1. **Small API Responses:** No performance benefit
2. **Complex Business Logic:** Need application processing
3. **Real-time Processing:** Files need immediate server-side processing
4. **Legacy Client Support:** Clients can't handle direct storage access

### **10. Interview-Specific Considerations**

**Common Interview Questions:**
1. "How do you secure valet keys from misuse?"
2. "What's the difference between SAS tokens and pre-signed URLs?"
3. "How do you handle failed uploads with valet keys?"
4. "When would you choose valet key over direct application handling?"

**Design Discussion Points:**
- Security trade-offs and implementations
- Performance benchmarking
- Cost optimization strategies
- Multi-cloud considerations

---

## **Before-Interview Checklist**

**✅ Core Concept Understanding**
- [ ] Valet key analogy and purpose
- [ ] Difference from traditional proxy approach
- [ ] Security implications and trade-offs
- [ ] Performance benefits and limitations

**✅ Implementation Details**
- [ ] SAS tokens vs pre-signed URLs
- [ ] Permission models and restrictions
- [ ] Token generation and validation
- [ ] Client-side implementation patterns

**✅ Security Considerations**
- [ ] Principle of least privilege implementation
- [ ] Token expiration and revocation
- [ ] IP and protocol restrictions
- [ ] Audit logging and monitoring

**✅ Cloud Provider Implementations**
- [ ] Azure SAS tokens
- [ ] AWS pre-signed URLs
- [ ] GCP signed URLs
- [ ] Multi-cloud abstraction strategies

**✅ Performance Optimization**
- [ ] Token caching strategies
- [ ] Client-side upload optimization
- [ ] Monitoring and alerting
- [ ] Cost optimization techniques

---

## **60-Second Recap (Bullet Format)**

**Core Concept:**
- Provide clients with temporary, restricted access tokens
- Allow direct client-to-storage communication
- Eliminate application as middleman for data transfer

**Key Components:**
- **Valet Key/Token:** Time-limited, permission-scoped access credential
- **Storage Service:** Cloud storage (Azure Blob, AWS S3, etc.)
- **Application Server:** Issues and manages tokens
- **Client:** Uses token to access storage directly

**Benefits:**
- Reduced application server load
- Improved scalability and performance
- Lower bandwidth costs
- Fine-grained access control

**Common Implementations:**
- **Azure:** Shared Access Signatures (SAS tokens)
- **AWS:** Pre-signed URLs
- **Google Cloud:** Signed URLs
- **Custom:** JWT-based access tokens

**Security Best Practices:**
- Principle of least privilege
- Short expiration times (minutes, not days)
- IP and protocol restrictions
- Comprehensive audit logging

**When to Use:**
- Large file uploads/downloads
- Mobile applications with bandwidth constraints
- Client-side processing requirements
- Multi-tenant storage isolation

**When to Avoid:**
- Small API payloads
- Complex server-side processing required
- Legacy client limitations
- When application needs to inspect/modify data

**Common Use Cases:**
- User file uploads (photos, documents)
- Media streaming
- Backup and restore operations
- Data export functionality

**Monitoring & Management:**
- Token issuance logging
- Usage analytics and reporting
- Suspicious activity detection
- Automatic key rotation

---

## **Sources & References**

**Primary Source:**
- Microsoft Azure Architecture Center: Valet Key Pattern
- URL: https://learn.microsoft.com/en-us/azure/architecture/patterns/valet-key

**Additional References:**
- Azure Storage SAS documentation
- AWS S3 pre-signed URLs guide
- Google Cloud signed URLs documentation
- OAuth 2.0 token-based authentication

**Related Azure Patterns:**
- Gatekeeper Pattern
- Federated Identity Pattern
- Static Content Hosting Pattern
- CQRS Pattern

**Interview Citation Format:**
- "As described in Microsoft's Valet Key pattern documentation..."
- "Following the direct client access approach for storage operations..."
- "The pattern reduces application load by delegating data transfer as outlined in Azure's architecture guidance..."