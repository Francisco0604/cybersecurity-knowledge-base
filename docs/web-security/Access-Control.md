# Access Control & Privilege Escalation

Access control (authorization) is the process by which an application decides whether an authenticated user is permitted to access a requested resource or perform a requested action. When access control mechanisms fail, attackers can access sensitive data, manipulate resources, or execute privileged commands belonging to other users or administrators.

---

## 1. Core Concepts & Foundations

### 1.1 Authentication vs. Authorization

Understanding the distinction between authentication and authorization is fundamental to web application security:

| Dimension | Authentication (AuthN) | Authorization (AuthZ) |
| :--- | :--- | :--- |
| **Question Answered** | *"Who are you?"* | *"What are you allowed to do?"* |
| **Mechanisms** | Passwords, MFA, SSO, biometric data, API keys | RBAC, ABAC, access lists, object ownership checks |
| **HTTP Status Code** | `401 Unauthorized` (unauthenticated request) | `403 Forbidden` (authenticated, but permission denied) |
| **Failure State** | Session spoofing, credential stuffing, brute force | Insecure Direct Object References, Privilege Escalation |

```text
Incoming Request
       ↓
[ Authentication Filter ] ──(Invalid Session)──> 401 Unauthorized
       ↓ (Valid Identity: User "wiener", Role: "USER")
[ Authorization Engine ]  ──(Accessing /admin)──> 403 Forbidden
       ↓ (Permitted Action)
[ Business Logic & Data Access ]
```

---

### 1.2 Access Control Models

Modern applications employ various access control paradigms:

1. **Role-Based Access Control (RBAC):** Permissions are assigned to specific roles (e.g., `ROLE_USER`, `ROLE_MANAGER`, `ROLE_ADMIN`). Users inherit permissions by being assigned to one or more roles.
2. **Attribute-Based Access Control (ABAC):** Permissions are evaluated dynamically based on user attributes, resource attributes, environmental context (e.g., time, location), and requested actions.
3. **Discretionary Access Control (DAC):** The owner of a resource determines who has access to it (e.g., file permissions in Linux).
4. **Mandatory Access Control (MAC):** Access is governed by strict organizational classifications (e.g., Confidential, Secret, Top Secret).

---

### 1.3 Privilege Escalation Taxonomies

Broken access control vulnerabilities typically manifest in two primary privilege escalation forms:

#### Vertical Privilege Escalation
A user with lower privileges accesses functionality or data reserved for a higher-privileged user (e.g., a standard user accessing administrative controls).

```text
[ Standard User ] ──────────────► [ Administrator Panel ]
 (ROLE_USER)                        (ROLE_ADMIN Required)
```

