# 👤 User Creation in Microsoft Entra ID

## 🧾 Overview

In **Microsoft Entra ID**, users are the core identity objects.  
They can represent employees, service accounts, guests, or any identity that needs access to resources.

Users can be created through different methods:
- Azure Portal (manual, UI-based)
- Azure CLI (command-line tool for scripting)
- Azure PowerShell (administrative scripting)
- On-premises Active Directory synced via **Entra Connect**

---

## 🔑 Required Fields for User Creation

| Field | Description | Example |
|------|-------------|---------|
| **User Principal Name (UPN)** | Unique login name for the user (like an email address) | john.doe@yourtenant.onmicrosoft.com |
| **Display Name** | Full name of the user | Test Account |
| **Password** | Initial password (must meet complexity requirements) | P@ssw0rd1234 |
| **Account Enabled** | Whether the account is active or disabled | True |

> 🔒 **Note**: Passwords must meet tenant-defined password complexity policies. Whether the user must change the password at first login.

---

## 🖥️ 1. Create a User via **Azure Portal**

### Steps:
1. Open the [Azure Portal](https://portal.azure.com/).
2. Search for **Microsoft Entra ID** and select it.
3. Navigate to **Users** under the **Manage** section.
4. Click **+ New user** → **Create new user**.
5. Fill in the following mandatory fields:
   - **User Principal Name**
   - **Display Name**
   - **Initial Password**
6. (Optional) Assign groups or roles.
7. Click **Create**.

📝 **Best Practice**: Enable **Multi-Factor Authentication (MFA)** for new users.

![image](https://github.com/user-attachments/assets/14bc2bea-cae1-4d94-9763-44a96002bb7d)

## 🔧 2. Create a User via **Azure CLI**

> ⚡️ Ensure Azure CLI is installed and authenticated (`az login`).

### Command:
```bash
az ad user create --display-name "John Doe" \
                  --password "P@ssw0rd1234" \
                  --user-principal-name "john.doe@yourtenant.onmicrosoft.com" \
                  --force-change-password-next-login true
