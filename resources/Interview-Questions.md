# Technical Cybersecurity & Penetration Testing Interview Questions

A comprehensive collection of core technical interview questions and structured answers covering computer networking, web application security, Active Directory, modern authentication, and testing tools.

---

## 1. Computer Networking & Protocols

### Q1: Can you explain the TCP Three-Way Handshake step by step?
**Answer:**
TCP establishes a reliable, bidirectional connection using three packets:
1. **SYN (Synchronize):** The client sends a packet with the `SYN` flag set and an Initial Sequence Number (`Seq = X`) to request a connection.
2. **SYN-ACK:** The server responds with `SYN` and `ACK` flags set, establishing its own sequence number (`Seq = Y`) and acknowledging the client's SYN (`Ack = X + 1`).
3. **ACK:** The client sends an `ACK` packet (`Seq = X + 1`, `Ack = Y + 1`), completing connection establishment. Application data can now flow.

### Q2: What is the difference between TCP and UDP?
**Answer:**
- **TCP (Transmission Control Protocol):** Connection-oriented, reliable, guarantees packet delivery and ordering through sequence numbers, acknowledgements, and retransmissions. Used for HTTP/HTTPS, SSH, FTP.
- **UDP (User Datagram Protocol):** Connectionless, lightweight, low-overhead, does not guarantee delivery or packet ordering. Used for DNS queries, DHCP, video streaming, VoIP.

### Q3: How does HTTPS protect HTTP traffic?
**Answer:**
HTTPS encapsulates HTTP application traffic inside a **Transport Layer Security (TLS)** session (typically over TCP port 443). TLS uses asymmetric cryptography (PKI digital certificates) to authenticate the server and negotiate shared symmetric session keys, which are then used to encrypt all HTTP headers, methods, URLs, and payloads with high-speed symmetric ciphers (e.g., AES-GCM, ChaCha20-Poly1305).

---

## 2. Web Application Security & OWASP Top 10

### Q4: What is the difference between Authentication and Authorization?
**Answer:**
- **Authentication (AuthN):** Confirms *who* a user is (identity verification via passwords, MFA, biometrics).
- **Authorization (AuthZ):** Determines *what* an authenticated user is permitted to do (access control via RBAC, ABAC, permission checks).
- *HTTP Status codes:* `401 Unauthorized` reflects authentication failures; `403 Forbidden` reflects authorization/permission failures.

### Q5: What is an Insecure Direct Object Reference (IDOR)?
**Answer:**
IDOR occurs when an application uses user-supplied input (such as database primary keys, account numbers, or filenames) to access objects directly without performing server-side authorization checks to verify whether the requesting user owns or has permission to access that object.

### Q6: How do you prevent SQL Injection?
**Answer:**
The primary defense against SQL Injection is **Parameterized Queries (Prepared Statements)**. Parameterization separates SQL code from user-supplied data: the database treats user input strictly as literal values rather than executable SQL commands, preventing SQL syntax injection regardless of the input contents.

### Q7: What are the three types of Cross-Site Scripting (XSS)?
**Answer:**
1. **Reflected XSS:** The malicious payload is delivered in the immediate HTTP request (e.g., in a URL parameter) and reflected by the server directly into the immediate response.
2. **Stored (Persistent) XSS:** The malicious payload is stored in the backend database (e.g., comments, forum posts) and executed in the browsers of all victims who view the page.
3. **DOM-Based XSS:** The vulnerability exists entirely on the client side; JavaScript reads data from an untrusted source (e.g., `location.search`) and writes it insecurely into an execution sink (e.g., `innerHTML`, `eval()`).

