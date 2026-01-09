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

---

## 6. How These Concepts Fit Together

The same data can exist in multiple tiers, each serving a different purpose:

