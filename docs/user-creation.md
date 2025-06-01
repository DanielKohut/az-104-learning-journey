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
| **User Principal Name (UPN)** | Unique login name for the user (like an email address) | testacc@yourtenant.onmicrosoft.com |
| **Display Name** | Full name of the user | Test Account |
| **Password** | Initial password (must meet complexity requirements) | P@ssw0rd1234 |
| **Account Enabled** | Whether the account is active or disabled | True |

> 🔒 **Note**: Passwords must meet tenant-defined password complexity policies. Whether the user must change the password at first login.
> User creating using PowerShell required Password as a mini object, PasswordProfile and Nickname.

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

<pre><font color="#26A269"><b>ubuntu@DanielKohut</b></font>:<font color="#12488B"><b>~</b></font>$ az ad user create --display-name &quot;User Created By CLI&quot; --password Password12345@ --user-principal-name usercreatedbycli@danikohutgmail.onmicrosoft.com
</pre>

![image](https://github.com/user-attachments/assets/e899ddf8-5ecd-4355-8b93-1d29cbcc9827)

## 🔧 3. Create a User via **PowerShell**

> ⚡️ Ensure Micorosft Graph Module is installed and authenticated.
> Install-Module Microsoft.Graph -Scope CurrentUser
> Connect-MgGraph -Scopes "User.ReadWrite.All"

📝 **Best Practice**: Make sure you have newer version of PowerShell like version V7.*, don`t use older V5.1.

New-MgUser `
  -DisplayName "User Created By PowerShell using Graph Module" `
  -MailNickname "usercreatedbypowershell" `
  -UserPrincipalName "usercreatedbypowershell@danikohutgmail.onmicrosoft.com" `
  -PasswordProfile @{
      Password = "Password123456@"
    }

![image](https://github.com/user-attachments/assets/ef280d1f-1a57-4c70-92fc-d4bb7b19a508)

Using Linux/Ubuntu:

![image](https://github.com/user-attachments/assets/82ae2564-dd5a-4527-9fde-3b853a6f1a83)

## 🔧 4. Create a User via **CLI or PowerShell using CloudShell**

![image](https://github.com/user-attachments/assets/46c0bb44-4c91-4cd4-91ff-aeca051e05b4)

# 👤 Users review on the portal

![image](https://github.com/user-attachments/assets/d5cc0b0f-ed6a-4b02-883e-575bbcf6d16a)

| **Tool**                  | **Required Parameters**                                                                 | **Notes**                                                                 |
|----------------------------|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------|
| **Azure CLI**              | `--display-name`<br>`--password`<br>`--user-principal-name`                              | `MailNickname` is **auto-generated** from the UserPrincipalName.          |
| **Microsoft Graph PowerShell** | `-DisplayName`<br>`-UserPrincipalName`<br>`-MailNickname`<br>`-PasswordProfile`             | `PasswordProfile` must include at least `Password`. No auto MailNickname. |
| **Azure Portal (UI)**      | **Display Name**<br>**User Principal Name**<br>**Password (auto or custom)**                                | UI auto-generates MailNickname from username.                             |

---

### 🔹 Additional Notes
- **Azure CLI** is lenient; it auto-generates `MailNickname` based on `UserPrincipalName` if not provided.
- **PowerShell (Microsoft Graph SDK)** is strict; it **requires explicit `MailNickname`** and the `Password` inside a `PasswordProfile` hashtable.
- **Azure Portal** auto-handles many fields behind the scenes, but **Name** and **Username** are mandatory.
- **First-time Sign-In Password Change**:
  - **CLI** and **Portal**: Automatically force password change on first sign-in.
  - **PowerShell**: By default, `ForceChangePasswordNextSignIn` is **true** unless specified otherwise.

---

