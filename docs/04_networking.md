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
