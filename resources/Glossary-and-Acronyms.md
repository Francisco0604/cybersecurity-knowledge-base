# Cybersecurity Glossary & Acronyms Reference

A quick-reference dictionary of cybersecurity acronyms, networking protocols, authentication terms, and penetration testing concepts encountered throughout this knowledge base.

---

## 🔤 A – Z Acronyms & Definitions

### A
* **ABAC (Attribute-Based Access Control):** An authorization model that evaluates dynamic attributes (user, resource, environment) to grant access.
* **AD (Active Directory):** Microsoft's centralized directory service providing authentication, policy enforcement, and resource management across Windows domains.
* **AD DS (Active Directory Domain Services):** The core server role in Windows Server that manages directory database objects (`ntds.dit`).
* **ADUC (Active Directory Users and Computers):** The Microsoft Management Console (`dsa.msc`) used to manage domain users, groups, computers, and OUs.
* **API (Application Programming Interface):** A set of rules and protocols enabling software applications to communicate with each other.
* **ARP (Address Resolution Protocol):** A Layer 2 protocol used to map an IPv4 address to a physical MAC address on a local network.
* **AS-REP (Authentication Service Response):** A Kerberos message returned by the Key Distribution Center (KDC) containing session keys and TGTs; targeted during AS-REP Roasting.
* **ATO (Account Takeover):** An attack where an unauthorized party gains full control over a legitimate user's account.

---

### B
* **Base64:** A binary-to-text encoding scheme that represents binary data in an ASCII string format. **Base64 is NOT encryption.**
* **BPF (Berkeley Packet Filter):** A kernel-level packet filtering syntax used by capture drivers (Npcap, tcpdump) to filter traffic before it reaches Wireshark memory.

---

### C
* **CA (Certificate Authority):** A trusted entity that issues digital certificates to verify the identity of websites during TLS handshakes.
* **CAPTCHA:** Automated challenge-response test designed to determine whether a user is human, preventing automated brute-force attacks.
* **CIDR (Classless Inter-Domain Routing):** A method for allocating IP addresses and IP routing (e.g., `/24` represents a 255.255.255.0 subnet).
* **CLI (Command-Line Interface):** A text-based user interface used to run commands, scripts, and administrative tools.
* **CSP (Content Security Policy):** An HTTP response header that restricts the resources (scripts, images, styles) a browser is allowed to load, mitigating XSS attacks.
* **CSRF (Cross-Site Request Forgery):** A web vulnerability that tricks an authenticated victim into submitting unauthorized state-changing requests to a target application.
* **CSPRNG (Cryptographically Secure Pseudorandom Number Generator):** An algorithm producing numbers with high entropy suitable for cryptographic keys and session tokens.
* **CVSS (Common Vulnerability Scoring System):** A standardized framework for assessing the severity and impact of security vulnerabilities (Scale: 0.0 – 10.0).

---

### D
* **DAC (Discretionary Access Control):** An access control model where the owner of a resource determines permissions.
* **DC (Domain Controller):** The central server in an Active Directory network responsible for authenticating users and enforcing domain security policies.
* **DHCP (Dynamic Host Configuration Protocol):** A network management protocol used to dynamically assign IP addresses, subnet masks, and gateways to network devices.
* **DNS (Domain Name System):** The hierarchical naming system that translates human-readable domain names (e.g., `example.com`) into IP addresses.
* **DOM (Document Object Model):** A hierarchical programming interface that represents HTML and XML documents in the browser.
* **DSRM (Directory Services Restore Mode):** A specialized safe-mode boot option for Windows Server Domain Controllers used for AD database recovery.
* **DVWA (Damn Vulnerable Web Application):** An intentionally vulnerable PHP/MySQL web application used for legal penetration testing practice.

---

### E
* **Ephemeral Port:** A temporary, short-lived port allocated automatically by a client operating system for outbound network communication (typically ports 49152–65535).
* **eQTL (Expression Quantitative Trait Loci):** Genomic genomic loci that explain variation in expression levels of mRNAs.
* **EXP (Expiration Claim):** A standard JWT claim (`exp`) defining the UNIX timestamp after which the token must be rejected.

---

### F
* **FFUF (Fast Fuzzing):** A high-speed, command-line web fuzzing tool written in Go used for directory and parameter discovery.
* **FIDO (Fast IDentity Online):** An open standard for passwordless and multi-factor authentication (e.g., FIDO2 / WebAuthn).
* **FQDN (Fully Qualified Domain Name):** A complete domain name that specifies the exact location of a host in the DNS hierarchy (e.g., `dc01.corp.local`).

---

