# Cybersecurity Knowledge Base & Hands-On Security Labs

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Category: Cybersecurity](https://img.shields.io/badge/Category-Penetration%20Testing-blue.svg)]()

Designed and documented by **Francisco Elroy Afonso**  
*Aspiring Penetration Tester / Cybersecurity Analyst*

---

## 📌 Overview

Welcome to the **Cybersecurity Knowledge Base** repository! This repository serves as a comprehensive, structured technical reference and practical laboratory manual covering **Computer Networking**, **Offensive Security Tools**, **Web Application Vulnerabilities (OWASP Top 10)**, and **Hands-on Security Labs**.

It is designed to bridge theoretical network & web security concepts with practical vulnerability identification, traffic interception, and remediation methodologies.

---

## 📁 Repository Structure

```text
cybersecurity-knowledge-base/
│
├── README.md
│
├── docs/
│   ├── networking/
│   │   ├── 01-How-Websites-Work.md
│   │   ├── 02-DNS.md
│   │   ├── 03-DNS-Hierarchy.md
│   │   ├── 04-TCP-IP.md
│   │   ├── 05-HTTP.md
│   │   ├── 06-HTTPS-TLS.md
│   │   ├── 07-Headers.md
│   │   ├── 08-Cookies.md
│   │   └── 09-Sessions.md
│   │
│   ├── tools/
│   │   ├── Wireshark.md
│   │   ├── Burp-Suite.md
│   │   ├── Nmap.md
│   │   ├── Gobuster.md
│   │   └── FFUF.md
│   │
│   └── web-security/
│       ├── SQL-Injection.md
│       ├── XSS.md
│       ├── CSRF.md
│       ├── IDOR.md
│       └── SSRF.md
│
├── labs/
│   ├── Lab-01-TCP-Handshake.md
│   ├── Lab-02-DNS-Lookup.md
│   ├── Lab-03-HTTP-Request.md
│   ├── Lab-04-HTTPS-TLS.md
│   ├── Lab-05-Burp-Repeater.md
│   ├── Lab-06-Cookies.md
│   └── ...
│
├── screenshots/
│   ├── lab01-tcp-handshake.png
│   ├── lab02-dns-query.png
│   ├── lab03-http-request.png
│   ├── lab04-tls-client-hello.png
│   ├── lab04-tls-server-hello.png
│   └── lab04-tls-application-data.png
│
└── resources/
    ├── Cheat-Sheets.md
    ├── Interview-Questions.md
    └── Useful-Commands.md
```

---

## 📚 Knowledge Base Modules & Documentation

### 1. Networking Fundamentals ([`docs/networking/`](docs/networking/))
- [01-How-Websites-Work.md](docs/networking/01-How-Websites-Work.md) – End-to-end web communication flow.
- [02-DNS.md](docs/networking/02-DNS.md) – Domain Name System architecture & resolution process.
- [03-DNS-Hierarchy.md](docs/networking/03-DNS-Hierarchy.md) – Root servers, TLDs, and Authoritative Name Servers.
- [04-TCP-IP.md](docs/networking/04-TCP-IP.md) – OSI model, TCP/IP stack layers, and encapsulation.
- [05-HTTP.md](docs/networking/05-HTTP.md) – Client-server architecture, HTTP methods, and status codes.
- [06-HTTPS-TLS.md](docs/networking/06-HTTPS-TLS.md) – TLS/SSL handshakes, PKI, digital certificates, and encryption.
- [07-Headers.md](docs/networking/07-Headers.md) – HTTP request/response headers & client-side security policies (CSP, HSTS).
- [08-Cookies.md](docs/networking/08-Cookies.md) – HTTP cookies, lifecycle, and security flags (`HttpOnly`, `Secure`, `SameSite`).
- [09-Sessions.md](docs/networking/09-Sessions.md) – Stateful vs stateless sessions, JWTs, session hijacking, and fixation.

### 2. Security & Reconnaissance Tools ([`docs/tools/`](docs/tools/))
- [Wireshark.md](docs/tools/Wireshark.md) – Packet capture, display filters, and network stream analysis.
- [Burp-Suite.md](docs/tools/Burp-Suite.md) – Traffic interception proxy, Repeater, Intruder attack modes, and CA certificates.
- [Nmap.md](docs/tools/Nmap.md) – Host discovery, SYN stealth scanning, service versioning, and NSE scripts.
- [Gobuster.md](docs/tools/Gobuster.md) – Directory, subdomain, and virtual host brute-forcing.
- [FFUF.md](docs/tools/FFUF.md) – High-speed web fuzzing, parameter discovery, and filtering.

### 3. Web Application Security ([`docs/web-security/`](docs/web-security/))
- [SQL-Injection.md](docs/web-security/SQL-Injection.md) – In-band, Blind, and OOB SQLi exploitation & parameterized query fixes.
- [XSS.md](docs/web-security/XSS.md) – Reflected, Stored, and DOM-based XSS attack vectors & output encoding defenses.
- [CSRF.md](docs/web-security/CSRF.md) – Cross-Site Request Forgery mechanics, anti-CSRF tokens, and `SameSite` flags.
- [IDOR.md](docs/web-security/IDOR.md) – Insecure Direct Object References, horizontal/vertical privilege escalation.
- [SSRF.md](docs/web-security/SSRF.md) – Server-Side Request Forgery targeting cloud metadata (IMDS) & internal networks.

---

## 🧪 Hands-on Security Labs ([`labs/`](labs/))

- [Lab-01-TCP-Handshake.md](labs/Lab-01-TCP-Handshake.md) – Wireshark capture of TCP 3-Way Handshake (SYN, SYN-ACK, ACK).
- [Lab-02-DNS-Lookup.md](labs/Lab-02-DNS-Lookup.md) – Command-line DNS query tracing (`dig`, `nslookup`) & packet inspection.
- [Lab-03-HTTP-Request.md](labs/Lab-03-HTTP-Request.md) – Manual raw HTTP request crafting using `netcat` & `curl`.
- [Lab-04-HTTPS-TLS.md](labs/Lab-04-HTTPS-TLS.md) – Wireshark capture & TLS 1.3 handshake analysis (Client/Server Hello, SNI, Cipher Suites, Application Data).
- [Lab-05-Burp-Repeater.md](labs/Lab-05-Burp-Repeater.md) – Intercepting requests and parameter tampering in Burp Repeater.
- [Lab-06-Cookies.md](labs/Lab-06-Cookies.md) – Cookie security attribute auditing and privilege escalation testing.

---

## 🛠️ Resources & Cheat Sheets ([`resources/`](resources/))

- [Cheat-Sheets.md](resources/Cheat-Sheets.md) – High-yield syntax reference for Nmap, Burp, Gobuster, FFUF, and Web Payloads.
- [Interview-Questions.md](resources/Interview-Questions.md) – Technical interview Q&A covering networking, web security, and tools.
- [Useful-Commands.md](resources/Useful-Commands.md) – Penetration testing command-line reference.

---

## 👤 Author

**Francisco Elroy Afonso**  
*Aspiring Penetration Tester / Cybersecurity Analyst*

---

## 📄 License

This repository is licensed under the [MIT License](LICENSE).
