# Cybersecurity Knowledge Base

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Category: Cybersecurity](https://img.shields.io/badge/Category-Penetration%20Testing-blue.svg)]()

Designed and documented by **Francisco Elroy Afonso**  
*Aspiring Penetration Tester*

---

## 📌 Overview

Welcome to the **Cybersecurity Knowledge Base**. This repository serves as a structured, core technical reference and study companion covering **computer networking fundamentals**, **web application security & modern authentication**, **enterprise Active Directory infrastructure**, and **offensive security tooling & packet analysis**.

The documentation is organized strictly by conceptual domains and practical testing tools, distilled from real-world labs, enterprise environment setups, and penetration testing assessments.

---

## 📁 Repository Structure

```text
cybersecurity-knowledge-base/
├── README.md
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
│   ├── web-security/
│   │   ├── Authentication-Security.md
│   │   ├── Session-Management-Security.md
│   │   ├── JWT-Security.md
│   │   ├── OAuth-Security.md
│   │   ├── MFA-Security.md
│   │   ├── Access-Control.md
│   │   ├── SQL-Injection.md
│   │   ├── XSS.md
│   │   ├── CSRF.md
│   │   └── SSRF.md
│   │
│   ├── infrastructure-security/
│   │   ├── 01-Active-Directory-Fundamentals.md
│   │   ├── 02-Active-Directory-Attacks-and-Defenses.md
│   │   └── 03-Virtual-Lab-Architecture.md
│   │
│   └── tools/
│       ├── Wireshark.md
│       ├── Burp-Suite.md
│       ├── Nmap.md
│       ├── Gobuster.md
│       ├── FFUF.md
│       ├── Hydra.md
│       ├── John-the-Ripper-and-Hashcat.md
│       ├── Responder.md
│       └── Docker-in-Pentesting.md
│
└── resources/
    ├── Glossary-and-Acronyms.md
    ├── Cheat-Sheets.md
    ├── Useful-Commands.md
    └── Interview-Questions.md
```

---

## 📚 Knowledge Base Modules

### 1. Networking Fundamentals ([`docs/networking/`](docs/networking/))
- [01-How-Websites-Work.md](docs/networking/01-How-Websites-Work.md) – End-to-end web communication and protocol stack flow.
- [02-DNS.md](docs/networking/02-DNS.md) – Domain Name System architecture & resolution process.
- [03-DNS-Hierarchy.md](docs/networking/03-DNS-Hierarchy.md) – Root name servers, TLDs, and authoritative name servers.
- [04-TCP-IP.md](docs/networking/04-TCP-IP.md) – OSI model, TCP/IP stack layers, and encapsulation.
- [05-HTTP.md](docs/networking/05-HTTP.md) – Client-server architecture, HTTP methods, and status codes.
- [06-HTTPS-TLS.md](docs/networking/06-HTTPS-TLS.md) – TLS/SSL handshakes, PKI, digital certificates, and encryption.
- [07-Headers.md](docs/networking/07-Headers.md) – HTTP request/response headers & client-side security policies (CSP, HSTS).
- [08-Cookies.md](docs/networking/08-Cookies.md) – HTTP cookies, lifecycle, and security flags (`HttpOnly`, `Secure`, `SameSite`).
- [09-Sessions.md](docs/networking/09-Sessions.md) – Stateful vs stateless sessions, JWTs, session hijacking, and fixation.

### 2. Web Application Security ([`docs/web-security/`](docs/web-security/))
- [Authentication-Security.md](docs/web-security/Authentication-Security.md) – Account enumeration, verbose errors, timing attacks, password reset logic, and HTTP Basic Auth.
- [Session-Management-Security.md](docs/web-security/Session-Management-Security.md) – Client-side role storage risks, session fixation, session hijacking, and cookie attributes.
- [JWT-Security.md](docs/web-security/JWT-Security.md) – Token structure, signature bypasses, `alg: none`, weak secret cracking, and algorithm confusion.
- [OAuth-Security.md](docs/web-security/OAuth-Security.md) – Authorization Code vs Implicit flows, redirect URI validation, OAuth CSRF, state parameters, and PKCE.
- [MFA-Security.md](docs/web-security/MFA-Security.md) – Authentication factors, OTP leakage in API responses, auth state logic flaws, and rate limiting.
- [Access-Control.md](docs/web-security/Access-Control.md) – Access control models, IDOR, vertical/horizontal privilege escalation, and method bypasses.
- [SQL-Injection.md](docs/web-security/SQL-Injection.md) – In-band, Blind, and Out-of-Band SQLi exploitation & parameterized queries.
- [XSS.md](docs/web-security/XSS.md) – Reflected, Stored, and DOM-based Cross-Site Scripting attack vectors & context-aware encoding.
- [CSRF.md](docs/web-security/CSRF.md) – Cross-Site Request Forgery mechanics, anti-CSRF tokens, and `SameSite` flags.
- [SSRF.md](docs/web-security/SSRF.md) – Server-Side Request Forgery targeting cloud metadata (IMDS) & internal network pivoting.

### 3. Infrastructure & Active Directory Security ([`docs/infrastructure-security/`](docs/infrastructure-security/))
- [01-Active-Directory-Fundamentals.md](docs/infrastructure-security/01-Active-Directory-Fundamentals.md) – Domain Controllers, AD DS, Forests, Organizational Units (OUs), SPNs, and DNS.
- [02-Active-Directory-Attacks-and-Defenses.md](docs/infrastructure-security/02-Active-Directory-Attacks-and-Defenses.md) – LLMNR/NBT-NS poisoning, Kerberoasting, AS-REP Roasting, Pass-the-Hash, and enterprise hardening.
- [03-Virtual-Lab-Architecture.md](docs/infrastructure-security/03-Virtual-Lab-Architecture.md) – Hypervisor network isolation, VMnet subnets, baseline VM snapshots, and Docker target deployment.

### 4. Security & Reconnaissance Tools ([`docs/tools/`](docs/tools/))
- [Wireshark.md](docs/tools/Wireshark.md) – Packet analysis, TCP 3-way handshakes, DNS inspection, TLS 1.3 negotiation, and display filters.
- [Burp-Suite.md](docs/tools/Burp-Suite.md) – Proxy interception, Repeater, Intruder, target scope, and manual testing workflows.
- [Nmap.md](docs/tools/Nmap.md) – Host discovery, SYN stealth scanning, service versioning, and NSE scripts.
- [Gobuster.md](docs/tools/Gobuster.md) – Directory, DNS subdomain, and virtual host brute-forcing.
- [FFUF.md](docs/tools/FFUF.md) – High-speed web fuzzing, parameter discovery, and filtering.
- [Hydra.md](docs/tools/Hydra.md) – Multithreaded network login brute-forcing across HTTP Basic Auth, SSH, and FTP.
- [John-the-Ripper-and-Hashcat.md](docs/tools/John-the-Ripper-and-Hashcat.md) – Offline dictionary attacks against JWT secrets, NetNTLMv2, and Kerberos tickets.
- [Responder.md](docs/tools/Responder.md) – LLMNR, NBT-NS, and MDNS broadcast poisoning for NetNTLMv2 credential capture.
- [Docker-in-Pentesting.md](docs/tools/Docker-in-Pentesting.md) – Deploying and isolating vulnerable web practice targets (OWASP Juice Shop, DVWA).

### 5. Resources & References ([`resources/`](resources/))
- [Glossary-and-Acronyms.md](resources/Glossary-and-Acronyms.md) – A-to-Z reference of cybersecurity acronyms and technical definitions.
- [Cheat-Sheets.md](resources/Cheat-Sheets.md) – High-yield command syntax reference for Nmap, Burp, FFUF, Hydra, Responder, and Wireshark.
- [Useful-Commands.md](resources/Useful-Commands.md) – Categorized command-line reference for scanning, web fuzzing, hash cracking, and lab administration.
- [Interview-Questions.md](resources/Interview-Questions.md) – Technical interview questions and structured answers across networking, web security, and infrastructure.

---

## 👤 Author

**Francisco Elroy Afonso**  
*Aspiring Penetration Tester*

---

## 📄 License

This repository is licensed under the [MIT License](LICENSE).
