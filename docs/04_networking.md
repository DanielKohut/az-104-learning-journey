# Module 4: Azure Networking

This document is a long-term reference and AZ-104 exam preparation note.
It explains Azure Virtual Networks, CIDR, subnets, and IP address calculation
from first principles, with clear examples and Azure-specific behaviour.

---

## 1. Why is it called a Virtual Network?

An Azure Virtual Network (VNet) is called *virtual* because it does not consist
of physical cables, switches, or routers that you own or manage.

Instead:
- The network is created and managed **in software**
- It exists **logically** inside Azure datacentres
- Azure provides the physical infrastructure underneath

Despite being virtual, it behaves like a traditional network:
- IP address ranges
- Subnets
- Routing
- Firewalls (NSGs)
- Gateways (VPN / ExpressRoute)

> A Virtual Network is a logically isolated network in Azure that enables secure
> communication between resources, the internet, and on-premises networks.

---

## 2. IPv4 Basics – Where do the numbers come from?

An IPv4 address always consists of **32 bits**.

Example:
10.0.0.0


In binary:


00001010.00000000.00000000.00000000


That is:
- 4 octets
- 8 bits per octet
- 32 bits total

This is the foundation of CIDR and subnetting.

---

## 3. CIDR Notation (/16, /24, etc.)

CIDR (Classless Inter-Domain Routing) defines:
- how many bits belong to the **network**
- how many bits are left for **hosts**

Example:


10.0.0.0/16


Meaning:
- first 16 bits → network portion
- remaining 16 bits → host portion

Host bits determine how many IP addresses are available.

---

## 4. Calculating IP Addresses

### Formula


Host bits = 32 − CIDR prefix
Total IPs = 2^(host bits)


### Examples

| CIDR | Host bits | Total IPs |
|---|---|---|
| /16 | 16 | 65,536 |
| /24 | 8 | 256 |
| /27 | 5 | 32 |
| /26 | 6 | 64 |

---

## 5. Azure Reserved IP Addresses (Very Important)

Azure **reserves 5 IP addresses in every subnet**, regardless of size.

Reserved IPs are used for:
- Network address
- Default gateway
- Azure DNS
- Azure internal services
- Azure broadcast-like address

### Example: /24 subnet


256 total IPs
− 5 Azure reserved
= 251 usable IPs


This rule applies to **every subnet**.

---

## 6. What is a Subnet?

A subnet is a **logical subdivision of a VNet**.

Think of it as:
- VNet → city
- Subnet → street
- VM → house

Example:


VNet: 10.0.0.0/16

Subnets:

10.0.1.0/24 (frontend)

10.0.2.0/24 (backend)

10.0.3.0/24 (database)


Subnets allow:
- logical segmentation
- security boundaries
- service-specific requirements

---

## 7. Why Subnets Are Required in Azure

Subnets are needed to:
- Apply Network Security Groups (NSGs)
- Separate tiers (web, app, db)
- Support Azure services that require dedicated subnets

A VM **must** be placed into a subnet.

---

## 8. Available IPs vs Total IPs

The Azure Portal shows **Available IPs**, not total IPs.

Available IPs =  


(total IPs − 5 Azure reserved − used IPs)


Used IPs include:
- VM NICs
- Load Balancer frontend IPs
- Application Gateways
- Bastion
- Gateways

The number is **dynamic** and changes as resources are added or removed.

---

## 9. Example Subnet Calculations

### Example 1: Empty /24 subnet


256 − 5 = 251 available IPs


### Example 2: /24 subnet with 1 VM


251 − 1 = 250 available IPs


### Example 3: /27 subnet with 1 VM and 1 Load Balancer


/27 → 32 total
32 − 5 = 27 usable
27 − 2 = 25 available IPs


---

## 10. Special Azure Subnets

### AzureBastionSubnet
- Must have the exact name: `AzureBastionSubnet`
- Requires a dedicated subnet
- Uses multiple IPs internally
- Common sizes: /26 or larger

### GatewaySubnet
- Required for VPN Gateway and ExpressRoute
- Must have the exact name: `GatewaySubnet`
- IP usage is dynamic and not always shown as a fixed number
- Should not be too small

⚠️ `/29` is **not recommended** for VPN Gateway

---

## 11. Exam-Critical Rules to Remember

- IPv4 always has 32 bits
- Azure reserves **5 IPs per subnet**
- /24 subnet → **251 usable IPs**
- Available IPs change dynamically
- Bastion and Gateway require **dedicated subnets**
- Subnets must not overlap

