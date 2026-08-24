# OAuth 2.0 & Modern Authorization Security

OAuth 2.0 (RFC 6749) is an open authorization framework that allows third-party client applications to obtain limited access to protected user resources (such as profiles, contacts, or APIs) without exposing the user's login credentials to the client.

---

## 1. Core Architecture & Actors

```text
       ┌────────────────────────┐
       │     Resource Owner     │ (The User)
       └───────────┬────────────┘
                   │ Grants Consent
                   ▼
┌──────────────────────────────────────┐
│                Client                │ (Web App / Mobile App)
└───────┬──────────────────────▲───────┘
        │ 1. Request Auth      │ 4. Bearer Access Token
        ▼                      │
┌──────────────────────┐       │
│ Authorization Server │ ──────┘ 3. Issues Token
└──────────────────────┘
        │
        ▼ 5. Query Resource with Token
┌──────────────────────┐
│   Resource Server    │ (API hosting user data)
└──────────────────────┘
```

| Actor / Entity | Definition & Role | Example |
| :--- | :--- | :--- |
| **Resource Owner** | The entity that owns the data and can grant access. | The end user. |
| **Client** | The application requesting access to user data on behalf of the owner. | Third-party web or mobile app (e.g., "Bistro App"). |
| **Authorization Server** | Authenticates the user, obtains consent, and issues codes/tokens. | OAuth Provider (Google, GitHub, CoffeeShopApp). |
| **Resource Server** | Hosts protected data and validates access tokens on API requests. | Backend API endpoints serving user resources. |

---

## 2. OAuth 2.0 Protocol Flows & Grant Types

### 2.1 The Authorization Code Flow (Standard / Secure)

```text
User / Browser               Client App             Auth Server          Resource Server
      │                          │                       │                      │
      │── 1. Login with OAuth ──►│                       │                      │
      │                          │── 2. Redirect to Auth►│                      │
      │◄── 3. Present Consent Page ──────────────────────│                      │
      │── 4. User Grants Consent ───────────────────────►│                      │
      │◄── 5. Redirect with Authorization Code ──────────│                      │
      │── 6. Deliver Code ──────►│                       │                      │
      │                          │── 7. Exchange Code ──►│                      │
      │                          │      (Secret + Code)  │                      │
      │                          │◄── 8. Return Token ───│                      │
      │                          │                       │                      │
      │                          │── 9. API Request with Bearer Token ─────────►│
      │                          │◄── 10. Protected Resource Data ──────────────│
```

* **Authorization Endpoint (`/oauth/authorize`):** Interacts with user's browser for login and permission consent.
* **Token Endpoint (`/oauth/token`):** Back-channel server-to-server call where the client exchanges the temporary code for an access token.

---

### 2.2 The Implicit Grant Flow (Legacy / Deprecated)
In the Implicit Grant flow, the authorization server returns an access token directly to the browser in the URL fragment (`#access_token=...`) without issuing an intermediate authorization code or requiring client secret verification.

* **Security Flaws:**
  - Tokens exposed directly in browser history, logs, and `Referer` headers.
  - Vulnerable to client-side extraction via JavaScript (`window.location.hash`).
  - **Status:** Officially **deprecated in OAuth 2.1** in favor of Authorization Code with PKCE.

---

## 3. OAuth Vulnerability Patterns

```text
OAuth Attack Vectors
├── 1. Insecure Redirect URI Interception (Code & Token theft)
├── 2. OAuth CSRF & Missing State Parameter (Account linking attacks)
├── 3. Implicit Grant + XSS Chaining (Token extraction via DOM hash)
├── 4. Client Secret Exposure (Decompilation of mobile/SPA clients)
└── 5. Scope Escalation & Flawed Token Lifetime Management
```

---

### 3.1 Insecure `redirect_uri` Interception
The `redirect_uri` parameter indicates where the authorization server must send the authorization code:

* **Vulnerability:** If the authorization server performs weak redirect URI validation (e.g., regex matching, allowing open redirects, or accepting arbitrary subdomains):
  ```text
  GET /oauth/authorize?client_id=123&redirect_uri=https://attacker-controlled.com/callback&response_type=code
  ```
* **Impact:** The authorization server sends the victim's authorization code to the attacker's server, enabling the attacker to exchange it for an access token.

---

### 3.2 OAuth CSRF & Missing `state` Parameter
The `state` parameter binds the client's authorization request to the user's current session:

```text
Attacker Initiates OAuth Flow ──► Obtains Attacker's Auth Code
                                          │
                                          ▼
Attacker Crafts Malicious Callback URL with Attacker's Code
                                          │
                                          ▼
Victim Visits Link While Logged In
                                          │
                                          ▼
Client Associates Attacker's OAuth Account with Victim's App Account
```

* **Impact (Account Takeover / Account Linking):** If the application fails to validate a unique, cryptographically random `state` parameter, an attacker tricks a victim into completing an OAuth callback using the attacker's authorization code, binding the attacker's OAuth identity to the victim's account.

---

### 3.3 Chaining XSS with Implicit Grant Token Theft
When applications combine the Implicit Grant flow with Cross-Site Scripting (XSS):

1. The client receives the access token in the URI fragment (`https://app.com/callback#access_token=SECRET_TOKEN`).
2. An attacker exploits an XSS flaw on the target application to inject malicious JavaScript.
3. The script executes `window.location.hash`, extracts the access token, and exfiltrates it to an external server.

---

## 4. Modern OAuth Defenses & OAuth 2.1

```text
┌─────────────────────────────────────────────────────────────┐
│ 1. Strict Exact-Match Redirect URI Validation               │
│ Compare against pre-registered whitelist without wildcards  │
├─────────────────────────────────────────────────────────────┤
│ 2. Mandatory Cryptographic `state` Parameter                │
│ Enforce unguessable CSRF tokens bound to user session       │
├─────────────────────────────────────────────────────────────┤
│ 3. Enforce PKCE (Proof Key for Code Exchange)               │
│ Protect public clients against code interception attacks    │
├─────────────────────────────────────────────────────────────┤
│ 4. Deprecate Implicit Flow                                  │
│ Use Authorization Code + PKCE for all SPA and mobile clients│
├─────────────────────────────────────────────────────────────┤
│ 5. Short-Lived Access Tokens                                │
│ Minimize token exposure window and enforce rotation         │
└─────────────────────────────────────────────────────────────┘
```
