# Burp Suite — Tool Reference & Practical Guide

Burp Suite (developed by PortSwigger) is the industry-standard integrated platform for performing security assessments and penetration testing of web applications and APIs.

---

## 1. What is Burp Suite?

Burp Suite functions primarily as an intercepting HTTP/HTTPS proxy that sits between a web browser (or API client) and the destination web application server:

```text
[ Browser / Client ] ────────► [ Burp Suite Proxy ] ────────► [ Target Web Server ]
                                      │
                                      ├── Intercept & Inspect Traffic
                                      ├── Modify Requests / Responses On-The-Fly
                                      ├── Replay & Tamper (Repeater)
                                      └── Automate Fuzzing (Intruder)
```

By intercepting communication at the application layer, Burp Suite enables security testers to analyze raw HTTP/S requests, discover hidden functionality, manipulate client-side variables, test authorization boundaries, and identify vulnerabilities.

---

## 2. Project Setup & Environment Configuration

### 2.1 Project Types
* **Temporary Projects (Community Edition):** Stored in memory; data is discarded when Burp is closed.
* **Disk-Based Projects (Professional Edition):** Saved to a `.burp` project file on disk, allowing sessions, history, and repeater tabs to persist across restarts.

### 2.2 Proxy Listener Configuration
Burp listens on local loopback by default:
* **Default Interface:** `127.0.0.1:8080` (configured under **Proxy** → **Proxy settings** → **Proxy listeners**).
* **Binding to Specific Interfaces:** Useful when proxying traffic from mobile devices, VMs, or external testing environments.

### 2.3 Burp's Embedded Browser vs. External Browsers
* **Burp's Built-in Chromium Browser:** Accessible via **Proxy** → **Open browser**. Comes pre-configured with proxy settings and trusts the Burp CA certificate out of the box.
* **External Browsers (Firefox / Chrome):**
  1. Configure proxy settings to route HTTP/HTTPS traffic through `127.0.0.1:8080` (or use extensions like FoxyProxy).
  2. Install Burp's PortSwigger CA Certificate (`http://burp/cert` when proxy is active) into the browser's certificate trust store under **Authorities** to intercept HTTPS traffic without TLS certificate errors.

---

## 3. Proxy & Intercept

The **Proxy** module is the core interception engine of Burp Suite.

```text
[ Browser ] ──> HTTP Request ──> [ Burp Proxy (Intercept ON) ]
                                            │
                                            ├── [Forward] ──> Sent to Server
                                            ├── [Drop]    ──> Discarded (Never sent)
                                            └── [Modify]  ──> Tamper headers/params, then Forward
```

### 3.1 Intercept Controls
* **Intercept is on:** All matching traffic is paused before leaving Burp, allowing manual inspection and real-time parameter/header editing.
* **Intercept is off:** Traffic passes freely through the proxy while still being logged in **HTTP history**.
* **Forward (Ctrl+F / Cmd+F):** Sends the current intercepted request/response onward.
* **Drop (Ctrl+D / Cmd+D):** Silently discards the request; the server never receives it, and the client receives a broken connection.

### 3.2 Interception Rules
Configure under **Proxy** → **Proxy settings** → **Request interception rules**:
* Filter out noisy third-party traffic (analytics, CDNs, fonts, image extensions `.png`, `.jpg`, `.css`, `.woff2`).
* Intercept only in-scope target URLs or specific HTTP methods.

---

## 4. HTTP History & Traffic Analysis

The **HTTP history** tab records every request and response that passes through the Burp Proxy.

```text
#  | Host              | Method | URL              | Status | Length | MIME | Title
1  | lab.web-sec.net   | GET    | /                | 200    | 4520   | HTML | Home
2  | lab.web-sec.net   | GET    | /robots.txt      | 200    | 120    | text | -
3  | lab.web-sec.net   | POST   | /login           | 302    | 0      | -    | -
4  | lab.web-sec.net   | GET    | /my-account      | 200    | 3210   | HTML | Account
```

### 4.1 Key Operations in HTTP History
* **Display Filter Bar:** Click the filter bar above the table to filter by:
  * **Scope:** Show only in-scope items.
  * **MIME Type:** Hide CSS, images, and fonts to focus on HTML, JSON, and XML.
  * **Status Code:** Filter by `2xx`, `3xx`, `4xx`, or `5xx`.
  * **Search Term:** Search for specific parameters, cookies, or strings across requests and responses.
