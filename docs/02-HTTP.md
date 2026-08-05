# 02 - HTTP Protocol Security & Structure

## Overview

Hypertext Transfer Protocol (HTTP) is the application-layer protocol used to transmit web data. Understanding HTTP request and response structures, methods, status codes, headers, and session management is essential for inspecting and manipulating web traffic during penetration tests.

---

## 1. HTTP Request Structure

An HTTP request consists of three main components:

```http
POST /login.php HTTP/1.1
Host: target-app.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
Content-Type: application/x-www-form-urlencoded
Content-Length: 35
Cookie: sessionid=xyz123456789

username=admin&password=Password123!
```

1. **Request Line**: Contains HTTP Method (`POST`), Request Path (`/login.php`), and Protocol Version (`HTTP/1.1`).
2. **Request Headers**: Key-value pairs providing metadata about the request, client, and authentication.
3. **Empty Line (`\r\n`)**: Separates headers from the message body.
4. **Message Body**: Contains parameters, payload data, or JSON payloads (used primarily in `POST`, `PUT`, and `PATCH` requests).

---

## 2. HTTP Response Structure

An HTTP response returned by the server:

```http
HTTP/1.1 200 OK
Date: Wed, 05 Aug 2026 16:45:00 GMT
Server: Apache/2.4.52 (Ubuntu)
Set-Cookie: sessionid=xyz123456789; Secure; HttpOnly; SameSite=Strict
Content-Type: text/html; charset=UTF-8
Content-Length: 1250

<!DOCTYPE html>
<html>
...
```

1. **Status Line**: Contains Protocol Version (`HTTP/1.1`), Status Code (`200`), and Reason Phrase (`OK`).
2. **Response Headers**: Metadata provided by the server (server tokens, cookie settings, security policies).
3. **Message Body**: The returned resource (HTML markup, JSON data, image bytes).

---

## 3. HTTP Methods

| Method | Purpose | Safe | Idempotent | Security Considerations |
| :--- | :--- | :--- | :--- | :--- |
| **GET** | Retrieve data from server | Yes | Yes | Sensitive data should **never** be passed in GET query strings (logged in proxy/server logs). |
| **POST** | Submit data to server | No | No | Used for form submissions and actions; vulnerable to CSRF if anti-CSRF tokens are missing. |
| **PUT** | Create or replace target resource | No | Yes | If misconfigured/unauthenticated, allows unauthorized file uploads. |
| **DELETE** | Remove specified resource | No | Yes | Test for broken object-level authorization (IDOR) on resource IDs. |
| **PATCH** | Apply partial modifications | No | No | Check for mass assignment vulnerabilities. |
| **OPTIONS**| Query supported server methods | Yes | Yes | Used in CORS preflight requests; reveals enabled HTTP methods. |
| **HEAD** | Same as GET, returns headers only | Yes | Yes | Useful for fast service banner grabbing and content length verification. |

---

## 4. HTTP Status Codes

* **1xx (Informational)**: Request received, continuing process (e.g., `101 Switching Protocols` for WebSockets).
* **2xx (Success)**: Action successfully received and accepted.
  * `200 OK`: Request succeeded.
  * `201 Created`: Resource successfully created (`POST`/`PUT`).
  * `204 No Content`: Action succeeded, no body returned.
* **3xx (Redirection)**: Further action needed to complete request.
  * `301 Moved Permanently`: Permanent redirect (cached by browser).
  * `302 Found`: Temporary redirect (useful for identifying post-login redirects).
  * `304 Not Modified`: Cached copy remains valid.
* **4xx (Client Error)**: Request contains bad syntax or cannot be fulfilled.
  * `400 Bad Request`: Malformed syntax or invalid parameters.
  * `401 Unauthorized`: Authentication required (missing/invalid credentials).
  * `403 Forbidden`: Authenticated but unauthorized (access denied).
  * `404 Not Found`: Requested resource does not exist (useful for directory brute-forcing).
* **5xx (Server Error)**: Server failed to fulfill valid request.
  * `500 Internal Server Error`: Generic server fault (often reveals stack traces during SQLi or error-based testing).
  * `502 Bad Gateway` / `503 Service Unavailable`: Upstream proxy or server overload issues.

---

## 5. Security-Critical HTTP Headers

### Key Security Response Headers
- **`Set-Cookie` Flags**:
  - `Secure`: Ensures cookies are transmitted only over encrypted HTTPS.
  - `HttpOnly`: Prevents client-side JavaScript (`document.cookie`) from accessing the cookie (mitigates session hijacking via XSS).
  - `SameSite=Strict/Lax`: Controls cross-site cookie transmission to defend against Cross-Site Request Forgery (CSRF).
- **`Content-Security-Policy` (CSP)**: Restricts sources from which scripts, styles, and assets can be loaded.
- **`Strict-Transport-Security` (HSTS)**: Forces browsers to communicate exclusively over HTTPS.
- **`X-Frame-Options`**: Prevents clickjacking by controlling iframe embedding (`DENY`, `SAMEORIGIN`).
