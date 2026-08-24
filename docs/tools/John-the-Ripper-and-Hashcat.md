# Offline Password Cracking: John the Ripper & Hashcat

Offline password cracking involves recovering plaintext passwords from captured cryptographic hashes, tokens, or digital signatures without interacting directly with the target server.

---

## 1. John the Ripper (JtR)

John the Ripper is a fast, versatile password cracker available across Unix, macOS, and Windows.

```text
[ Captured Hash / JWT / Ticket ] ──► [ John the Ripper Engine ] ◄── [ Wordlist + Rules ]
                                                 │
                                                 ▼
                                     [ Plaintext Credential ]
```

### 1.1 Cracking JWT Symmetric Secrets (HMAC-SHA256)
When a JWT uses HS256 with a weak signing secret:

```bash
# Crack JWT secret using a dictionary list
john --wordlist=jwt.secrets.list --format=HMAC-SHA256 jwt.txt

# Display recovered secret
john --show --format=HMAC-SHA256 jwt.txt
```

### 1.2 Cracking Linux / Shadow Hashes & Password Files
```bash
# Unshadow passwd and shadow files
unshadow /etc/passwd /etc/shadow > unshadowed.txt

# Execute dictionary attack
john --wordlist=/usr/share/wordlists/rockyou.txt unshadowed.txt
```

---

## 2. Hashcat: GPU-Accelerated Password Recovery

Hashcat is an advanced password recovery utility optimized for multi-threaded GPU computation (OpenCL / CUDA).

### 2.1 Essential Hashcat Modes

| Target Format / Hash Type | Hashcat Mode (`-m`) | Example Target |
| :--- | :--- | :--- |
| **MD5** | `-m 0` | Legacy web application hashes |
| **SHA-256** | `-m 1400` | Standard SHA256 hashes |
| **NTLM** | `-m 1000` | Windows local SAM / Active Directory hashes |
| **NetNTLMv2 / NTLMv2** | `-m 5600` | Hashes captured via Responder LLMNR poisoning |
| **Kerberos 5 TGS (Kerberoast)** | `-m 13100` | SPN service ticket hashes |
| **Kerberos 5 AS-REP (AS-REP Roast)** | `-m 18200` | Pre-authentication disabled tickets |
| **JWT (HMAC-SHA256)** | `-m 16500` | HS256 JWT tokens |

### 2.2 Attack Commands
```bash
# Crack NetNTLMv2 hash captured by Responder
hashcat -m 5600 -a 0 captured_ntlmv2.txt /usr/share/wordlists/rockyou.txt

# Crack Kerberoasting TGS ticket
hashcat -m 13100 -a 0 kerberoast_tickets.txt /usr/share/wordlists/rockyou.txt

# Crack NTLM password hash with rules
hashcat -m 1000 -a 0 ntlm_hashes.txt rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```