* **Sending to Other Tools:**
  * **Send to Repeater (`Ctrl+R` / `Cmd+R`):** Send selected request to Repeater for manual manipulation.
  * **Send to Intruder (`Ctrl+I` / `Cmd+I`):** Send selected request to Intruder for automated fuzzing.
  * **Send to Comparer (`Ctrl+M` / `Cmd+M`):** Compare two requests or responses for diff analysis.

---

## 5. Target & Scope

The **Target** module maintains the hierarchical structure of tested applications.

### 5.1 Target Scope
Configuring scope prevents testing non-target systems and keeps logs focused:
1. Navigate to **Target** → **Scope settings**.
2. Add target domains or URL prefixes (e.g., `https://target-app.com`).
3. Enable **Use advanced scope control** for fine-grained regex matching and excluding specific subdomains or paths.

### 5.2 Site Map
The **Site map** displays discovered endpoints organized by folder and URL structure:
* Endpoints requested by the tester appear in black text.
* Unrequested endpoints discovered via HTML links/JavaScript appear in gray text.

---

## 6. Burp Repeater — Manual Request Tampering

Repeater is the primary tool for manual vulnerability testing and iterative exploitation. It allows you to modify headers, methods, parameters, and bodies, resend requests arbitrarily, and inspect the resulting responses.

```text
[ HTTP History / Proxy ] ──(Ctrl+R)──► [ Burp Repeater ]
                                              │
                                              ├── 1. Modify Target Element
                                              ├── 2. Click "Send" (Ctrl+Space)
                                              └── 3. Analyze Response Delta
```

### 6.1 Core Repeater Capabilities

#### 1. Changing HTTP Request Methods
* **Shortcut:** Right-click anywhere in the request editor → **Change request method**.
* Burp automatically converts `POST` parameters in the body to URL query parameters when switching to `GET`, and vice-versa.

#### 2. Manipulating Request Headers
* **Adding Custom Headers:** Insert headers directly in the raw request editor:
  ```http
  X-Original-URL: /admin
  X-Rewrite-URL: /admin/delete
  X-Forwarded-For: 127.0.0.1
  ```
* **Modifying Origin & Referer:**
  ```http
  Referer: https://target-app.com/admin
  ```
* **Switching Sessions:** Replace or modify the `Cookie` header to test privilege escalation between different user accounts.

#### 3. Parameter Tampering & Payload Testing
* Modify numeric identifiers: `GET /download-transcript/2.txt` → `1.txt`.
* Inject JSON fields (Mass Assignment): `{"email": "user@test.com", "roleid": 2}`.
* Test parameter type juggling: Convert string to array (`user=admin` → `user[]=admin`).

### 6.2 Response Inspector Views
* **Pretty:** Formats HTML/JSON/XML with syntax highlighting and folding.
* **Raw:** Unformatted, exact bytes returned by the server.
* **Hex:** Byte-level hexadecimal view for inspecting binary responses or hidden characters.
* **Render:** Emulates browser rendering of HTML responses.

---

## 7. Burp Intruder — Automated Fuzzing & Attacks

Intruder automates customized HTTP requests for fuzzing, brute forcing, and parameter enumeration.

```text
[ Target Request ] ──► [ Define Payload Positions (§id§) ] ──► [ Select Attack Type ] ──► [ Run Attack ]
```

### 7.1 Attack Types

| Attack Type | Number of Positions | Number of Paylists | Execution Logic | Use Case |
| :--- | :---: | :---: | :--- | :--- |
| **Sniper** | Multiple | 1 | Places each payload into one position at a time sequentially. | Fuzzing multiple parameters for SQLi/XSS individually. |
| **Battering Ram** | Multiple | 1 | Places the *same* payload into all positions simultaneously. | Testing identical username/password combinations. |
| **Pitchfork** | Multiple | Multiple (1:1) | Iterates through multiple wordlists in lockstep (Index 0 with 0, 1 with 1). | Username:Password credential spraying. |
| **Cluster Bomb** | Multiple | Multiple (Cartesian) | Tests every possible combination of all wordlists. | Brute forcing Username × Password combinations. |

