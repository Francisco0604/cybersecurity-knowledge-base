# Active Directory Fundamentals & Enterprise Architecture

Active Directory Domain Services (AD DS) is Microsoft's centralized directory service and identity management platform. Used by over 90% of Fortune 500 enterprises, Active Directory provides centralized domain authentication, authorization, access control, and policy enforcement across enterprise networks.

---

## 1. Core Architecture & Organizational Hierarchy

```text
[ Active Directory Forest ]
              │
              ▼
    [ Root Domain: CORP.LOCAL ]
              │
     [ Domain Controller (DC-01) ] (Runs AD DS, Kerberos KDC, DNS)
              │
    ┌─────────┴───────────────────────────────┐
    │                                         │
    ▼                                         ▼
[ Organizational Units (OUs) ]      [ Domain-Joined Endpoints ]
  ├── OU: IT_Admins                   ├── DC-01 (192.168.10.10)
  ├── OU: CORP_Employees              └── WKSTN-01 (192.168.10.20)
  └── OU: Service_Accounts
```

---

## 2. Key Components of Active Directory

| Component | Definition | Security & Functional Role |
| :--- | :--- | :--- |
| **Domain Controller (DC)** | The central Windows Server hosting AD DS. | Authenticates users, validates credentials, enforces security policies, and distributes Kerberos tickets. |
| **Active Directory Domain Services (AD DS)** | Core server role providing the directory database (`ntds.dit`). | Stores identity information for all network users, computers, groups, and service objects. |
| **Forest & Domain** | Top-level logical container boundary in AD. | Establishes identity boundaries, schema definitions, and trust relationships across organizational units. |
| **Organizational Unit (OU)** | Subdivisions within a domain container. | Groups users, service accounts, and computer objects to apply granular **Group Policy Objects (GPOs)**. |
| **Domain-Joined Endpoint** | Client workstation or server attached to domain. | Authenticates local users against the Domain Controller rather than relying on local SAM database accounts. |
| **Active Directory Integrated DNS** | Centralized DNS service running on the DC. | Resolves domain hostnames and registers vital Service Location (**SRV**) records for Kerberos, LDAP, and DC discovery. |

---

## 3. Service Principal Names (SPNs)

A **Service Principal Name (SPN)** is a unique identifier assigned to a service instance running in a Windows domain. SPNs associate a service running on a host with a specific service account:

```text
Format:
ServiceClass/Host:Port Domain\AccountName

Example:
MSSQLSvc/sql01.corp.local:1433 CORP\sql_service
```

* **Role in Authentication:** When a domain user accesses a network service (e.g., MS SQL Server, IIS Web Server), Kerberos uses the registered SPN to determine which service account's password hash will encrypt the Kerberos ticket.

---

## 4. Active Directory Authentication Protocols

Active Directory networks primarily rely on two core authentication protocols:

```text
Active Directory Authentication
├── 1. Kerberos (Primary / Modern Standard)
│   ├── Ticket Granting Ticket (TGT) issued by Key Distribution Center (KDC)
│   ├── Ticket Granting Service (TGS) tickets for target services
│   └── High security; uses symmetric timestamped tickets
└── 2. NTLM / NetNTLMv2 (Legacy / Fallback Protocol)
    ├── Challenge-Response authentication mechanism
    ├── Used when hostnames are queried via IP or when Kerberos fails
    └── Vulnerable to network relay and poisoning attacks
```

---

## 5. Key Takeaways to Remember

1. **The DC is the crown jewel:** Compromising the Domain Controller grants complete control over all domain-joined assets and identities.
2. **DNS is foundational to AD:** Active Directory cannot function without properly configured DNS resolver settings (pointing to the DC).
3. **SPNs bridge services to accounts:** Every Kerberoastable service relies on a registered SPN linked to a domain user account.
4. **Group Policies enforce baseline security:** GPOs centrally push security configurations (disabling legacy protocols, enforcing password complexity) to all endpoints.
