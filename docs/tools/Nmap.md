# Nmap: Network Mapper & Port Scanner

Nmap is an open-source network scanner used for network discovery, port scanning, service version detection, OS identification, and automated vulnerability assessments.

---

## 1. Operating Mechanism & Scan Architecture

```text
[ Nmap Scanner (Kali Linux) ] ──(Raw IP Packets)──► [ Target Subnet / Hosts ]
                                                          │
                              ◄──(TCP / UDP Responses)───┘
```

Nmap sends specially crafted raw IP packets and analyzes the responses (or lack of responses) to determine live hosts, open ports, firewall filtering rules, and running service banners.

---

## 2. Core Scanning Techniques

### 2.1 Host Discovery (Ping Sweep)
Discovers live machines on a network subnet without scanning individual ports:

```bash
nmap -sn 192.168.10.0/24
```

* **Mechanism:** Sends ICMP Echo Requests, TCP SYN to port 443, TCP ACK to port 80, and ICMP timestamp requests. On local subnets, it uses ARP requests.

---

### 2.2 TCP SYN Stealth Scan (`-sS`)
The default and most widely used port scan:

```text
Client (Nmap)                     Target Host (Port Open)
     │                                       │
     │── 1. TCP SYN (Seq = X) ──────────────►│
     │                                       │
     │◄── 2. TCP SYN-ACK (Seq = Y, Ack = X+1)─│ (Port is OPEN)
     │                                       │
     │── 3. TCP RST (Teardown connection) ──►│
```

* **Advantage:** Never completes the full three-way handshake, making it fast and less likely to be logged by basic application-layer loggers.

---

### 2.3 TCP Connect Scan (`-sT`)
Completes the full three-way handshake via operating system network socket calls:

* **Use Case:** Used when the user does not have raw packet privileges (non-root users).

---

### 2.4 Service & OS Version Detection
```bash
# Service Version Detection (-sV)
nmap -sV -p 80,443,445 192.168.10.10

# Default Safe Script Scan (-sC)
nmap -sC -p 80,443 192.168.10.10

# Comprehensive Version & Script Scan
sudo nmap -sS -sV -sC -T4 192.168.10.10
```

---

## 3. High-Yield Command Options & Flags

| Flag | Category | Purpose / Description |
| :--- | :--- | :--- |
| `-sn` | Host Discovery | Ping sweep (disable port scanning). |
| `-sS` | Scan Technique | TCP SYN stealth scan. |
| `-sT` | Scan Technique | Full TCP Connect scan. |
| `-sU` | Scan Technique | UDP port scan. |
| `-p-` | Port Scope | Scan all 65,535 TCP ports. |
| `-p 80,443,445` | Port Scope | Scan specific targeted ports. |
| `-sV` | Version Detection | Probe open ports to determine service name and version info. |
| `-sC` | Scripting (NSE) | Run default safe Nmap Scripting Engine (NSE) scripts. |
| `-O` | OS Detection | Identify target operating system via TCP/IP fingerprinting. |
| `-T0` to `-T5` | Timing | Adjust scan speed (`-T4` recommended for modern LANs/labs). |
| `-oN <file>` | Output | Save scan results in standard human-readable text format. |
| `-oA <prefix>` | Output | Output in all three major formats (Text, XML, Grepable). |