### G
* **GPO (Group Policy Object):** A collection of settings in Active Directory that centrally manage operating system configurations and security policies on domain endpoints.
* **GUID (Globally Unique Identifier):** A 128-bit integer number used as a unique object identifier in software and Active Directory.

---

### H
* **HMAC (Hash-based Message Authentication Code):** A specific type of message authentication code involving a cryptographic hash function and a secret key.
* **HSTS (HTTP Strict Transport Security):** An HTTP response header instructing browsers to only communicate with a site via encrypted HTTPS connections.
* **HTML (HyperText Markup Language):** The standard markup language for documents designed to be displayed in a web browser.
* **HTTP (HyperText Transfer Protocol):** The stateless application protocol used for transmitting web resources over TCP port 80.
* **HTTPS (HTTP Secure):** Encrypted HTTP traffic running over TLS/SSL on TCP port 443.

---

### I
* **ICMP (Internet Control Message Protocol):** A network layer protocol used by devices to send error messages and operational information (e.g., `ping` and `traceroute`).
* **IDOR (Insecure Direct Object Reference):** A broken access control flaw where an application uses user-supplied input to access database objects directly without verifying authorization.
* **IMDS (Instance Metadata Service):** A local HTTP service (`169.254.169.254`) in cloud environments that provides instance metadata, commonly targeted in SSRF attacks.
* **IP (Internet Protocol):** The principal network layer communications protocol in the Internet protocol suite for routing data across network boundaries.
* **IPv4 / IPv6:** 32-bit (IPv4) and 128-bit (IPv6) addressing schemes for network device identification.

---

### J
* **JTC (JSON Web Token):** Standard format for securely transmitting claims between parties as a JSON object (RFC 7519).
* **Juice Shop (OWASP):** An intentionally insecure web application written in Node.js, Express, and Angular designed for security training.

---

### K
* **KDC (Key Distribution Center):** The Kerberos service running on a Domain Controller responsible for issuing TGTs and TGS tickets.
* **Kerberos:** The default, ticket-based network authentication protocol in Active Directory providing mutual authentication over port 88.

---

### L
* **LAN (Local Area Network):** A computer network that connects computers within a limited geographical area (home, office, lab).
* **LLMNR (Link-Local Multicast Name Resolution):** A legacy protocol that allows computers on a local network to perform name resolution when DNS is unavailable (UDP port 5355).

---

### M
* **MAC (Mandatory Access Control):** A strict access control model where permissions are dictated centrally based on data security classifications.
* **MAC Address (Media Access Control):** A unique 48-bit physical hardware identifier assigned to a network interface controller (NIC).
* **MFA (Multi-Factor Authentication):** An authentication method requiring two or more distinct factor categories (Knowledge, Possession, Inherence, Location, Behavior).
* **MitM (Man-in-the-Middle):** An attack where an adversary secretly intercepts and alters communication between two parties who believe they are communicating directly.
* **MSSQL (Microsoft SQL Server):** A relational database management system developed by Microsoft.
* **MTU (Maximum Transmission Unit):** The largest size packet or frame (typically 1500 bytes on Ethernet) that can be sent in a single network layer transaction.

---

### N
* **NAT (Network Address Translation):** A method of mapping multiple local private IP addresses to a single public IP address before transferring information.
* **NBT-NS (NetBIOS Name Service):** A legacy Windows protocol used for resolving NetBIOS computer names on a local subnet (UDP port 137).
* **NIC (Network Interface Card):** A computer hardware component that connects a computer to a computer network.
* **Nmap (Network Mapper):** An open-source network scanner used for host discovery, port scanning, OS detection, and vulnerability assessments.
* **NSE (Nmap Scripting Engine):** A powerful scripting extension allowing Nmap users to automate vulnerability scanning and network discovery.
* **NTLM / NTLMv2:** Microsoft's challenge-response authentication protocol used when Kerberos is unavailable.

---

### O
* **OAuth 2.0:** An open authorization framework (RFC 6749) allowing third-party applications limited access to user resources without sharing passwords.
* **OOB (Out-of-Band):** Exploitation techniques that trigger a secondary channel/protocol (e.g., DNS, HTTP) to exfiltrate data when direct responses are blind.
* **OSI Model:** The 7-layer Open Systems Interconnection conceptual model (Physical, Data Link, Network, Transport, Session, Presentation, Application).
* **OTP (One-Time Password):** A temporary password valid for only one login session or transaction.
* **OU (Organizational Unit):** A subdivision container in Active Directory used to organize domain objects and apply Group Policies.
* **OWASP (Open Web Application Security Project):** A non-profit foundation dedicated to improving software security, famous for the **OWASP Top 10**.