### Q8: What is the difference between SOP and CORS, and how do attackers exploit CORS misconfigurations?
**Answer:**
- **Same-Origin Policy (SOP):** A browser-enforced security control that restricts how scripts on one origin interact with resources on another origin.
- **Cross-Origin Resource Sharing (CORS):** A server-controlled mechanism that provides explicit exceptions to SOP via HTTP response headers (e.g., `Access-Control-Allow-Origin`).
- **Core Principle:** CORS does *not* stop the server from processing cross-origin requests; it controls whether client-side JavaScript is allowed to read the response.
- **Key Misconfigurations:**
  1. **Arbitrary Origin Reflection:** Dynamically echoing the client's `Origin` header into `ACAO` along with `Access-Control-Allow-Credentials: true`.
  2. **Bad Regex / Substring Validation:** Using unanchored pattern checks that permit attacker domains (e.g., `trusted.com.attacker.com` or `attackertrusted.com`).
  3. **Null Origin Trust:** Trusting `Access-Control-Allow-Origin: null`, which can be weaponized using sandboxed `<iframe>` elements or `data:` URIs.

---

## 3. Modern Authentication: JWT, OAuth & MFA

### Q9: Are JSON Web Tokens (JWTs) encrypted by default?
**Answer:**
No. JWTs are **encoded using Base64URL, not encrypted**. Anyone who inspects the token can decode the header and payload. The signature provides integrity verification (proving the token was not altered), but does not provide confidentiality. Sensitive data must never be placed in JWT claims.

### Q10: What is the `alg: none` vulnerability in JWTs?
**Answer:**
The JWT specification defines `none` for unsigned tokens. If a backend server improperly accepts `alg: none`, an attacker can forge arbitrary claims (such as elevating privileges to admin) and remove the signature part. The server processes the forged token as valid.

### Q11: What is the role of the `state` parameter in OAuth 2.0?
**Answer:**
The `state` parameter is an unguessable cryptographic token generated by the client and sent in the authorization request. When the authorization server redirects back with the authorization code, it returns the same `state` value. The client verifies this value matches the original request, preventing **OAuth CSRF** attacks where an attacker tricks a victim into linking the attacker's OAuth account.

### Q12: What are the primary authentication factor categories in MFA?
**Answer:**
1. **Something You Know:** Passwords, PINs.
2. **Something You Have:** Mobile phone (SMS/Authenticator), Hardware security key (YubiKey).
3. **Something You Are:** Biometrics (fingerprint, face recognition).
4. **Somewhere You Are:** Geolocation / IP range.
5. **Something You Do:** Behavioral dynamics.

---

## 4. Enterprise Infrastructure & Active Directory

### Q13: What is LLMNR / NBT-NS Poisoning and how is it exploited?
**Answer:**
When a Windows endpoint fails to resolve a hostname via DNS, it broadcasts LLMNR (UDP 5355) and NBT-NS (UDP 137) queries across the local subnet. An attacker running a tool like **Responder** answers the broadcast claiming to be that host, requests NTLM authentication, and captures the victim's **NetNTLMv2 password hash** for offline cracking.

### Q14: What is Kerberoasting?
**Answer:**
Kerberoasting is a post-exploitation attack where an authenticated domain user requests Kerberos Ticket Granting Service (TGS) tickets for service accounts associated with registered Service Principal Names (SPNs). The ticket is encrypted with the service account's NTLM password hash; the attacker extracts the ticket from memory and cracks the password offline.

---

## 5. Security Tools & Practical Operations

### Q15: What is the difference between a Capture Filter and a Display Filter in Wireshark?
**Answer:**
- **Capture Filter (BPF):** Applied before capturing; packets that do not match are discarded at the driver level and never stored in memory.
- **Display Filter:** Applied after capturing; all packets remain in memory, but non-matching packets are hidden from the user interface.

### Q16: What is the difference between Nmap `-sS` and `-sT` scans?
**Answer:**
- **`-sS` (SYN Stealth Scan):** Sends a SYN packet. If the port is open, the target replies with SYN-ACK; Nmap immediately sends RST to tear down the connection before it is established. It is fast and stealthy.
- **`-sT` (TCP Connect Scan):** Completes the full TCP 3-way handshake via the operating system network stack. It is slower and more easily logged by target firewalls and application servers.