---

## 12. Quick Exam Mental Checklist

1. Calculate host bits: `32 − prefix`
2. Calculate total IPs: `2^(host bits)`
3. Subtract 5 (Azure reserved)
4. Subtract used resources
5. Check if the subnet is special (Bastion / Gateway)

---

## 13. One-Sentence Exam Summary

> Azure Virtual Networks are logically isolated networks where IP addressing
> follows standard IPv4 CIDR rules, with Azure reserving five IP addresses per
> subnet and dynamically allocating addresses to resources.

## 14. Virtual Network Lab – Core Purpose

[LAB 01 – Configure secure access to workloads with Azure virtual networking services](https://microsoftlearning.github.io/Configure-secure-access-to-workloads-with-Azure-virtual-networking-services/Instructions/Labs/LAB_01_virtual_networks.html)


The purpose of this lab is to demonstrate how Azure Virtual Networks are used
to securely connect workloads while controlling inbound and outbound traffic
using layered network security.

The lab focuses on:
- Network isolation
- Controlled access to resources
- Secure traffic flow design
- Practical use of VNets, subnets, public IPs, and NSGs


## 15. Creating a Virtual Network and Address Space

A Virtual Network defines the private IP address space for Azure resources.

Key points:
- VNets use CIDR notation (for example: 10.0.0.0/16)
- The address space must be planned before deploying resources
- VNets provide logical isolation between workloads

Best practice:
- Allocate a sufficiently large address space
- Avoid resizing VNets after deployment if possible


## 16. Subnets and Network Segmentation

Subnets divide a Virtual Network into smaller logical segments.

Why subnets are used:
- Separate different workload tiers (web, app, database)
- Apply security rules at scale
- Support Azure services that require dedicated subnets

Important rules:
- Subnets must not overlap
- A virtual machine must always be deployed into a subnet


## 17. Network Security Groups (NSGs)

A Network Security Group acts as a stateful firewall for Azure networking.

NSGs can be associated with:
- A subnet
- A network interface (NIC)

Rules are evaluated based on:
- Priority (lower number = higher priority)
- Source and destination
- Port and protocol
- Allow or deny action

Default behaviour:
- Inbound traffic is denied by default
- Outbound traffic is allowed by default


## 18. NSG Association – Subnet vs NIC

Subnet-level NSG:
- Applies to all resources in the subnet
- Easier to manage
- Recommended for most scenarios

NIC-level NSG:
- Applies to a single virtual machine
- Used for exception-based rules

If both are present:
- Traffic must be allowed by both NSGs
- The most restrictive rule takes effect


## 19. Public IP and Private IP Usage

Public IP addresses are used only for external access.

Key concepts:
- Virtual machines always communicate internally using private IPs
- Azure performs NAT between public and private IP addresses
- Having a public IP does not automatically allow inbound traffic


## 20. Traffic Flow Through Azure Networking

Typical inbound traffic flow:

Internet
→ Public IP
→ NSG evaluation
→ Virtual machine (private IP)

If any NSG rule blocks traffic:
- The connection fails
- The resource is not reachable


## 21. Security Principles Demonstrated in the Lab

This lab reinforces core Azure security principles:
- Least privilege (only required ports are opened)
- Network segmentation using subnets
- Defence in depth using multiple security layers
- Secure-by-default networking behaviour


## 22. Common Configuration Mistakes

Common issues highlighted by the lab:
- Forgetting to associate an NSG
- Allowing overly broad inbound rules
- Using public IPs unnecessarily
- Deploying all workloads into a single subnet
- Poor IP address planning


## 23. Exam-Relevant Takeaways

For AZ-104 exam preparation:
- VNets provide logical network isolation
- Subnets segment VNets into manageable units
- NSGs control traffic at subnet and NIC level
- Public IPs do not bypass NSG rules
- Azure reserves five IP addresses per subnet


## 24. One-Sentence Lab Summary

This lab demonstrates how Azure Virtual Networks, subnets, public IPs, and
Network Security Groups work together to securely control traffic using
layered network security principles.


## 25. User-Defined Routes (UDRs) and Traffic Steering

Azure provides system routes by default to handle basic traffic flow.
In some scenarios, traffic must be explicitly routed through a specific resource.

This is achieved using User-Defined Routes (UDRs).

A UDR allows you to:
- Override Azure system routes
- Control where traffic is sent
- Forward traffic to a virtual appliance (firewall, IDS, proxy, inspection VM)

Common use case:
Routing all outbound or internal traffic through a network inspection VM.

Example:
Route: 0.0.0.0/0
Next hop type: Virtual appliance
Next hop address: Private IP of VM1

This ensures all traffic flows through VM1 before reaching its destination.

Important rules:
- UDRs are associated with subnets
- NSGs do not control routing
- System routes cannot be modified


## 26. System Routes vs User-Defined Routes

Azure routing follows a defined precedence order.

Route types:
- System routes – Default Azure routing
- User-defined routes – Custom traffic control
- BGP routes – Dynamic routing via gateways

Key rule:
User-defined routes override system routes.

If routing behaviour must change, UDRs are required.


## 27. Load Balancers – Load Balancing vs NAT

Azure Load Balancers support two distinct traffic patterns.

Load Balancing Rules:
- Used when one service is hosted on multiple backend VMs
- Traffic is distributed across all healthy backends
- Typical use cases: HTTP (80), HTTPS (443)

Inbound NAT Rules:
- Used when traffic must reach one specific VM
- One-to-one mapping between public port and private port
- Typical use cases: RDP (3389), SSH (22)

Example:
PublicIP:3389 → VM3:3389

Inbound NAT rules do not distribute traffic.


## 28. Network Gateways – Purpose and Function

A network gateway is used to connect separate networks.

Gateways are required for:
- Site-to-Site VPN
- Point-to-Site VPN
- VNet-to-VNet connections
- ExpressRoute

Gateways are not used for internal VNet traffic.


## 29. GatewaySubnet (Critical Exam Topic)

Azure network gateways require a dedicated subnet named exactly:

GatewaySubnet

Key rules:
- Must be named GatewaySubnet
- Must not contain other resources
- Used exclusively by the gateway
- IP usage is dynamic

Best practice:
- Use /27 or larger
- Avoid very small subnets


## 30. Local Network Gateway (On-Premises Definition)

The Local Network Gateway represents the on-premises network in Azure.

It contains:
- Public IP address of the on-premises VPN device
- On-premises internal IP address range

Important distinction:
- IP address refers to the VPN device public IP
- Address space refers to the on-prem internal network range

Example:
Public IP:      12.12.79.10
Address space:  10.112.1.0/24

The Local Network Gateway is not an Azure gateway.
It is a logical representation of the remote network.


## 31. Azure App Service and Virtual Networks

Azure App Services are not deployed inside VNets by default.

To allow an App Service to access:
- Virtual machines
- Databases
- Private endpoints

VNet Integration must be configured.

Key points:
- Enables outbound access from the App Service to the VNet
- Uses private IP connectivity
- Does not expose inbound traffic to the App Service

This is required when a web app must connect to backend resources using private IPs.


## 32. Azure Files vs Blob Storage (Networking Perspective)

Azure provides multiple storage services with different access models.

Azure Blob Storage:
- Accessed via HTTP or HTTPS
- Uses public or private endpoints
- Ideal for web content and unstructured data

Example:
https://sa.blob.core.windows.net/container/object

Azure File Share:
- Accessed via SMB or NFS
- Uses UNC paths
- Behaves like a traditional file share

Example:
\\storageaccount.file.core.windows.net\share

Key rule:
UNC paths are only used with Azure File Shares.
Blob storage is not mounted as a file share.


## 33. Azure File Share Networking Model

An Azure File Share:
- Is hosted inside a storage account
- Requires authentication (keys, SAS, or Azure AD)
- Can be accessed via public or private endpoints

The storage account does not expose files directly.
The file share is the actual network-accessible resource.


## 34. Network Watcher – Performance vs Connectivity

Azure Network Watcher provides multiple diagnostic tools.

Key distinctions:
- IP flow verify – Checks NSG allow or deny decisions
- Connection troubleshoot – One-time connectivity test
- Connection monitor – Continuous monitoring, RTT, latency
- NSG flow logs – Traffic metadata logging

If average round-trip time (RTT) or latency is required:
Connection Monitor must be used.


## 35. Exam Mental Model – Routing and Security

Keep these responsibilities clearly separated:

Routing     → User-Defined Routes
Security    → Network Security Groups
Access      → Public IP and NAT
Monitoring  → Network Watcher

Mixing these concepts is a common exam trap.


## 36. One-Sentence Network Summary (Extended)

Azure networking combines logical isolation, controlled routing, and layered
security using VNets, subnets, NSGs, gateways, and user-defined routes to
securely and efficiently control traffic flow.
