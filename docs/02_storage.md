# Module 2: Azure Storage

## Key Rule (VERY IMPORTANT)

> **UNC paths are used ONLY with Azure Files.**  
> **All other Azure Storage services use URLs (HTTP/HTTPS).**

---

## Azure Files (UNC Path)

Azure Files behaves like a traditional network file share.

### UNC Path Format

\\<storage-account-name>.file.core.windows.net\<file-share-name>

### Example

\\contosostorage.file.core.windows.net\data

### Usage

- Windows VMs
- SMB protocol
- Mapping network drives
- Scripts and services

### Notes

- Uses **SMB**
- Requires authentication (storage key or AD-based auth)
- This is the **ONLY true UNC path in Azure Storage**

---

## Azure Files (URL Access)

Azure Files can also be accessed via HTTPS.

### Format

https://<storage-account-name>.file.core.windows.net/<file-share-name>

### Example

https://contosostorage.file.core.windows.net/data

---

## Azure Blob Storage (URL ONLY)

Blob storage **never uses UNC**.

### Container URL

https://<storage-account-name>.blob.core.windows.net/<container-name>

### Example

https://contosostorage.blob.core.windows.net/images

### Specific Blob

https://contosostorage.blob.core.windows.net/images/photo.jpg

### Notes

- Uses HTTP/HTTPS
- Not mountable as a file system via UNC
- Optimized for unstructured data

---

## Azure Data Lake Storage Gen2

Built on top of Blob Storage.

### Format

https://<storage-account-name>.dfs.core.windows.net/<filesystem-name>

### Example

https://contosostorage.dfs.core.windows.net/rawdata

---

## Azure Queue Storage (URL)

### Format

https://<storage-account-name>.queue.core.windows.net/<queue-name>

### Example

https://contosostorage.queue.core.windows.net/tasks

---

## Azure Table Storage (URL)

### Format

https://<storage-account-name>.table.core.windows.net/<table-name>

### Example

https://contosostorage.table.core.windows.net/users

---

## Linux Access to Azure Files (NFS / SMB)

Linux does **not** use UNC syntax.

### Format (NFS)

<storage-account-name>.file.core.windows.net:/<file-share-name>

### Example

contosostorage.file.core.windows.net:/data

---

## Quick Comparison Table

| Storage Service | UNC | URL | Endpoint |
|----------------|-----|-----|----------|
| Azure Files    | Yes | Yes | file.core.windows.net |
| Blob Storage   | No  | Yes | blob.core.windows.net |
| Data Lake Gen2 | No  | Yes | dfs.core.windows.net |
| Queue Storage  | No  | Yes | queue.core.windows.net |
| Table Storage  | No  | Yes | table.core.windows.net |

---

## Exam Memory Hooks

- **UNC = Azure Files**
- **\\ = File Share**
- **blob = HTTP/HTTPS only**
- **Storage account name is always first**
- **Subscription name is NEVER part of a path**

---

## Common Exam Traps

- Using UNC for Blob storage ❌
- Including subscription name in the path ❌
- Using portal.azure.com as a storage endpoint ❌
- Mixing blob and file endpoints ❌

---

## One-Line Exam Rule

> **If the path starts with `\\`, it must be Azure Files. Everything else is a URL.**
