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

## 37. VNet Peering – Same vs Different Regions

VNet Peering allows virtual networks to communicate privately using Azure’s
backbone network.

Key characteristics:
- Low latency and high bandwidth
- Traffic stays on the Microsoft network
- No VPN gateway required

Important rules:
- VNets must have non-overlapping address spaces
- Peering works across regions (Global VNet Peering)
- Peered VNets behave like one network for routing

Traffic behaviour:
- No transitive routing by default
- Network Security Groups still apply
- User-Defined Routes can influence peered traffic

Common use case:
Hub-and-spoke network architecture.


## 38. Overlapping Address Spaces – Hard Limitation

Azure does not allow communication between VNets that have overlapping IP
address spaces.

This rule applies regardless of:
- Azure region
- Subscription
- Tenant

If VNets have overlapping address spaces:
- VNet peering is not possible
- VPN or ExpressRoute connections will not work

Overlapping address spaces are allowed only when:
- VNets are completely isolated
- No connectivity is ever required

### NSG Priority and Default Rules – Key Behaviour

- NSG rules are evaluated from lowest to highest priority number
- The first matching rule determines the action
- Azure provides default inbound rules

Default inbound rules include:
- AllowVnetInBound (65000)
- AllowAzureLoadBalancerInBound (65001)
- DenyAllInBound (65500)

When a VM is behind an Azure Load Balancer:
- The VM sees the source as AzureLoadBalancer
- If no higher-priority deny rule exists, inbound traffic is allowed
  by the default AllowAzureLoadBalancerInBound rule


Exam rule:
If VNets must communicate, address spaces must not overlap.

## 39. Well-Known Ports – Web and Internet Services

These ports are required for hosting and accessing web applications in Azure.

HTTP (Port 80):
- Used for unencrypted web traffic
- Commonly redirected to HTTPS
- Used by load balancers and App Services

HTTPS (Port 443):
- Used for encrypted web traffic (TLS/SSL)
- Required for Azure Portal, ARM API, Storage, and most PaaS services
- Most critical port in Azure environments

Exam tip:
Web applications typically require **80 and 443**, but production systems
should allow **443 only**.


## 40. DNS – Name Resolution (Critical Exam Topic)

DNS is required for almost all Azure services.

DNS uses:
- Port 53 UDP (standard queries)
- Port 53 TCP (large responses and zone transfers)

Important rule:
DNS is **not UDP-only**. Both UDP and TCP must be considered.

Exam tip:
If DNS traffic is blocked, most Azure services will fail to connect.


## 41. Remote Access Ports (VM Administration)

These ports allow administrators to manage virtual machines.

SSH (Port 22 TCP):
- Used for Linux VM administration
- Also used by SFTP

RDP (Port 3389 TCP):
- Used for Windows VM administration

Best practice:
Do not expose these ports directly to the internet.
Use Azure Bastion or restrict access via NSGs.


## 42. File and Storage Access Ports

These ports are required for file sharing and storage access.

SMB (Port 445 TCP):
- Used by Windows File Shares
- Required for Azure Files
- Must be allowed outbound for Azure Files access

NFS (Port 2049 TCP/UDP):
- Used for Linux file shares
- Supported by Azure Files (premium)

Exam tip:
Azure File Shares require **port 445**, not HTTP/HTTPS.


## 43. FTP and Secure File Transfer

FTP is still common in legacy systems.

FTP (Port 21 TCP):
- Control channel for FTP

FTP Data:
- Port 20 TCP (active mode)
- Dynamic ports (passive mode)

SFTP (Port 22 TCP):
- Secure file transfer over SSH
- Preferred over FTP

Exam tip:
FTP requires more than one port and is difficult to secure with NSGs.


## 44. Email and Messaging Ports

These ports are used for mail services and notifications.

SMTP (Port 25 TCP):
- Used for email delivery
- Often blocked outbound by default in Azure

SMTP Submission (Port 587 TCP):
- Secure mail submission
- Recommended alternative to port 25

POP3 (Port 110 TCP) / POP3S (995 TCP):
- Email retrieval

IMAP (Port 143 TCP) / IMAPS (993 TCP):
- Email synchronization

Exam tip:
Azure blocks outbound SMTP on port 25 by default.


## 45. Database Ports (Common Exam Knowledge)

Databases listen on vendor-specific ports.

SQL Server: 1433 TCP  
MySQL: 3306 TCP  
PostgreSQL: 5432 TCP  
Oracle: 1521 TCP  
MongoDB: 27017 TCP  
Redis: 6379 TCP  