### 7.2 Payload Types
* **Simple List:** Custom list of strings or loaded wordlist file.
* **Numbers:** Sequential or step-based numeric ranges (e.g., `1` to `1000` with step `1`).
* **Brute Forcer:** Character set permutations across specified min/max lengths.
* **Null Payloads:** Sends empty requests repeatedly (useful for denial-of-service or race condition testing).

---

## 8. Auxiliary Utilities

### 8.1 Burp Comparer
Comparer provides visual diffing between two requests or responses.
* **How to Use:** Select two items from HTTP history or Repeater → Right-click → **Send to Comparer** → Choose **Words** or **Bytes** comparison.
* **Use Cases:** Spotting subtle differences in blind SQL injection, authorization discrepancies, or timing behaviors.

### 8.2 Burp Decoder
Decoder provides rapid encoding, decoding, and hashing:
* **Formats Supported:** URL encode/decode, Base64, HTML entities, Hex, ASCII hex, Gzip.
* **Hashing Algorithms:** MD5, SHA-1, SHA-256, SHA-512.
* **Smart Decode:** Automatically attempts heuristic decoding of recognized formats.

### 8.3 Burp Sequencer
Analyzes the quality of randomness in session tokens and anti-CSRF tokens:
* Captures large samples of tokens (e.g., 10,000+ samples).
* Performs statistical randomness tests (FIPS 140-2 entropy tests) to determine token predictability.

---

## 9. Practical Burp Workflows

### 9.1 Multi-Account Authorization Testing Workflow

To effectively test for horizontal and vertical privilege escalation without session confusion:

```text
Step 1: Open Target in Two Separate Browser Contexts
        - Browser 1 (Normal Window): Authenticated as Administrator
        - Browser 2 (Incognito Window / Multi-Account Container): Authenticated as Standard User (wiener)
        ↓
Step 2: Capture Privileged Requests in HTTP History
        - Perform administrative action as Administrator (e.g., user promotion, delete, config change)
        - Locate request in HTTP history and send to Repeater (Tab 1: "Admin Baseline")
        ↓
Step 3: Test Unauthenticated Access
        - Duplicate request in Repeater (Tab 2: "Unauth Test")
        - Remove Cookie / Authorization headers
        - Send request and observe response (Expect 401/403/302 to login)
        ↓
Step 4: Test Vertical Privilege Escalation
        - Duplicate request in Repeater (Tab 3: "Standard User Test")
        - Replace Administrator session cookie with Standard User (wiener) session cookie
        - Send request and observe response
        ↓
Step 5: Test Method & Header Tampering
        - Switch HTTP method (POST <-> GET)
        - Inject headers: X-Original-URL: /admin, Referer: /admin
        - Compare response status code and length against Admin Baseline
```

### 9.2 Parameter & IDOR Manipulation Workflow

```text
1. Capture object-retrieval request in Proxy:
   GET /api/documents?docId=1001 HTTP/2
   Cookie: session=[USER-A-SESSION]
   ↓
2. Send to Repeater (Ctrl+R)
   ↓
3. Replace object ID with target user's ID:
   GET /api/documents?docId=1002 HTTP/2
   ↓
4. Send request and inspect response:
   - Status 200 OK + User B data leaked ──> IDOR Confirmed
   - Status 403 Forbidden ──> Access control properly enforced
```

---

## 10. Summary Cheat Sheet & Shortcuts

| Action | Windows / Linux Shortcut | macOS Shortcut |
| :--- | :--- | :--- |
| **Send to Repeater** | `Ctrl + R` | `Cmd + R` |
| **Send to Intruder** | `Ctrl + I` | `Cmd + I` |
| **Send Request in Repeater** | `Ctrl + Space` | `Cmd + Space` |
| **Forward Intercepted Request** | `Ctrl + F` | `Cmd + F` |
| **Drop Intercepted Request** | `Ctrl + D` | `Cmd + D` |
| **Switch to Next Tab** | `Ctrl + Tab` | `Cmd + Tab` |
| **URL-Encode Selected Text** | `Ctrl + U` | `Cmd + U` |
| **URL-Decode Selected Text** | `Ctrl + Shift + U` | `Cmd + Shift + U` |
