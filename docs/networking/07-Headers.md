# HTTP Headers

## Overview

HTTP headers are key-value pairs sent in both HTTP requests and HTTP responses. They provide essential metadata about the request, the client, the server, the connection, the message body, authentication credentials, and client-side security policies.

Headers follow the standard format:

```http
Header-Name: Header-Value
```

Basic exchange:

```text
Client                                  Server
  │                                       │
  │── Request (Method, URI + Headers) ───►│
  │                                       │
  │◄── Response (Status + Headers) ───────│
```

Headers are separated from the message body by a single blank line (Carriage Return + Line Feed: `\r\n\r\n`).

---

# 1. Request Headers

Request headers are sent by the client to give the server context about the requested resource, the client's capabilities, or identity.

Example request:

```http
GET /api/profile HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
Accept: application/json, text/plain, */*
Accept-Language: en-US,en;q=0.9
Accept-Encoding: gzip, deflate, br
Authorization: Bearer eyJhbGciOiJIUzI1Ni...
Cookie: session_id=abc123xyz; userRole=student
Connection: keep-alive
```

---

## Key Request Headers

### `Host`
Specifies the domain name of the server and the TCP port number.

```http
Host: example.com
```

* **Purpose:** Enables Virtual Hosting (VHosting), allowing multiple distinct domain names to be hosted on the same physical IP address.
* **Security Consideration:** If an application relies on the `Host` header to construct reset password links or server redirects, an attacker can manipulate it to perform **Host Header Injection** or **Password Reset Poisoning**.

---

### `User-Agent`
Identifies the client software, browser engine, operating system, and version making the request.

```http
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) Firefox/128.0
```

* **Purpose:** Allows web servers to customize content (e.g., desktop vs. mobile layouts) or log client telemetry.
* **Security Consideration:** Can be arbitrarily modified by attackers and automated testing tools (e.g., `curl`, `Nmap`, `sqlmap`).

---

### `Authorization`
Transmits client authentication credentials to the server.

```http
# HTTP Basic Authentication (Base64 encoded username:password)
Authorization: Basic YWRtaW46cGFzc3dvcmQxMjM=

# Bearer Token (e.g., JSON Web Token / OAuth Access Token)
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

* **Purpose:** Authenticates API calls and protected web endpoints.
* **Security Consideration:** Basic authentication headers transmit plaintext credentials encoded in Base64. They must always be protected with HTTPS/TLS.

---

### `Cookie`
Sends previously stored HTTP cookies from the browser back to the server.

```http
Cookie: PHPSESSID=d3b07384d113edec49eaa6238ad5ff00; theme=dark
```

* **Purpose:** Enables stateful session tracking across stateless HTTP requests.

---

### `Referer` & `Origin`
Indicates the URL of the previous web page from which a link was followed or a cross-origin request was initiated.

```http
Referer: https://example.com/login.php
Origin: https://example.com
```

* **`Origin`:** Sent in CORS and state-changing requests (`POST`, `PUT`, `DELETE`); contains only the scheme, host, and port (no path).
* **`Referer`:** Contains the full source URL including query parameters (unless restricted by `Referrer-Policy`).
* **Security Consideration:** Applications should not rely solely on the `Referer` header for access control or CSRF validation, as headers can be omitted or manipulated.

---

### `Content-Type` & `Content-Length`
Describes the media type and byte size of the request or response body.

```http
Content-Type: application/json
Content-Length: 42
```

Common content types include:
* `application/x-www-form-urlencoded` (Standard HTML form submissions)
* `multipart/form-data` (File uploads and binary data)
* `application/json` (REST APIs and modern web applications)

---

### Forwarded & Routing Headers
Headers added by reverse proxies, load balancers, and API gateways:

```http
X-Forwarded-For: 203.0.113.195
X-Forwarded-Host: example.com
X-Original-URL: /admin/dashboard
X-Rewrite-URL: /admin/dashboard
```

* **Security Consideration:** Misconfigured backend servers may trust `X-Original-URL` or `X-Rewrite-URL` to bypass frontend access control filters or trust `X-Forwarded-For` to bypass IP-based whitelisting.

---

# 2. Response Headers

Response headers are sent by the server to provide status metadata, caching policies, server information, and security instructions to the browser.

Example response:

```http
HTTP/1.1 200 OK
Date: Mon, 24 Aug 2026 12:00:00 GMT
Server: Apache/2.4.52 (Ubuntu)
Set-Cookie: session_id=abc123xyz; Path=/; Secure; HttpOnly; SameSite=Lax
Content-Type: text/html; charset=UTF-8
Content-Length: 1024
Cache-Control: no-cache, no-store, must-revalidate
Strict-Transport-Security: max-age=31536000; includeSubDomains
Content-Security-Policy: default-src 'self'
```

---

## Key Response Headers

### `Set-Cookie`
Directs the browser to store a cookie and return it in subsequent requests.

```http
Set-Cookie: session_id=d3b07384...; Path=/; Secure; HttpOnly; SameSite=Strict
```

---

### `Location`
Used in `3xx` redirection responses (e.g., `301 Moved Permanently`, `302 Found`, `303 See Other`) to indicate the target URL where the client should navigate.

```http
HTTP/1.1 302 Found
Location: /dashboard.php
```

---

### `WWW-Authenticate`
Sent in `401 Unauthorized` responses to indicate the authentication scheme supported by the server.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Basic realm="Restricted Area"
```

