# Module 5: Monitoring and Maintenance

# Azure Backup – Core Concepts (RTO, RPO, Snapshot, Vault, Archive)

This document summarizes the core Azure Backup concepts in a clear and practical way, with examples.
It is intended for AZ-104 exam preparation and long-term reference.

---

## 1. Recovery Time Objective (RTO)

**RTO = the maximum acceptable time to restore a service after a failure**

It defines how long a system can be unavailable before the impact becomes unacceptable.

### Example
- A business application goes down
- The business can tolerate up to 3 hours of downtime
- The system must be restored within that time

**RTO = 3 hours**

Lower RTO usually means faster (and more expensive) solutions.

---

## 2. Recovery Point Objective (RPO)

**RPO = the maximum amount of data loss, measured in time**

It defines how much data can be lost when a failure occurs.

### Example
- Backups are taken every hour
- A failure happens at 10:45
- The last backup was at 10:00

Up to 45 minutes of data is lost  
**RPO = 1 hour**

Lower RPO requires more frequent backups or replication.

---

## 3. Snapshot Tier

**Snapshot = a point-in-time copy of data**

- Captures the state of disks or files at a specific moment
- Stored inside the customer’s Azure subscription
- Mainly used for fast restores

### Key characteristics
- Very fast recovery
- Short-term retention
- Not isolated from the source

### Example use cases
- Bad update
- Accidental deletion
- Quick rollback

Snapshot = fast recovery, not long-term protection.

---

## 4. Vault-Standard Tier (Azure Backup Vault)

**Vault backup = isolated and secure backup storage**

- Stored in a Microsoft-managed Recovery Services Vault
- Isolated from the source workload
- Protects against deletion, corruption, and ransomware

### Key characteristics
- Slower restore than snapshots
- High security
- Medium to long-term retention

### Example use cases
- VM deleted
- Subscription compromised
- Ransomware incident

Vault backup = survivability and security.

---

## 5. Archive Tier

**Archive tier = long-term retention for compliance**

- Designed for data that is rarely accessed
- Used mainly for legal or regulatory requirements

### Key characteristics
- Lowest storage cost
- Slow restore
- Not intended for daily operations

### Example use cases
- 7–10 year retention policies
- Audits and compliance checks

Archive = keep data safe for the long term.

## 6. Azure Virtual Machine and Disks – Basics

When you create an Azure Virtual Machine:

- Azure **automatically creates a Managed OS Disk**
- The VM **always runs from Managed Disks**
- You do **NOT** need to create a Storage Account for normal VM operation

### Important points
- OS disk is mandatory
- Data disks are optional
- Managed Disks are fully handled by Azure

The VM **never runs directly from a Storage Account**.

---

## 7. Managed Disks

**Managed Disk = the actual disk used by the VM**

### Key characteristics
- Bootable
- Attach/detach to VMs
- Snapshottable
- Billable even when not attached

Managed Disks are the final, production disks.

---

## 8. Disk Size Configuration

You can configure disk size:

- During VM creation
- Later (only increase, never decrease)

### Rules
- Disk size can be increased
- Disk size cannot be reduced
- OS disk resize usually requires VM stop

---

## 9. Azure VM Restore – What Really Happens

When an OS disk becomes corrupted, Azure **does NOT repair the existing disk**.

Instead, Azure performs a **disk replacement**.

---

## 10. Restore Flow (Replace Existing)

### Step-by-step process

1. Backup data exists in the Recovery Services Vault
2. A **temporary Staging Storage Account** is required
3. Azure extracts the backup into the staging storage
4. Azure **creates a NEW Managed Disk** from the backup
5. The VM attaches to the new disk
6. The original disk is detached

### Restore flow
Recovery Services Vault
↓
Staging Storage Account (temporary)
↓
New Managed Disk (final)
↓
Virtual Machine


---

## 11. What Is the Staging Storage Account?

**Staging Storage Account = temporary working area**

- Used only during restore
- Holds unpacked backup data
- Not used by the VM
- Can be deleted after restore

It is required because Azure cannot restore disks directly from the vault.

---

## 12. What Happens to the Old (Corrupted) Disk?

After restore with **Replace existing**:

- The original OS disk is **detached**
- It becomes an independent resource
- Azure does **not** delete it automatically

### Important
- Detached disks still incur storage costs
- They are billed the same as attached disks

---

## 13. Cost Consideration – Detached Disks

If you do nothing:
- The detached disk remains
- Storage cost continues indefinitely

### Best practice after restore
1. Verify the VM works correctly
2. Decide to:
   - Delete the old disk
   - Snapshot it
   - Keep it temporarily

Unused disks are a common hidden cost.

---

## 14. Backup vs Site Recovery (High-Level)

**Azure Backup**
- Used for data recovery
- Restore takes time
- Suitable for VM corruption or deletion

**Azure Site Recovery**
- Used for disaster recovery
- Requires pre-configuration
- Enables fast failover during regional outages

Backup = restore data  
ASR = continue service

---

## 15. Exam Key Takeaways (AZ-104)

- VMs run on Managed Disks, not Storage Accounts
- Restore creates new disks, it does not fix old ones
- Staging Storage Account is temporary
- Detached disks are still billed
- Disk size can be increased, not reduced
- Backup is reactive, Site Recovery is proactive

---

## Final Summary

Azure Backup restores data by rebuilding new managed disks from vault-stored backups using a temporary staging storage account.  
The old disks remain detached and billable until manually removed.

This design prioritizes safety, integrity, and recoverability.


