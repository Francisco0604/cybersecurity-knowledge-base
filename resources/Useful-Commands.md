# Useful Commands & CLI Tool Reference

A categorized collection of essential command-line tools for network scanning, web application security testing, credential auditing, and virtual lab administration.

---

## 1. Network Discovery & Port Scanning

```bash
# Verify local IP assignment on Linux
ip -br -c a

# Ping sweep to discover live hosts on local subnet
nmap -sn 192.168.10.0/24

# Fast SYN scan of top 1000 ports with timing template 4
sudo nmap -sS -T4 192.168.10.10

# Comprehensive scan of all 65,535 TCP ports with version detection
sudo nmap -p- -sV -sC -T4 192.168.10.10 -oA initial_scan_results

# Scan UDP top ports
sudo nmap -sU --top-ports 100 -T4 192.168.10.10
```

---

## 2. Web Application Fuzzing & Enumeration

```bash
# Directory and file fuzzing with common extensions
ffuf -u http://192.168.10.30/FUZZ -w /usr/share/wordlists/dirb/common.txt -e .php,.html,.txt -c

# Match and filter responses by status code, word count, or line count
ffuf -u http://192.168.10.30/FUZZ -w wordlist.txt -fc 404 -fs 1234 -fl 45

# Fuzz hidden URL query parameters
ffuf -u "http://192.168.10.30/view.php?FUZZ=1" -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -fs 0

# Fast directory enumeration with Gobuster
gobuster dir -u http://192.168.10.30 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 40
```

---

## 3. Web & API Command-Line Requests (`curl`)

```bash
# Issue an HTTP GET request and inspect headers
curl -i http://192.168.10.30/index.php

# Send an HTTP POST request with JSON body
curl -H "Content-Type: application/json" -X POST -d '{"username":"admin","password":"password1"}' http://192.168.10.30/api/login

# Send request with Bearer JWT Authorization header
curl -H "Authorization: Bearer <JWT_TOKEN>" http://192.168.10.30/api/v1.0/profile

# Follow HTTP redirects automatically
curl -L http://192.168.10.30/
```

---

## 4. Credential & Hash Cracking

```bash
# THC-Hydra HTTP Basic Auth dictionary attack
hydra -l admin -P /usr/share/seclists/Passwords/Common-Credentials/500-worst-passwords.txt target.thm http-get /labs/basic_auth/

# John the Ripper - Crack HS256 JWT secret
john --wordlist=jwt.secrets.list --format=HMAC-SHA256 jwt.txt

# Hashcat - Crack NetNTLMv2 hash from Responder
hashcat -m 5600 -a 0 ntlmv2_hash.txt /usr/share/wordlists/rockyou.txt

# Hashcat - Crack Kerberoast TGS ticket
hashcat -m 13100 -a 0 tgs_ticket.txt /usr/share/wordlists/rockyou.txt
```

---

## 5. Active Directory & Enterprise Lab Tools

```bash
# Launch Responder to poison LLMNR/NBT-NS queries on eth0
sudo responder -I eth0 -dwv

# Active Directory: Register a Service Principal Name (Windows CMD / PowerShell)
setspn -a MSSQLSvc/sql01.corp.local:1433 CORP\sql_service

# Active Directory: List registered SPNs for a domain user
setspn -L CORP\sql_service

# Test DNS resolution against a specific server
nslookup example.com 192.168.10.10
```

---

## 6. Docker Container Management in Labs

```bash
# Launch OWASP Juice Shop container
docker run -d -p 3000:3000 --name juice-shop bkimminich/juice-shop

# Launch DVWA container
docker run -d -p 80:80 --name dvwa vulnerables/web-dvwa

# List running containers
docker ps

# Stop and restart container
docker restart juice-shop

# Stop and remove container
docker stop juice-shop && docker rm juice-shop
```
