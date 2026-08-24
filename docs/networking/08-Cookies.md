# HTTP Cookies

## Overview

An HTTP cookie is a small piece of data that a web server sends to a user's web browser. The browser stores this data and automatically sends it back to the server in subsequent HTTP requests to the same origin.

Because HTTP is a stateless protocol, cookies provide a mechanism to remember stateful information between separate HTTP requests:

```text
Client                                             Server
  │                                                  │
  │── 1. POST /login (Credentials) ─────────────────►│
  │                                                  │
  │◄── 2. HTTP 200 OK (Set-Cookie: session_id=123) ──│
  │                                                  │
  │   [ Browser Stores Cookie Locally ]              │
  │                                                  │
  │── 3. GET /dashboard (Cookie: session_id=123) ───►│
  │                                                  │
  │◄── 4. HTTP 200 OK (Authenticated Content) ───────│
```

---

# 1. Why Cookies Exist

HTTP requests do not inherently retain memory of preceding requests. Without state mechanisms:
- A user would have to enter their username and password on every single page navigation.
- Shopping carts would lose items upon loading a new product page.
- User customization preferences (dark mode, language) would reset immediately.

Cookies enable three primary use cases:
1. **Session Management:** User login status, shopping carts, game scores, or active authentication tokens.
2. **Personalization:** User interface preferences, themes, language, and regional settings.
3. **Tracking & Analytics:** Recording user browsing behavior and analytics across web sessions.

---

# 2. Cookie Headers: `Set-Cookie` vs. `Cookie`

```text
Server Response:
Set-Cookie: name=value; Domain=example.com; Path=/; Secure; HttpOnly; SameSite=Lax

Browser Request:
Cookie: name=value; theme=dark
```

* **`Set-Cookie` (Response Header):** Dispatched by the web server instructing the browser to save a cookie with specific attributes and security constraints.
* **`Cookie` (Request Header):** Dispatched by the browser containing only `name=value` pairs matching the current request's domain and path.

---

# 3. Cookie Scope & Lifetime Attributes

### 3.1 `Domain` and `Path`
Defines the scope and destination boundaries where the browser is allowed to send the cookie:

* **`Domain`:** Specifies which hosts can receive the cookie. If omitted, the cookie is scoped strictly to the exact host (excluding subdomains). If explicitly set (e.g., `Domain=example.com`), it is automatically shared with all subdomains (`api.example.com`, `admin.example.com`).
* **`Path`:** Specifies the URL path prefix required in the request URI (e.g., `Path=/admin` ensures the cookie is only sent to `/admin` and its child endpoints).

---

### 3.2 Session Cookies vs. Persistent Cookies

```text
Cookie Lifetime
├── Session Cookie (Transient / In-Memory)
│   └── No Expires or Max-Age specified; purged immediately when the browser is closed.
└── Persistent Cookie (Stored on Disk)
    └── Contains Expires=Date or Max-Age=Seconds; persists across browser restarts.
```

* **`Expires`:** Specifies an absolute expiration date and time in GMT format (e.g., `Expires=Wed, 21 Oct 2026 07:28:00 GMT`).
* **`Max-Age`:** Specifies the cookie lifetime in seconds relative to the current time (e.g., `Max-Age=3600` expires in 1 hour). Modern browsers give precedence to `Max-Age` over `Expires`.

---

# 4. Critical Cookie Security Flags

Security attributes are essential to protect cookies against interception, theft, and cross-site manipulation:

```text
┌─────────────────────────────────────────────────────────────┐
│ 1. HttpOnly Flag                                            │
│ Blocks client-side JavaScript access (document.cookie)      │
├─────────────────────────────────────────────────────────────┤
│ 2. Secure Flag                                              │
│ Restricts transmission strictly to encrypted HTTPS traffic  │
├─────────────────────────────────────────────────────────────┤
│ 3. SameSite Attribute (Strict / Lax / None)                 │
│ Controls cross-site cookie transmission to defend vs CSRF   │
└─────────────────────────────────────────────────────────────┘
```

