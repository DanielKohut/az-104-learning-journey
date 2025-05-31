# 🏢 Group Creation in Microsoft Entra ID

## 🧾 Overview

**Groups** in Microsoft Entra ID are collections of users, service principals, and devices.  
Groups simplify management by allowing you to assign permissions, access to resources, and policies to a set of users instead of individually.

There are two main types of groups:
- **Security groups** – Manage member and computer access to shared resources.
- **Microsoft 365 groups** (formerly Office 365 groups) – Provide collaboration tools (email, SharePoint, Teams, Planner).

---

## 🎯 Why Use Groups?

| Reason | Description |
|--------|-------------|
| ✅ Simplified management | Assign permissions to a group rather than individual users |
| ✅ Resource access | Manage access to applications, file shares, databases |
| ✅ Collaboration | Microsoft 365 Groups enable shared mailbox, calendar, SharePoint site, and Teams |
| ✅ Dynamic membership | Automatically add/remove users based on attributes (Dynamic groups) |

> 💡 **Dynamic Groups** require at least **Azure AD Premium P1** license.

---

## 🔗 Integrations with Groups

- **Microsoft 365**: Assign licenses and provide collaboration (Exchange, Teams, SharePoint).
- **Enterprise Applications (SaaS)**: Assign group-based access to apps like Salesforce, ServiceNow.
- **Azure RBAC**: Assign Azure resource permissions to groups.
- **Conditional Access**: Apply policies based on group membership.

---

## 🔑 Required Fields for Group Creation

| Field | Description | Example |
|------|-------------|---------|
| **Group Name** | Name of the group | Marketing Team |
| **Group Type** | Security or Microsoft 365 | Security |
| **Membership Type** | Assigned, Dynamic User, Dynamic Device | Assigned |
| **Description** | Purpose or details about the group | Access to marketing materials |

---

## 🖥️ 1. Create a Group via **Azure Portal**

### Steps:
1. Open the [Azure Portal](https://portal.azure.com/).
2. Navigate to **Microsoft Entra ID** > **Groups** > **+ New Group**.
3. Choose the **Group Type**:
   - **Security** or **Microsoft 365**
4. Fill in the required fields:
   - **Group Name**
   - **Group Description**
   - **Membership Type**: Assigned / Dynamic User / Dynamic Device
5. (Optional) Add members immediately.
6. Click **Create**.

---

## 💻 2. Create a Group via **Azure CLI**

> ⚡️ Requires Azure CLI and `az login`.

### Command:
```bash
az ad group create --display-name "Marketing Team" \
                   --mail-nickname "marketingteam" \
                   --security-enabled true \
                   --mail-enabled false

Important Parameters:
--display-name: Group name.

--mail-nickname: Unique alias for the group.

--security-enabled: true for Security group.

--mail-enabled: false for Security groups, true for M365 groups (email-enabled).

🧩 3. Create a Group via Azure PowerShell
⚡️ Requires the Az module.

Command:

New-AzADGroup -DisplayName "Marketing Team" `
              -MailNickname "marketingteam" `
              -SecurityEnabled $true `
              -MailEnabled $false

📝 For Microsoft 365 Groups, set -MailEnabled $true and ensure licenses are assigned properly.

🏢 4. Group Creation via Azure Entra Connect (On-Premises)
In hybrid environments:

Create groups in Active Directory Users and Computers (ADUC).

Security groups will sync automatically if within a synced OU.

Microsoft 365 Groups are cloud-only and cannot be created on-premises.

💡 Security Groups can be created on-premises and synchronized via Entra Connect.