Exam tip:
Database ports should be restricted to application subnets only.


## 46. Identity and Directory Services

These ports are used for authentication and directory services.

LDAP: 389 TCP/UDP  
LDAPS: 636 TCP  
Kerberos: 88 TCP/UDP  
Global Catalog: 3268 TCP  
Global Catalog SSL: 3269 TCP  

Used by:
- Active Directory
- Azure AD Domain Services
- Hybrid identity scenarios


## 47. Time, Monitoring, and Infrastructure Ports

These ports support system-level services.

NTP (Port 123 UDP):
- Time synchronization
- Critical for authentication and logging

SNMP (Ports 161/162 UDP):
- Monitoring and telemetry


## 48. Azure-Specific Networking Port Summary

Common Azure services require the following ports:

Azure Portal / ARM / APIs:
- 443 TCP

Azure Load Balancer health probes:
- Configurable, often HTTP/HTTPS

Azure Bastion:
- No inbound ports required on the VM

Azure File Sync:
- 443 TCP (control)
- 445 TCP (file access)


## 49. Exam Mental Model – Ports and NSGs

Remember the following patterns:

Web services     → 80 / 443  
DNS              → 53 (UDP + TCP)  
Linux admin      → 22  
Windows admin    → 3389  
File shares      → 445  
Databases        → Vendor-specific  

NSG rules:
- Lowest priority number wins
- First matching rule applies
- Default DenyAllInBound blocks everything not explicitly allowed


## 50. One-Sentence Ports Summary

> Azure networking relies on well-known ports such as 80 and 443 for web traffic,
> 53 for DNS, 22 and 3389 for VM administration, 445 for file sharing, and
> vendor-specific ports for databases, all controlled using Network Security Groups.

## 51. Exam Mental Model – User-Defined Routes (UDRs)

Remember the following routing principles:

UDRs:
- Override Azure system routes
- Apply **only to the subnet they are associated with**
- Affect **outbound traffic only**
- Do not affect inbound traffic

Routing precedence:
- User-defined routes (UDR)
- BGP routes
- System routes

Key rule:
- If a UDR exists, Azure does **not** fall back to system routes


## 52. What a UDR Actually Does

A User-Defined Route tells Azure how to forward traffic **leaving a subnet**.

A route consists of:
- Destination address prefix
- Next hop type
- Next hop address (if required)

Common next hop types:
- Virtual appliance
- Internet
- Virtual network gateway
- None (blackhole)

Example:
- Destination: `10.0.1.0/24`  
- Next hop type: `Virtual appliance`  
- Next hop address: `10.0.3.4`  

Meaning:
Traffic destined for `10.0.1.0/24` must pass through the virtual appliance at `10.0.3.4`.


## 53. Virtual Appliance Routing Responsibility

When using a virtual appliance as the next hop:

Azure:
- Delivers traffic to the appliance
- Does not inspect or forward traffic further

Virtual appliance VM must:
- Have IP forwarding enabled on the NIC
- Have routing enabled in the operating system
- Handle forwarding and filtering itself

Azure handles **delivery to the next hop only**.


## 54. Subnet Scope of UDRs (Critical Exam Rule)

UDRs are:
- Not global
- Not bidirectional

UDRs apply only to:
- Traffic originating from the subnet
- Traffic matching the destination prefix

UDRs do NOT apply to:
- Traffic originating from other subnets
- Traffic entering the subnet

Example:
- RT1 applied to Subnet1 and Subnet2
- Subnet3 has no route table

Result:
- Subnet1 traffic follows the UDR
- Subnet2 traffic follows the UDR
- Subnet3 traffic uses Azure system routes


## 55. UDR Failure Scenario

If a UDR forces traffic through a virtual appliance and that appliance is unavailable:

- Traffic from the subnet fails
- Azure does not reroute traffic automatically
- There is no fallback to system routes

UDR-based designs can introduce a single point of failure
unless high availability is implemented.


## 56. Common UDR Use Cases

Typical scenarios where UDRs are used:

- Central firewall inspection
- Forced tunneling to on-premises
- IDS / IPS traffic inspection
- Proxy-based internet access
- Hub-and-spoke network architectures

Traffic flow pattern:
Subnet → UDR → Virtual appliance → Destination


## 57. One-Sentence UDR Summary

> User-Defined Routes allow explicit control over subnet egress traffic by
> overriding Azure system routes and forcing traffic through specific next hops,
> such as virtual appliances, with routing enforced only at the subnet level.

