# Active Directory Attacks & Hardening Strategies

Active Directory environments present a rich attack surface due to legacy protocol backwards compatibility, complex Kerberos delegation mechanisms, and permission misconfigurations. This document details the foundational attack vectors and enterprise hardening controls.

---

## 1. Active Directory Attack Vectors

```text
Active Directory Attack Lifecycle
├── 1. Internal Reconnaissance (Nmap subnet sweeps, Domain enumeration)
├── 2. Credential Capture & Broadcast Poisoning (LLMNR / NBT-NS via Responder)
├── 3. Service Ticket Attacks (Kerberoasting registered SPNs)
├── 4. Pre-Authentication Attacks (AS-REP Roasting)
├── 5. Lateral Movement & Hash Reuse (Pass-the-Hash / PtH)
└── 6. Domain Escalation & Persistence
```

---

### 1.1 LLMNR & NBT-NS Poisoning (Responder)
When a Windows machine fails to resolve a hostname via primary DNS (e.g., mistyping a network share name like `\\file-servr`), it falls back to broadcast protocols:
- **LLMNR:** Link-Local Multicast Name Resolution (UDP port 5355).
- **NBT-NS:** NetBIOS Name Service (UDP port 137).

```text
Victim Client                      Attacker (Responder)              Target Service
     │                                      │                              │
     │── 1. DNS Query: "file-servr" ────────┼─────────────────────────────►│ (DNS: NXDOMAIN)
     │                                      │
     │── 2. LLMNR Broadcast: "Who is file-servr?" ──►│
     │                                      │
     │◄── 3. Spoofed LLMNR Response: "I am file-servr!"
     │                                      │
     │── 4. NTLMv2 Authentication Challenge/Response ──►│
     │                                      │
     │                        [ CAPTURES NTLMv2 HASH ]
```

* **Attack Mechanism:** An attacker running **Responder** listens on the local subnet, answers the broadcast claiming to be the requested resource, issues an NTLM authentication challenge, and captures the victim's **NetNTLMv2 / NTLMv2 password hash**.
* **Impact:** The captured NetNTLMv2 hash can be cracked offline using Hashcat or John the Ripper (`hashcat -m 5600`).

---

### 1.2 Kerberoasting
Kerberoasting is an authenticated post-compromise attack targeting Active Directory service accounts:

```text
Domain User (Attacker)                 Domain Controller (KDC)
          │                                      │
          │── 1. Request TGS Ticket for SPN ────►│ (e.g., MSSQLSvc/sql01:1433)
          │                                      │
          │◄── 2. Return TGS Ticket ─────────────│ (Encrypted with Service Account NTLM Hash)
          │
[ Extract Ticket from Memory & Crack Offline ]
```

* **Attack Mechanism:** Any authenticated domain user can request a Kerberos Ticket Granting Service (TGS) ticket for any account with a registered Service Principal Name (SPN).
* **Vulnerability:** The ticket is encrypted using the NTLM hash of the target service account's password. The attacker extracts this ticket and cracks the plaintext password offline without generating network noise against the DC.

---

### 1.3 AS-REP Roasting
AS-REP Roasting targets domain user accounts that have the configuration flag **"Do not require Kerberos preauthentication"** enabled (`DONT_REQ_PREAUTH`):

* **Attack Mechanism:** An attacker requests an Authentication Service Response (**AS-REP**) for the target user without sending pre-authentication timestamps.
* **Vulnerability:** The Domain Controller returns an encrypted AS-REP ticket. The attacker extracts the encrypted portion and cracks it offline (`hashcat -m 18200`) to recover the user's plaintext password.

---

### 1.4 Pass-the-Hash (PtH) & Lateral Movement
Pass-the-Hash allows an attacker to authenticate to remote Windows services (SMB, WMI, WinRM) using a user's NTLM password hash directly, without ever cracking or knowing the cleartext password.

---

## 2. Enterprise Hardening & Defensive Controls

| Attack Vector | Recommended Enterprise Defensive Controls |
| :--- | :--- |
| **LLMNR / NBT-NS Poisoning** | Disable LLMNR via Group Policy (`Turn off multicast name resolution`); Disable NetBIOS on network adapters; Enforce **SMB Signing** across all servers and endpoints. |
| **Kerberoasting** | Enforce long, complex passwords ($\ge 25$ characters) for all service accounts; Migrate service accounts to **Group Managed Service Accounts (gMSAs)** which automatically rotate 128-character passwords. |
| **AS-REP Roasting** | Audit domain accounts using PowerShell to ensure Kerberos pre-authentication is enforced on all active user accounts. |
| **Pass-the-Hash (PtH)** | Implement Tiered Administrative Model (Tier 0/1/2); Restrict Domain Admin accounts from logging into standard workstations; Enable Remote Credential Guard and Protected Users security group. |
