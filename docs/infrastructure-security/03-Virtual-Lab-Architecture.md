# Virtual Lab Architecture & Isolation Design

Building an isolated virtual lab environment is essential for safely practicing offensive security, malware analysis, network auditing, and Active Directory penetration testing without risking production networks or violating security policies.

---

## 1. Enterprise Home Lab Topology

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│ HYPERVISOR (VMware Workstation Pro / Player / VirtualBox)                   │
│                                                                             │
│  Isolated Virtual Subnet: VMnet (192.168.10.0/24 - Host-Only / Custom NAT)  │
│  ┌──────────────────┐  ┌──────────────────┐  ┌───────────────────────────┐  │
│  │ DC-01            │  │ WKSTN-01         │  │ KALI-ATTACK               │  │
│  │ Windows Server   │  │ Windows 10/11    │  │ Kali Linux                │  │
│  │ AD DS, DNS       │  │ Domain-Joined    │  │ Pentest Workstation       │  │
│  │ 192.168.10.10    │  │ 192.168.10.20    │  │ 192.168.10.50             │  │
│  └────────┬─────────┘  └────────┬─────────┘  └─────────────┬─────────────┘  │
│           │                     │                          │                │
│  ═════════╪═════════════════════╪══════════════════════════╪══════════════  │
│           │                     │                          │                │
│  ┌────────┴─────────────────────┴──────────────────────────┴─────────────┐  │
│  │ WEB-TARGET (Ubuntu Linux / Docker Engine - 192.168.10.30)             │  │
│  │ ├── Container 1: OWASP Juice Shop (Port 3000:3000)                    │  │
│  │ └── Container 2: Damn Vulnerable Web App / DVWA (Port 80:80)          │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 2. Virtual Networking Modes

Hypervisors support multiple virtual networking adapters (VMnet) with distinct security boundaries:

| Network Mode | Communication Boundary | Internet Access | Pentest Suitability |
| :--- | :--- | :--- | :--- |
| **Bridged** | VM shares physical network card and receives IP from home router. | Full Internet. | ⚠️ **High Risk:** Attacker and vulnerable target VMs are exposed to physical LAN. |
| **NAT (Network Address Translation)** | VM communicates through host IP via private subnet; host isolates VM traffic. | Outbound Internet. | 🟢 **Good:** Allows updating tools while isolating inbound traffic from LAN. |
| **Host-Only (Custom VMnet)** | Completely air-gapped private network connecting only designated VMs and the host. | No Internet. | 🟢 **Best:** Complete isolation for dangerous exploit or malware simulations. |

---

## 3. Virtual Machine Management & Baseline Snapshots

Offensive security testing can corrupt operating system registries, modify directory schemas, or destabilize services:

```text
Fresh VM Installation
        │
        ▼
Configure Base Network & Tools
        │
        ▼
[ TAKE BASELINE SNAPSHOT ] ──► "Clean Setup - AD & Web Targets Configured"
        │
        ├──► Execute Penetration Testing & Exploit Payloads
        │
        └──► Revert to Snapshot ──► Instant Restoration to Pristine State
```

* **Best Practice:** Always take a cold (powered-off) snapshot immediately after completing domain setup, before launching any offensive security tools.

---

## 4. Containerized Vulnerable Web Targets (Docker)

Deploying vulnerable targets in Docker containers on a Linux virtual machine provides lightweight, easily resettable practice environments:

* **OWASP Juice Shop:**
  ```bash
  docker run -d -p 3000:3000 bkimminich/juice-shop
  ```
* **Benefits:**
  - Instant deployment without manual web server or database configuration.
  - Resetting a broken target requires a single command (`docker restart` or `docker rm -f`).
  - Strict port-mapping isolates application vulnerabilities to specific host ports.