---

### P
* **PCAP (Packet Capture):** The standard file format for storing captured network packets (used by Wireshark, tcpdump).
* **PEH (Practical Ethical Hacker):** A comprehensive hands-on cybersecurity certification provided by TCM Security.
* **PKCE (Proof Key for Code Exchange):** An extension to OAuth 2.0 designed to prevent authorization code interception attacks on public clients.
* **PKI (Public Key Infrastructure):** The framework of roles, policies, hardware, and software needed to create, manage, and revoke digital certificates.
* **PoC (Proof of Concept):** Demonstration code or steps proving that a security vulnerability is exploitable in a practical environment.
* **PtH (Pass-the-Hash):** An attack technique where an adversary uses an intercepted NTLM hash to authenticate to remote services without cracking the plaintext password.

---

### R
* **RBAC (Role-Based Access Control):** An authorization model that assigns permissions to specific organizational roles rather than individual users.
* **RDP (Remote Desktop Protocol):** Microsoft proprietary protocol providing graphical remote management over TCP port 3389.
* **RFC (Request for Comments):** Formal documents published by the Internet Engineering Task Force (IETF) specifying internet standards and protocols.
* **RSA:** An asymmetric public-key cryptosystem widely used for secure data transmission and digital signatures.
* **RST (Reset Flag):** A TCP control flag indicating that the connection must be immediately terminated or rejected.

---

### S
* **SAM (Security Account Manager):** The local Windows database storing local user account passwords and hashes.
* **SMB (Server Message Block):** A network file sharing protocol used in Windows networks (TCP port 445).
* **SMB Signing:** A security feature that cryptographically signs SMB packets, preventing tampering and NTLM relay attacks.
* **SNI (Server Name Indication):** A TLS extension indicating which hostname the client is attempting to connect to during the initial `Client Hello`.
* **SPN (Service Principal Name):** A unique identifier of a service instance associated with a Windows domain service account.
* **SQL (Structured Query Language):** The standard language used for managing data held in a relational database management system.
* **SQLi (SQL Injection):** A vulnerability where untrusted user input alters backend database queries, allowing unauthorized data access or command execution.
* **SRV Record (Service Record):** A DNS record specifying the hostname and port number of servers for specified services (essential for Active Directory).
* **SSRF (Server-Side Request Forgery):** A flaw allowing an attacker to induce the backend web server to make unintended HTTP requests to internal or cloud resources.
* **SYN (Synchronize Flag):** The initial TCP flag transmitted by a client to initiate the three-way handshake.

---

### T
* **TCP (Transmission Control Protocol):** A reliable, connection-oriented transport layer protocol utilizing sequence numbers and acknowledgements.
* **TCP/IP:** The conceptual communications model and protocol suite that powers the global internet.
* **TGS (Ticket Granting Service):** A Kerberos ticket granting access to a specific network service (e.g., MSSQL, SMB).
* **TGT (Ticket Granting Ticket):** An initial Kerberos ticket issued by the KDC proving identity to request subsequent service tickets.
* **TLS (Transport Layer Security):** The cryptographic protocol providing privacy and data integrity over computer networks (successor to SSL).
* **TLD (Top-Level Domain):** The highest level in the hierarchical DNS system (`.com`, `.org`, `.gov`, `.local`).
* **TOTP (Time-Based One-Time Password):** An algorithm that generates a one-time password based on the current time and a shared secret key (RFC 6238).
* **TTL (Time-To-Live):** A mechanism that limits the lifespan of data or DNS records in a network to prevent endless routing loops and govern cache expiration.

---

### U
* **UDP (User Datagram Protocol):** A lightweight, connectionless transport layer protocol with no handshake or delivery guarantees (used by DNS, DHCP, streaming).
* **URI / URL:** Uniform Resource Identifier / Uniform Resource Locator; string specifying the address of a web resource.

---

### V
* **VAPT (Vulnerability Assessment & Penetration Testing):** A comprehensive security auditing process identifying and exploiting vulnerabilities to assess organizational risk.
* **VHost (Virtual Host):** A method for hosting multiple domain names on a single physical web server.
* **VM (Virtual Machine):** An emulation of a computer system that provides the functionality of a physical computer.
* **VMnet:** A virtual network adapter created by VMware hypervisors (e.g., `VMnet1` Host-Only, `VMnet8` NAT).

---

### X
* **XHR (XMLHttpRequest):** A browser API object used to exchange data with a web server behind the scenes without reloading the web page.
* **XSS (Cross-Site Scripting):** A client-side code injection vulnerability where malicious JavaScript executes in the context of an unsuspecting victim's browser session.