---

## 4.1 `HttpOnly` Flag
Instructs the browser that the cookie should not be accessible through client-side scripts (such as JavaScript `document.cookie`):

```http
Set-Cookie: session_id=a8f9c2e1; Path=/; HttpOnly
```

* **Security Impact:** If a web application suffers from a Cross-Site Scripting (XSS) vulnerability, an attacker injecting JavaScript cannot read or exfiltrate an `HttpOnly` session cookie directly.

---

## 4.2 `Secure` Flag
Instructs the browser that the cookie must only be transmitted over encrypted connections (HTTPS):

```http
Set-Cookie: session_id=a8f9c2e1; Path=/; Secure
```

* **Security Impact:** Prevents the browser from sending session identifiers over plaintext HTTP, protecting against network sniffing and Man-in-the-Middle (MitM) eavesdropping attacks.

---

## 4.3 `SameSite` Attribute (CSRF Defense)
Controls whether cookies are sent along with cross-site requests (e.g., when a user clicks a link or submits a form on a third-party website):

| `SameSite` Value | Behavior / Policy | Security Level |
| :--- | :--- | :--- |
| **`SameSite=Strict`** | The cookie is **never** sent with cross-site requests (even top-level link navigations from external sites). | 🟢 **Highest:** Maximum protection against CSRF. |
| **`SameSite=Lax`** | The cookie is withheld on cross-site subrequests (images, iframes, AJAX `POST`), but sent when a user follows a top-level `GET` link. Default in modern browsers. | 🟡 **Balanced:** Standard balance of security and usability. |
| **`SameSite=None`** | The cookie is sent with **all** cross-site requests. Requires the `Secure` flag (`SameSite=None; Secure`). | 🔴 **None:** Susceptible to CSRF without anti-CSRF tokens. |

---

# 5. Cookie Vulnerability Taxonomies

```text
Cookie Security Vulnerabilities
├── 1. Session Theft via XSS (Missing HttpOnly flag)
├── 2. Plaintext Interception (Missing Secure flag)
├── 3. Cross-Site Request Forgery (Missing SameSite or SameSite=None)
├── 4. Cookie Tampering & Parameter Manipulation (Modifying role/admin cookies)
└── 5. Predictable / Weak Token Generation (Low-entropy or guessable cookies)
```

---

# 6. Interview Questions

### What does the `HttpOnly` cookie attribute do?
The `HttpOnly` attribute prevents client-side scripts (JavaScript) from accessing `document.cookie`. This provides a critical defense against session hijacking via Cross-Site Scripting (XSS).

### What is the difference between `SameSite=Strict` and `SameSite=Lax`?
- `SameSite=Strict` ensures the cookie is never sent on any cross-site request, even when a user clicks an external link to visit the site.
- `SameSite=Lax` allows the cookie to be sent during top-level navigations (regular `GET` links from external sites), but blocks it on cross-site state-changing requests (`POST`, `PUT`, `DELETE`, `<iframe>`, `<img>`).

### Why should session identifiers never be stored in persistent cookies?
Persistent cookies remain stored on physical disk across browser sessions. If a user accesses an application from a shared or public computer, a persistent cookie may remain accessible to subsequent users of that machine. Sensitive session tokens should use transient session cookies with short server-side idle timeouts.

---

# 7. Lesson Summary

- Cookies allow stateless HTTP to maintain stateful user sessions.
- `Set-Cookie` sets the cookie; the `Cookie` header transmits it back to the server.
- `Domain` and `Path` restrict cookie routing scope.
- `HttpOnly` mitigates XSS cookie theft.
- `Secure` prevents transmission over unencrypted HTTP.
- `SameSite` defends against Cross-Site Request Forgery (CSRF).
- Applications must never trust unverified client-side data stored in cookies.
