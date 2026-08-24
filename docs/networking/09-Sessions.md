# HTTP Sessions & State Management

## Overview

A session is a continuous series of interactions and exchanges between a client and a web application. Because the Hypertext Transfer Protocol (HTTP) is inherently stateless, session management is the server-side mechanism that tracks and associates multiple sequential requests with a single authenticated user identity.

```text
[ Client (Browser) ]                              [ Web Server ]
         │                                              │
         │── 1. POST /login (Username + Password) ─────►│ (Validates credentials)
         │                                              │
         │◄── 2. Set-Cookie: session_id=XYZ123 ─────────│ (Generates & saves session record)
         │                                              │
         │── 3. GET /account (Cookie: session_id=XYZ123)►│ (Looks up XYZ123 in database)
         │                                              │
         │◄── 4. 200 OK (Returns Account Details) ──────│ (Associates request with User)
```

---

# 1. Stateful vs. Stateless Session Architectures

Modern web applications implement session management using two primary architectural paradigms:

```text
Session Architectures
├── 1. Stateful (Server-Side Managed Sessions)
│   ├── Storage: Session database, Redis cache, or server memory
│   ├── Client Token: Opaque random string (e.g., PHPSESSID, JSESSIONID)
│   └── Verification: Server queries session store on every request
└── 2. Stateless (Client-Side Stored, Cryptographically Signed Tokens)
    ├── Storage: Client browser (Cookie / Authorization Header)
    ├── Client Token: Self-contained signed token (e.g., JWT)
    └── Verification: Server validates cryptographic signature
```

| Dimension | Stateful Sessions | Stateless Sessions (e.g., JWT) |
| :--- | :--- | :--- |
| **Token Type** | Opaque identifier with no inherent meaning. | Formatted payload containing claims and signatures. |
| **Server Overhead** | Requires memory/database lookup for each HTTP request. | No database lookup needed for signature verification. |
| **Instant Revocation** | Trivial: Delete the session ID from the server-side store. | Complex: Requires token revocation blocklists or short lifetimes. |
| **Scalability** | Requires sticky sessions or a centralized distributed cache (e.g., Redis). | Easily scales horizontally across independent microservice nodes. |

---

# 2. Complete Session Lifecycle

```text
1. SESSION CREATION
   └── User authenticates; server generates high-entropy random Session ID (CSPRNG).
           │
           ▼
2. SESSION BINDING & STORAGE
   └── Server binds Session ID to User ID in session store; sends Session ID in Secure/HttpOnly cookie.
           │
           ▼
3. SESSION VALIDATION & USAGE
   └── Server verifies incoming Session ID against active store on every incoming request.
           │
           ▼
4. PRIVILEGE TRANSITIONS (Session Regeneration)
   └── Server invalidates existing Session ID and issues a new ID upon login/privilege change.
           │
           ▼
5. SESSION TERMINATION & DESTRUCTION
   └── Server explicitly purges session record from database upon logout or timeout.
```

---

# 3. Session Vulnerabilities & Attack Vectors

```text
Session Vulnerabilities
├── 1. Session Fixation (Retaining pre-login session ID across authentication)
├── 2. Session Hijacking (Theft of session tokens via XSS, sniffing, or logs)
├── 3. Predictable Session Identifiers (Low entropy / sequential ID generation)
├── 4. Ineffective Session Invalidation (Client-only logout without server-side purge)
└── 5. Missing Inactivity & Absolute Timeouts (Indefinitely active sessions)
```

---

### 3.1 Session Fixation
Session fixation occurs when an application keeps the user's existing session identifier across a privilege transition (such as logging in):

```text
1. Attacker obtains fresh session identifier (SID_1) from target application.
2. Attacker tricks victim into using SID_1 (via URL parameter, subdomain cookie injection, or phishing).
3. Victim authenticates to the application using SID_1.
4. If the server does NOT regenerate the session ID upon login, SID_1 becomes authenticated.
5. Attacker uses SID_1 to access the victim's authenticated account.
```

* **Defensive Rule:** Always invalidate existing session identifiers and generate a fresh, unique session token immediately upon authentication.

---

### 3.2 Session Hijacking
Session hijacking occurs when an attacker obtains a legitimate user's active session token:

* **Theft Vectors:**
  - **Cross-Site Scripting (XSS):** Reading non-`HttpOnly` cookies via JavaScript.
  - **Man-in-the-Middle (MitM):** Capturing unencrypted session cookies over HTTP (missing `Secure` flag).
  - **Network / Reverse Proxy Logging:** Capturing session tokens passed insecurely in URL query strings (`/view?session_id=...`).

---

### 3.3 Client-Side Role Storage Risks
A severe authorization breakdown occurs when an application trusts user roles or permissions stored in client-side cookies or storage rather than checking server-side session state:

```text
[ Client Browser ] ──► localStorage.setItem("userRole", "admin")
                             │
                             ▼ (HTTP Request with modified role)
[ Vulnerable Application ]
  └── Reads "admin" role directly from client request WITHOUT database authorization check
```

* **Core Principle:** Client-side storage can only be used to update UI views (showing/hiding menu items). The server backend must independently verify user authorization from trusted server-side state for every privileged operation.

---

# 4. Defensive Best Practices & Session Hardening

```text
┌─────────────────────────────────────────────────────────────┐
│ 1. Cryptographically Secure Session IDs                     │
│ Generate tokens using CSPRNG with >= 128 bits of entropy    │
├─────────────────────────────────────────────────────────────┤
│ 2. Session Regeneration on Authentication                   │
│ Issue a brand new session identifier immediately upon login │
├─────────────────────────────────────────────────────────────┤
│ 3. Comprehensive Cookie Flags                               │
│ Enforce HttpOnly, Secure, and SameSite=Lax/Strict flags     │
├─────────────────────────────────────────────────────────────┤
│ 4. Dual Session Timeouts                                    │
│ Enforce idle inactivity timeout (e.g. 15m) & max lifetime   │
├─────────────────────────────────────────────────────────────┤
│ 5. Complete Server-Side Session Destruction                 │
│ Purge session records from backend database upon logout     │
└─────────────────────────────────────────────────────────────┘
```

---

# 5. Interview Questions

### What is Session Fixation and how do you prevent it?
Session fixation is an attack where an adversary establishes a known session ID on a victim's browser before the victim logs in. If the server fails to issue a new session identifier upon successful login, the attacker's pre-set session ID becomes authenticated. It is prevented by always invalidating the existing session ID and generating a new, cryptographically random session token immediately upon successful login.

### What is the difference between an idle timeout and an absolute timeout?
- **Idle Timeout:** Invalidates the session after a specified period of user inactivity (e.g., no requests for 15 minutes), protecting unattended browser sessions.
- **Absolute Timeout:** Invalidates the session after a fixed total duration from initial login (e.g., 8 hours), regardless of active usage, limiting the total window of opportunity if a session token is stolen.

### Why should session identifiers never be placed in URL parameters?
Session IDs in URLs are recorded in browser history, web server access logs, proxy logs, and leaked to external third parties via the `Referer` header when following external links. Session identifiers should only be transmitted via secure HTTP headers or cookies.

---

# 6. Lesson Summary

- Sessions maintain state across stateless HTTP requests.
- Stateful sessions store data on the server; stateless sessions (JWT) encode signed data on the client.
- Session IDs must possess high entropy and be generated by CSPRNG algorithms.
- Always regenerate session IDs upon authentication to eliminate session fixation.
- Protect session cookies using `HttpOnly`, `Secure`, and `SameSite` flags.
- Never rely on client-side state for authorization decisions.