---

# 3. HTTP Security Headers

Security headers instruct client browsers to activate built-in defensive security controls:

```text
┌─────────────────────────────────────────────────────────────┐
│ 1. Content-Security-Policy (CSP)                            │
│ Mitigates XSS and data injection attacks                    │
├─────────────────────────────────────────────────────────────┤
│ 2. Strict-Transport-Security (HSTS)                         │
│ Enforces HTTPS-only communication                           │
├─────────────────────────────────────────────────────────────┤
│ 3. X-Frame-Options                                          │
│ Protects against Clickjacking (UI Redressing)               │
├─────────────────────────────────────────────────────────────┤
│ 4. X-Content-Type-Options                                   │
│ Prevents MIME-sniffing attacks (nosniff)                    │
├─────────────────────────────────────────────────────────────┤
│ 5. Referrer-Policy                                          │
│ Controls sensitive URL leakage in Referer headers           │
└─────────────────────────────────────────────────────────────┘
```

---

## 3.1 `Content-Security-Policy` (CSP)
Restricts which domain sources are permitted to execute scripts, load styles, images, fonts, or frames:

```http
Content-Security-Policy: default-src 'self'; script-src 'self' https://trustedscripts.com; object-src 'none';
```

* **Impact:** Prevents malicious injected JavaScript (XSS) from executing or exfiltrating data to untrusted attacker domains.

---

## 3.2 `Strict-Transport-Security` (HSTS)
Forces the browser to always use HTTPS instead of plaintext HTTP:

```http
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
```

* **Impact:** Defeats SSL-stripping attacks and prevents users from inadvertently transmitting credentials over plaintext HTTP.

---

## 3.3 `X-Frame-Options`
Controls whether a webpage can be embedded inside an `<iframe>`, `<frame>`, or `<object>`:

```http
# Disallow framing entirely
X-Frame-Options: DENY

# Allow framing only by the same origin
X-Frame-Options: SAMEORIGIN
```

* **Impact:** Mitigates **Clickjacking** attacks where an attacker overlays a transparent victim site over a malicious page.

---

## 3.4 `X-Content-Type-Options`
Prevents browsers from MIME-sniffing a response away from the declared `Content-Type`:

```http
X-Content-Type-Options: nosniff
```

* **Impact:** Prevents browsers from executing user-uploaded image files or text files as executable JavaScript or HTML.

---

# 4. Header-Based Attack Vectors

```text
Header Attack Vectors
├── 1. Host Header Injection (Password reset poisoning & cache poisoning)
├── 2. HTTP Request Smuggling (Discrepancies in Content-Length vs. Transfer-Encoding)
├── 3. Header / CRLF Injection (Injecting malicious \r\n characters into responses)
└── 4. Access Control Bypasses (Spoofing X-Original-URL, X-Rewrite-URL, or Referer)
```

---

# 5. Interview Questions

### What is the purpose of the `Host` header?
The `Host` header specifies the domain name of the requested resource. It is required in HTTP/1.1 to enable Virtual Hosting, allowing multiple websites to be served from a single web server and IP address.

### How does `Content-Security-Policy` (CSP) mitigate Cross-Site Scripting (XSS)?
CSP allows website administrators to declare approved sources of executable scripts. Even if an attacker successfully injects a `<script>` tag into HTML, the browser will refuse to execute the script or connect to unauthorized external domains if it violates the CSP whitelist.

### Why is `X-Content-Type-Options: nosniff` important?
Without `nosniff`, browsers may inspect the content of a file (MIME-sniffing) and execute it as HTML or JavaScript even if the server declared it as an image (`Content-Type: image/png`). An attacker could upload a malicious script disguised as an image to achieve XSS.

---

# 6. Lesson Summary

- HTTP headers provide structured metadata for requests and responses.
- Request headers describe client identity, supported formats, and authentication credentials.
- Response headers provide status details, redirection targets, and cookie directives.
- Security headers (CSP, HSTS, X-Frame-Options, nosniff) enforce robust client-side protections.
- Headers are untrusted inputs when read by servers and must be validated to prevent injection and routing bypasses.
