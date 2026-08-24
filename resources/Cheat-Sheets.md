# Cybersecurity & Penetration Testing Cheat Sheets

High-yield syntax references and command structures for core networking, web security, and infrastructure assessment tools.

---

## 1. Nmap Network Scanner Cheat Sheet

```bash
# Host Discovery / Ping Sweep
nmap -sn 192.168.10.0/24

# Fast TCP SYN Stealth Scan (Default top 1000 ports)
sudo nmap -sS -T4 192.168.10.10

# Full TCP Port Scan with Service Versioning & Default Scripts
sudo nmap -p- -sS -sV -sC -T4 192.168.10.10 -oN nmap_full.txt

# Aggressive Scan (OS detection, version detection, script scanning, traceroute)
sudo nmap -A -T4 192.168.10.10

# Targeted Script Scan for Vulnerabilities
nmap --script "vuln and safe" -p 80,443,445 192.168.10.10
```

---

## 2. FFUF High-Speed Web Fuzzing Cheat Sheet

```bash
# Directory & File Fuzzing
ffuf -u http://192.168.10.30/FUZZ -w /usr/share/wordlists/dirb/common.txt

# File Extension Fuzzing
ffuf -u http://192.168.10.30/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -e .php,.html,.txt,.bak

# Parameter Discovery (GET)
ffuf -u "http://192.168.10.30/index.php?FUZZ=test" -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -fs 240

# Parameter Fuzzing (POST JSON)
ffuf -u http://192.168.10.30/api/login -X POST -H "Content-Type: application/json" -d '{"username":"admin","password":"FUZZ"}' -w passwords.txt -fc 401

# Subdomain / VHost Fuzzing
ffuf -u http://target.com -H "Host: FUZZ.target.com" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -fs 1245
```

---

## 3. Gobuster Directory & DNS Scanner Cheat Sheet

```bash
# Directory Enumeration
gobuster dir -u http://192.168.10.30 -w /usr/share/wordlists/dirb/common.txt -t 30 -x php,txt,html

# DNS Subdomain Brute-Forcing
gobuster dns -d example.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# Virtual Host Fuzzing
gobuster vhost -u http://example.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain
```

---

## 4. THC-Hydra Authentication Cracking Cheat Sheet

```bash
# HTTP Basic Authentication
hydra -l admin -P 500-worst-passwords.txt 192.168.10.30 http-get /labs/basic_auth/

# HTTP POST Form
hydra -l admin -P passwords.txt 192.168.10.30 http-post-form "/login.php:user=^USER^&pass=^PASS^:F=Invalid"

# SSH Dictionary Attack
hydra -L users.txt -P passwords.txt 192.168.10.20 ssh -t 4
```

---

## 5. Offline Hash & Secret Cracking Cheat Sheet

```bash
# John the Ripper - JWT HMAC-SHA256 Secret
john --wordlist=jwt.secrets.list --format=HMAC-SHA256 jwt.txt

# John the Ripper - NetNTLMv2
john --wordlist=rockyou.txt --format=netntlmv2 responder_hashes.txt

# Hashcat - NetNTLMv2 Hash (Responder)
hashcat -m 5600 -a 0 captured_ntlmv2.txt rockyou.txt

# Hashcat - Kerberoasting TGS Ticket
hashcat -m 13100 -a 0 kerberoast_tickets.txt rockyou.txt

# Hashcat - AS-REP Roasting Ticket
hashcat -m 18200 -a 0 asrep_tickets.txt rockyou.txt
```

---

## 6. Wireshark Packet Display Filters Cheat Sheet

```text
# Protocols
tcp || udp || dns || http || tls || arp || icmp

# IP Addresses & Ports
ip.addr == 192.168.10.10
ip.src == 192.168.10.50 and ip.dst == 192.168.10.10
tcp.port == 80 or tcp.port == 443
udp.port == 53

# Handshakes & Flags
tcp.flags.syn == 1 and tcp.flags.ack == 0
tcp.flags.reset == 1

# HTTP Inspection
http.request.method == "POST"
http.response.code >= 400
http contains "password"
```

---

## 7. Active Directory & Network Testing Cheat Sheet

```bash
# Responder LLMNR/NBT-NS Poisoning
sudo responder -I eth0 -dwv

# Active Directory SPN Registration (Windows Server)
setspn -a MSSQLSvc/sql01.corp.local:1433 CORP\sql_service

# Docker Vulnerable Target Deployment
docker run -d -p 3000:3000 --name juice-shop bkimminich/juice-shop
```
