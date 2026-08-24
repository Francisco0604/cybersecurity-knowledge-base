# FFUF: High-Speed Web Fuzzing

FFUF (Fast Fuzzing) is an open-source, highly parallelized web fuzzer written in Go. It is used to discover hidden files, directories, API endpoints, URL parameters, subdomains, and virtual hosts.

---

## 1. Operating Mechanism

FFUF replaces the keyword `FUZZ` with entries from a wordlist, sends asynchronous HTTP requests, and evaluates server responses based on HTTP status codes, word counts, line counts, and byte sizes.

```text
[ Wordlist ] ──► [ FFUF Multithreaded Engine (FUZZ keyword replacement) ]
                              │
                              ▼
                   [ Target Web Server ]
                              │
                              ▼
                [ Filter & Match Rules (fc, fs, fw) ]
                              │
                              ▼
              [ Discovered Endpoints / Files ]
```

---

## 2. Essential Use Cases & Commands

### 2.1 Directory & File Fuzzing
```bash
# Directory discovery
ffuf -u http://192.168.10.30/FUZZ -w /usr/share/wordlists/dirb/common.txt

# File discovery with extensions
ffuf -u http://192.168.10.30/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -e .php,.html,.txt,.bak
```

### 2.2 Hidden Parameter Fuzzing (GET & POST)
```bash
# Discover GET query parameters
ffuf -u "http://192.168.10.30/view.php?FUZZ=1" -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -fs 0

# Discover POST JSON parameters
ffuf -u http://192.168.10.30/api/login -X POST -H "Content-Type: application/json" -d '{"username":"admin","password":"FUZZ"}' -w passwords.txt -fc 401
```

### 2.3 Virtual Host & Subdomain Discovery
```bash
ffuf -u http://target.thm -H "Host: FUZZ.target.thm" -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -fs 1245
```

---

## 3. Match and Filter Options

| Flag | Purpose / Function | Example |
| :--- | :--- | :--- |
| `-mc` | Match HTTP status codes (default: 200, 204, 301, 302, 307, 401, 403, 405). | `-mc 200,302` |
| `-fc` | Filter (ignore) specific HTTP status codes. | `-fc 404,403` |
| `-ms` / `-fs` | Match / Filter by HTTP response body size in bytes. | `-fs 1420` |
| `-mw` / `-fw` | Match / Filter by response word count. | `-fw 35` |
| `-ml` / `-fl` | Match / Filter by response line count. | `-fl 12` |
| `-t <threads>` | Set number of concurrent worker threads (default 40). | `-t 50` |
| `-x <proxy>` | Route fuzzing traffic through an HTTP proxy (e.g. Burp Suite). | `-x http://127.0.0.1:8080` |