#### Horizontal Privilege Escalation
A user accesses resources or functions belonging to another user who holds the exact same permission level (e.g., User A viewing User B's billing records or private messages).

```text
[ User A (ID: 1001) ] ──────────► [ User B Profile (ID: 1002) ]
 (ROLE_USER)                        (ROLE_USER)
```

#### Context-Dependent Access Control Failures
The application allows actions to occur out of the prescribed sequence or outside permitted business logic boundaries (e.g., skipping a payment step or approving one's own discount).

---

## 2. Access Control Vulnerability Patterns

Based on empirical testing across diverse application architectures, access control weaknesses generally fall into distinct architectural flaw categories:

```text
Access Control Vulnerabilities
├── 1. Unprotected Privileged Endpoints & Obscurity Failures
├── 2. Client-Side Authorization & Parameter Tampering
│   ├── Cookie / Request Parameter Role Manipulation
│   └── Mass Assignment / Over-Posting
├── 3. Insecure Direct Object References (IDOR)
│   ├── Predictable / Sequential Identifiers
│   ├── GUID / Unpredictable Identifier Misconceptions
│   └── Multi-Format Resource Leaks (Files, Exports, Transcripts)
├── 4. Information Disclosure in Responses
│   ├── Body Leakage During Redirects (302 Found)
│   └── Presentation-Layer Password Masking vs. HTML Response Leakage
├── 5. Front-End vs. Back-End Semantic Discrepancies
│   └── Routing Header Overrides (X-Original-URL / X-Rewrite-URL)
├── 6. HTTP Method / Verb Tampering
├── 7. Multi-Step Workflow Authorization Gaps
└── 8. Header-Based Authorization Flaws (Referer / Origin Reliance)
```

---

### 2.1 Unprotected Privileged Endpoints & Obscurity Failures

Developers frequently place administrative functionality at unlinked, obscure, or hidden URL paths under the mistaken belief that secrecy equals security (**Security through Obscurity**).

* **Mechanism:** Endpoints like `/admin`, `/admin-console`, `/administrator`, or `/admin_secret_987` are deployed without server-side authentication or authorization checks.
* **Discovery Vectors:**
  - `robots.txt` disallow rules (`Disallow: /admin-panel`)
  - Source code analysis (JavaScript bundles leaking administrative routes like `window.adminUrl = '/admin-3k4j5h'`)
  - Directory fuzzing / content discovery wordlists

```text
Client (Unauthenticated) ──> GET /admin-secret ──> 200 OK (Full Admin Dashboard)
```

---

### 2.2 Client-Controlled Authorization & Parameter Tampering

Applications commit a severe architectural error when they trust client-supplied data to determine authorization privileges.

#### Cookie & Query Parameter Role Tampering
- The application reads the user's role directly from an editable cookie or request parameter (e.g., `Cookie: Admin=false` or `GET /account?role=user`).
- **Exploit:** An attacker intercepts the request in Burp Proxy/Repeater and alters the value to `Admin=true` or `role=admin`. Because the server fails to cross-reference the user's true server-side role in the database, it grants elevated access.

#### Mass Assignment / Over-Posting
- Occurs when web frameworks automatically bind HTTP request parameters (such as JSON body payloads) to internal data models or database entities.
- **Exploit:** A user updating their profile (`{"email": "user@test.com"}`) injects administrative attributes (`{"email": "user@test.com", "roleid": 2, "isAdmin": true}`). If the controller does not whitelist bound fields, the entity binds the injected fields and escalates the user's privileges.

```http
POST /api/user/update HTTP/2
Content-Type: application/json

{
  "name": "Wiener",
  "email": "wiener@normal-user.net",
  "roleid": 2
}
```

---

### 2.3 Insecure Direct Object References (IDOR)

Insecure Direct Object References occur when an application uses client-supplied input to access objects directly in storage (database records, files, cloud buckets) without enforcing object-level authorization.

```text
GET /account?id=1001 ──> Server queries DB: "SELECT * FROM users WHERE id = 1001"
                           (Checks if user owns record? NO)
                           ──> Returns User 1001's Private Data
```

#### IDOR Variants

1. **Predictable Sequential Identifiers:** Numeric integer keys (`/documents/100`, `/documents/101`). Easily enumerated and bulk-scraped.
2. **Unpredictable Identifiers (GUIDs / UUIDs):** Applications using GUIDs (e.g., `/user?id=6a7b...`) often assume GUID complexity eliminates the need for authorization. However:
   - GUIDs can be discovered via public interactions (forum posts, review authors, shared links).
   - GUIDs can leak through indirect API endpoints.
   - Once the GUID is known, missing object-level access control allows complete unauthorized access.
3. **Static File / Transcript References:** Direct access to filesystem paths or document identifiers (e.g., `GET /download-transcript/2.txt` altered to `1.txt`), which can expose sensitive credentials, chat logs, or invoices.

---

### 2.4 Information Disclosure & Sensitive Response Leakage

Broken access control can also manifest as sensitive information disclosure through subtle response behaviors:

#### Sensitive Data Leakage in Redirects (`302 Found`)
- When an unauthorized user requests a sensitive endpoint, the application generates a `302 Found` redirect to `/login`.
- **The Flaw:** The back-end web framework generates and renders the entire protected HTML response body *before* attaching the redirect header, and fails to terminate execution.
- **Result:** While web browsers automatically follow the redirect and display the login page, inspecting the raw HTTP response in Burp reveals the entire protected document body.

#### Presentation-Layer Password Masking
- An account configuration page loads the existing user's password into an HTML form element:
  ```html
  <input type="password" name="currentPassword" value="SuperSecretP@ss123">
  ```
- **The Flaw:** The developer relies on the browser's UI rendering (displaying dots `••••••••`) to keep the password secure. However, inspecting the raw HTTP response reveals the plaintext password directly in the source.

---

### 2.5 Front-End vs. Back-End Access Control Discrepancies

Enterprise architectures typically separate the infrastructure into front-end reverse proxies (WAFs, NGINX, API Gateways) and back-end application servers. When these layers disagree on how to parse or route the requested URL, access controls can be completely circumvented.

#### URL Rewrite Bypass via `X-Original-URL` and `X-Rewrite-URL`
- Front-end proxy checks the visible HTTP request line: `GET / HTTP/2` (Allowed as public).
- Back-end framework supports the non-standard `X-Original-URL: /admin` header and internally rewrites the request route to `/admin`.
- **Query Parameter Decoupling:** In framework route overrides, query parameters often need to stay on the main request line while the route override is passed in the header:
  ```http
  GET /?username=carlos HTTP/2
  Host: example.com
  X-Original-URL: /admin/delete
  ```

```text
[ Client ] ──(GET / with X-Original-URL: /admin)──► [ Reverse Proxy ]
                                                            │ (Inspects "/": Allowed)
                                                            ▼
                                                    [ Back-End Framework ]
                                                            │ (Reads Header: Routes to /admin)
                                                            ▼
                                                    [ Admin Controller Executed ]
```

---

### 2.6 Method-Based Access Control Bypasses

Access-control rules are frequently tied explicitly to specific HTTP verbs (e.g., only guarding `POST /admin/roles` while leaving `GET`, `PUT`, or `HEAD` unguarded).

* **Mechanism:** A framework routing configuration handles requests without strict HTTP verb constraints (e.g., Spring `@RequestMapping("/admin/roles")` handling both `GET` and `POST`), but the security filter only restricts `POST`.
* **Exploitation:** Converting the request from `POST` to `GET` and moving body parameters to URL query parameters bypasses the security filter while executing the privileged controller action.

```http
GET /admin-roles?username=wiener&action=upgrade HTTP/2
Cookie: session=[WIENER-SESSION]
```

---

### 2.7 Multi-Step Workflow Authorization Gaps

Complex business processes (e.g., role promotion, fund transfers, password resets) often span multiple consecutive requests:

```text
Step 1: Initiate Action ──> Step 2: Review / Confirm ──> Step 3: Execute Action
```

* **Flawed Assumption:** Developers frequently assume that because Step 1 is protected by an administrative role check, users cannot possibly reach Step 2 or Step 3 without being authorized.
* **The Flaw:** Step 2/3 (e.g., `POST /admin-roles` with `confirmed=true`) executes the state-changing action without re-verifying the requesting user's authorization.
* **Exploitation:** An attacker completely skips Step 1 and submits the final Step 2 execution request directly using their low-privileged session.

---

### 2.8 Header-Based Access Control Flaws

Some applications attempt to enforce authorization based on client-controlled request metadata, specifically the `Referer` or `Origin` header.

* **Flawed Assumption:** If `Referer: https://example.com/admin` is present, the request must have originated from an administrator interacting with the admin dashboard.
* **The Flaw:** HTTP request headers are generated entirely on the client side. Any user or attacker can forge or modify the `Referer` header using an intercepting proxy.

```http
GET /admin-roles?username=wiener&action=upgrade HTTP/2
Cookie: session=[WIENER-SESSION]
Referer: https://example.com/admin
```

---

## 3. Systematic Access Control Testing Methodology

When conducting authorized web penetration testing, follow a structured, multi-role testing matrix:

```text
1. Map Roles & Permissions
   ├── Unauthenticated User
   ├── Low-Privileged User A (Tenant 1)
   ├── Low-Privileged User B (Tenant 2)
   └── Administrative User
        ↓
2. Establish Baseline Behaviors
   ├── Request privileged endpoints as Admin (Record 200 OK + baseline size)
   └── Request privileged endpoints as Unauthenticated/User A (Expect 401/403)
        ↓
3. Test Vertical Privilege Escalation
   ├── Access administrative endpoints directly from User A session
   ├── Test URL rewrite headers (X-Original-URL, X-Rewrite-URL)
   ├── Perform HTTP verb tampering (POST <-> GET, PUT, PATCH)
   ├── Directly invoke sub-steps in multi-step workflows (skip entry steps)
   └── Tamper with client-controlled role identifiers & cookies
        ↓
4. Test Horizontal Privilege Escalation (IDOR)
   ├── Substitute User A identifier with User B identifier
   ├── Harvest GUIDs from public profile pages, comments, metadata
   ├── Inspect static file / export / transcript endpoints
   └── Check for response leakage in 302 redirects & masked input fields
        ↓
5. Validate & Document Impact
   └── Confirm unauthorized state change or sensitive data disclosure
```

---

## 4. Secure Architecture & Remediation

Access control must be designed as an intrinsic architectural property of the application, not an afterthought bolted onto perimeter filters.

### 4.1 Golden Rules of Access Control

1. **Default Deny:** Unless a resource or action is explicitly declared public, access must be denied by default.
2. **Server-Side Enforcement on Every Action:** Every single controller, API route, and service method must independently verify the authenticated user's authorization. Never rely solely on front-end UI hiding or reverse proxy URL filters.
3. **Object-Level Authorization (No Naked Object References):** Whenever a request accesses an object by ID, verify that the authenticated identity owns the object or has explicit tenant permissions:
   ```java
   // Secure Object-Level Check
   Document doc = documentRepository.findById(documentId);
   if (!doc.getOwnerId().equals(currentUser.getId())) {
       throw new AccessDeniedException("Unauthorized object access.");
   }
   ```
4. **Sanitize Gateway & Routing Headers:** Configure reverse proxies and API gateways to strip, sanitize, or overwrite client-supplied routing headers (`X-Original-URL`, `X-Rewrite-URL`, `X-Forwarded-*`).
5. **Strict HTTP Verb Enforcement:** Ensure endpoints only accept appropriate HTTP methods (e.g., state changes must require `POST`/`PUT`/`DELETE` and reject `GET` with `405 Method Not Allowed`).
6. **Server-Managed Workflow State:** Track multi-step workflows using server-side session state or cryptographically signed, short-lived tokens. Never rely on client-supplied flags like `confirmed=true`.
7. **Disable Mass Assignment:** Explicitly whitelist permitted fields when binding HTTP input to data models (e.g., using Data Transfer Objects / DTOs).

---

## 5. Summary Reference Matrix

| Attack Vector | Flaw Mechanism | Bypass Technique | Secure Fix |
| :--- | :--- | :--- | :--- |
| **Unprotected URL** | No auth on administrative path | Direct navigation / wordlist discovery | Server-side role check on endpoint |
| **Role Parameter** | Role verified from client cookie/parameter | Parameter tampering (`Admin=true`) | Store & verify roles strictly in session |
| **Mass Assignment** | Model auto-binding unchecked fields | Injecting `"roleid": 2` in JSON body | Use DTOs with strict field whitelisting |
| **IDOR (Predictable)** | Direct lookup without owner check | Integer key enumeration (`?id=1002`) | Enforce `ownerId == currentUserId` |
| **IDOR (GUID)** | Missing owner check; reliance on GUID secrecy | GUID discovery from public interactions | Enforce object ownership checks |
| **Data Leakage in 302** | Response body generated before redirect | Inspect raw body in Burp proxy | Terminate execution immediately on redirect |
| **URL Rewrite Bypass** | Front-end proxy & back-end parser mismatch | `X-Original-URL: /admin` | Strip headers at proxy; enforce auth on back-end |
| **Method Tampering** | Security rule only binds to `POST` | Switch method to `GET` + query params | Apply authorization to all verbs (`anyRequest()`) |
| **Multi-Step Bypass** | Missing auth on sub-step confirmation | Invoke Step 2 execution request directly | Verify role on every step & track server state |
| **Referer Bypass** | Auth check trusts `Referer` header | Spoof `Referer: /admin` header | Never use request headers for authorization |
