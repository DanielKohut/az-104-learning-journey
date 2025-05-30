# 🔐 About Microsoft Entra ID (formerly Azure Active Directory)

## 🧾 What is Entra ID?

**Microsoft Entra ID** is Microsoft’s cloud-based identity and access management (IAM) service.  
It helps employees, partners, and customers securely access internal and external resources, such as Microsoft 365, the Azure portal, or any SaaS application integrated with Entra ID.

> 💡 Previously known as **Azure Active Directory (Azure AD)**

---

## 🕰️ Evolution / Naming History

## 🧾 Historical Background

**Active Directory** was first introduced by Microsoft in 1999 as part of **Windows 2000 Server**, providing centralized domain management and directory services. It was further enhanced in **Windows Server 2003**, and Microsoft later released patches to make Active Directory compatible with earlier versions of Windows, including Windows 95, 98, and NT 4.0.

As Microsoft expanded into cloud technologies, **Azure Active Directory (Azure AD)** emerged as the cloud-based counterpart to on-premises Active Directory. Although it is now rebranded as **Microsoft Entra ID**, many official and community documents still refer to it as Azure AD.

To bridge the gap between on-premises Active Directory and the cloud, Microsoft introduced **Azure AD Connect**, which enabled hybrid identity synchronization. This tool has also been renamed and is now known as **Microsoft Entra Connect**.


| Year | Name | Description |
|------|------|-------------|
| ~2010 | Azure Active Directory | Introduced as the identity service for Microsoft Azure |
| July 2023 | Microsoft Entra ID | Rebranded to align with the broader **Microsoft Entra** product family |

The core technology remained the same, but the **branding and roadmap evolved** to reflect Microsoft's vision of **modern identity, permissions, and secure access control**.

---

## 🧩 Entra ID Key Components

- **Users and Groups** – Identity objects managed within the tenant
- **Service Principals** – Application identities for auth/authz
- **Tenant** – The dedicated, isolated environment of Entra ID
- **Directory** – The container that holds identity data
- **Authentication methods** – Password, MFA, biometrics
- **Federation** – Support for external IdPs (SAML, OIDC, etc.)

---

## 🔁 Differences from Windows Server Active Directory

| Feature | Windows AD | Entra ID |
|---------|------------|----------|
| Location | On-premises | Cloud |
| Protocols | Kerberos, LDAP | SAML, OIDC, OAuth2 |
| Integration | Local networks | SaaS, Web apps, Microsoft 365 |
| Management | GPOs | Conditional Access, Identity Protection |

---

## 🌐 Entra ID Use Cases

- Single Sign-On (SSO) to Microsoft 365, Azure, and 3rd party apps
- Role-Based Access Control (RBAC) in Azure
- Conditional Access Policies
- MFA and passwordless authentication
- Identity protection and risk-based policies

---

## 📚 Further Reading

- [What is Microsoft Entra ID? – Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity/entra-id-overview)
- [Azure AD is becoming Microsoft Entra ID – Official Announcement](https://techcommunity.microsoft.com/t5/microsoft-entra-azure-ad-blog/azure-ad-is-becoming-microsoft-entra-id/ba-p/3871344)
