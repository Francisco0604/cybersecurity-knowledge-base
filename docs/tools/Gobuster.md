# Gobuster: Directory, DNS & VHost Brute-Forcer

Gobuster is a fast, multithreaded command-line scanner written in Go used to brute-force URIs (directories and files), DNS subdomains, Virtual Hosts (VHosts), and open Amazon S3 buckets.

---

## 1. Operating Modes

```text
Gobuster Modes
├── dir   (Enumerate website directories, hidden folders, and files)
├── dns   (Brute-force DNS subdomains against target domain)
├── vhost (Enumerate virtual host header names on a target web server)
└── s3    (Enumerate public Amazon S3 buckets)
```

---

## 2. Essential Commands

### 2.1 Directory & File Fuzzing (`gobuster dir`)
```bash
# Basic directory enumeration
gobuster dir -u http://192.168.10.30 -w /usr/share/wordlists/dirb/common.txt

# Directory enumeration with specific file extensions
gobuster dir -u http://192.168.10.30 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 40 -x php,txt,html,bak

# Include specific status codes
gobuster dir -u http://192.168.10.30 -w common.txt -s "200,204,301,302,307,403"
```

### 2.2 DNS Subdomain Brute-Forcing (`gobuster dns`)
```bash
gobuster dns -d example.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -t 30
```

### 2.3 Virtual Host Enumeration (`gobuster vhost`)
```bash
gobuster vhost -u http://example.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain
```

---

## 3. Key Flags Reference

| Flag | Purpose / Description |
| :--- | :--- |
| `-u <url>` | Target URL. |
| `-w <wordlist>` | Path to wordlist file. |
| `-t <threads>` | Number of concurrent worker threads (default 10). |
| `-x <extensions>` | Comma-separated list of file extensions to append. |
| `-s <codes>` | Comma-separated list of acceptable HTTP status codes. |
| `-k` | Skip TLS certificate verification (useful in lab environments with self-signed certs). |
| `-o <file>` | Output results to a file. |
