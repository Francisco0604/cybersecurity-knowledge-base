# 01 - Web Fundamentals

## Overview

Web Application Penetration Testing requires a deep understanding of how web applications function under the hood. This document covers core web architecture, the client-server model, browser security controls, and foundational security concepts.

---

## 1. Web Application Architecture

Modern web applications follow a multi-tier architecture:

```text
[ Client (Browser) ] <--- HTTP / HTTPS ---> [ Web / App Server ] <---> [ Database / Backend ]
```

1. **Client (Frontend)**: Runs in the user's browser (HTML, CSS, JavaScript). Renders the user interface and sends requests to the server.
2. **Server (Backend)**: Processes incoming HTTP requests, executes business logic, handles authentication, and queries database servers (e.g., Apache, Nginx, Node.js, Python, Java).
3. **Database (Data Layer)**: Stores persistent application data such as user credentials, profile information, and transactional data (e.g., PostgreSQL, MySQL, MongoDB).

---

## 2. The Web Request Lifecycle

When a user enters a URL into a web browser:

1. **DNS Resolution**: The browser queries DNS servers to map the domain name (e.g., `example.com`) to an IP address (e.g., `93.184.216.34`).
2. **TCP Handshake**: The browser establishes a TCP connection with the target server via a three-way handshake (`SYN` -> `SYN-ACK` -> `ACK`).
3. **TLS Negotiation (HTTPS)**: If HTTPS is used, the client and server negotiate encryption keys via TLS handshakes to establish a secure, encrypted tunnel.
4. **HTTP Request**: The browser sends an HTTP request containing headers, cookies, and parameters.
5. **Server Processing**: The backend server validates input, executes application logic, and queries databases if needed.
6. **HTTP Response**: The server returns an HTTP response containing status codes, headers, and the response body (HTML, JSON, XML).
7. **Browser Rendering**: The browser parses HTML/CSS, executes JavaScript, and renders the webpage.

---

## 3. Same-Origin Policy (SOP) & CORS

### Same-Origin Policy (SOP)
SOP is a fundamental browser security mechanism that restricts how a document or script loaded from one origin can interact with a resource from another origin.

An **Origin** is defined by three components:
- **Protocol** (e.g., `https://`)
- **Domain / Hostname** (e.g., `example.com`)
- **Port** (e.g., `:443`)

| URL Compared to `http://store.example.com/dir/page.html` | Outcome | Reason |
| :--- | :--- | :--- |
| `http://store.example.com/dir2/other.html` | **Allowed** | Same protocol, host, and port |
| `https://store.example.com/dir/page.html` | **Blocked** | Different protocol (`https` vs `http`) |
| `http://store.example.com:81/dir/page.html` | **Blocked** | Different port (`81` vs `80`) |
| `http://news.example.com/dir/page.html` | **Blocked** | Different domain (`news` vs `store`) |

### Cross-Origin Resource Sharing (CORS)
CORS is an HTTP-header-based mechanism that allows a server to explicitly indicate any origins other than its own from which a browser should permit loading resources.
- `Access-Control-Allow-Origin: https://trusted-site.com`
- Misconfigured CORS (e.g., `Access-Control-Allow-Origin: *` with `Access-Control-Allow-Credentials: true`) can lead to unauthorized data exposure.

---

## 4. Fundamental Security Concepts

* **Authentication vs. Authorization**:
  * **Authentication (AuthN)**: Verifying *who* a user is (e.g., passwords, multi-factor authentication).
  * **Authorization (AuthZ)**: Verifying *what* permissions an authenticated user has (e.g., access control levels, role-based access).
* **Statelessness & Sessions**: HTTP is stateless. Web applications use session identifiers (stored in cookies or tokens) to maintain user state across multiple requests.
* **Defense-in-Depth**: Layering multiple security controls (input validation, output encoding, least privilege, WAFs) to ensure resilience against attacks.
