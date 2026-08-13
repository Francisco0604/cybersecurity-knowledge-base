# Burp Suite

## 1. What is Burp Suite?

Burp Suite is a comprehensive platform for testing web application security.

At its core, Burp operates as an inline HTTP proxy sitting between your client browser and the target web server:

```text
Browser
   ↓
 Burp
   ↓
Web Server
```

### Standard Web Traffic Flow
```text
Browser ───────────────► Server
```

### Intercepted Traffic Flow with Burp
```text
Browser ──► Burp ──► Server
             │
             └── Inspect / modify HTTP traffic
```

Burp Suite allows security testers to inspect, intercept, modify, and replay HTTP/HTTPS traffic in real-time.

---

## 2. Burp Proxy

**Burp Proxy** is the core component that intercepts and logs network traffic passing between the web browser and the web server.

### Key Capabilities
* **Intercept**: Pause incoming and outgoing traffic.
* **Inspect**: Examine HTTP headers, parameters, body data, and cookies.
* **Modify**: Alter requests before they reach the server or responses before they reach the browser.
* **Forward**: Send modified or unmodified requests onward to their destination.
* **Drop**: Abort requests to prevent them from reaching the server.
* **Record**: Maintain a log of all HTTP traffic for analysis.

### Traffic Flow Model
```text
Browser
   ↓
HTTP Request
   ↓
Burp Proxy
   ↓
Web Server
   ↓
HTTP Response
   ↓
Browser
```

---

## 3. Request Interception

The **Intercept** feature allows Burp to pause an HTTP request before it reaches the target server.

### Example Intercepted Request
```http
GET /login HTTP/2
Host: example.com
User-Agent: Mozilla/5.0
Cookie: session=ABC123
```

### Intercepted Execution State
```text
Browser
   ↓
Request
   ↓
BURP PROXY
   ↓
[PAUSED - Awaiting User Action]
   ↓
Target Server
```

### Interception Modes
* **Intercept ON**: Active requests are held in a paused state, allowing live modification.
* **Intercept OFF**: Requests pass through Burp continuously while still being recorded in HTTP History.

---

## 4. Traffic Control: Forward vs. Drop

When a request is intercepted, you must decide how Burp handles it:

### Forward
Sends the current request (along with any manual edits) to the target server.

```text
Intercepted Request
       ↓
    Forward
       ↓
 Target Server
```

### Drop
Discards the request immediately, preventing it from executing on the server.

```text
Intercepted Request
       ↓
      Drop
       ↓
      ❌ (Request Terminated)
```

---

## 5. Burp Embedded Browser

Burp Suite includes a pre-configured Chromium browser tailored for security testing.

### Advantages
* **Pre-configured Proxy**: Connects to Burp Proxy out of the box without manual network settings.
* **Isolated Environment**: Prevents personal browser extension noise and state leakage.
* **CA Certificate Ready**: Handles HTTPS decryption automatically.

### Basic Testing Workflow
```text
Burp Suite
   ↓
Launch Embedded Browser
   ↓
Navigate to Target Application
   ↓
Generate HTTP Requests
   ↓
Burp Proxy Intercepts Traffic
```

> **Note**: For educational and practical testing, always use authorized targets or dedicated vulnerable environments such as the PortSwigger Web Security Academy.

---

## 6. HTTP History

The **HTTP History** log records all HTTP requests and responses passing through the proxy.

Unlike live Interception, HTTP History works passively without pausing traffic.

### Intercept vs. HTTP History

| Feature | Intercept | HTTP History |
| :--- | :---: | :---: |
| Pauses execution | ✅ | ❌ |
| Inspects traffic content | ✅ | ✅ |
| Live request modification | ✅ | ❌ |
| Persistent traffic recording | ❌ | ✅ |

Think of HTTP History as a **searchable audit log of web communication**.

---

## 7. HTTP Request & Response Anatomy

Web applications rely on a bi-directional request-response architecture:

```text
Client / Browser ── REQUEST ──► Server

Client / Browser ◄─ RESPONSE ── Server
```

### HTTP Request Example
```http
GET /web-security HTTP/2
Host: portswigger.net
User-Agent: Mozilla/5.0
Accept: text/html
Cookie: session=xyz987
```

### HTTP Response Example
```http
HTTP/2 200 OK
Content-Type: text/html; charset=utf-8
Set-Cookie: session=xyz987; Secure; HttpOnly

<!DOCTYPE html>
<html>...</html>
```

---

## 8. Key HTTP Request Components

### 1. HTTP Method
Defines the intended action of the request.
```http
GET /web-security HTTP/2
```
* **GET**: Requests data from a specified resource.
* **POST**: Submits data to be processed by a specified resource.

### 2. Path
Indicates the exact resource or endpoint being targeted.
```http
/web-security/all-labs
```

### 3. Host Header
Specifies the domain name or IP address of the target server.
```http
Host: portswigger.net
```

### 4. User-Agent Header
Identifies the client browser, operating system, and software version making the request.
```http
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) ...
```

### 5. Cookie Header
Transmits stored session identifiers or state tokens back to the server.
```http
Cookie: session=ABC123XYZ
```

---

## 9. Key HTTP Response Components

### 1. Status Code
Indicates the result of the server's processing.
* `200 OK`: Request succeeded.
* `302 Found`: Redirect to another URL.
* `403 Forbidden`: Access denied.
* `404 Not Found`: Resource does not exist.
* `500 Internal Server Error`: Unhandled server-side exception.

### 2. Content-Type Header
Tells the client browser how to render the response body.
```http
Content-Type: text/html
```

### 3. Set-Cookie Header
Instructs the browser to store a new cookie or update an existing session identifier.

```text
Server Response (Set-Cookie: session=ABC123)
                   ↓
         Browser Stores Cookie
                   ↓
Future Request (Cookie: session=ABC123)
                   ↓
          Server Identifies User
```

---

## 10. Burp Repeater

**Burp Repeater** is a manual request manipulation tool used to modify, re-issue, and analyze HTTP requests interactively.

### Repeater Workflow
```text
HTTP History / Intercept
           ↓
Right-Click → Send to Repeater (Ctrl + R)
           ↓
     Modify Request
           ↓
    Click "Send"
           ↓
 Inspect Response Output
```

---

## 11. Controlled Testing Methodology

When analyzing application behavior in Repeater, follow a disciplined testing methodology:

```text
Identify Controllable Parameter
               ↓
    Change ONE Variable at a Time
               ↓
          Send Request
               ↓
         Observe Response
               ↓
   Compare against Baseline Response
```

> **Best Practice**: Avoid modifying multiple parameters simultaneously. Controlled single-variable changes clarify exact cause-and-effect relationships in application responses.

---

## 12. Query Parameters Analysis

Query parameters supply additional inputs to server-side scripts via the URL.

### Parameter Breakdown
```http
GET /search?q=burp&category=tools HTTP/2
```

```text
/search
   ↓
Target Path

?q=burp&category=tools
   ↓
Query String

Param 1 Name  : q
Param 1 Value : burp

Param 2 Name  : category
Param 2 Value : tools
```

Parameters directly dictate server logic, making them high-priority targets for manual assessment.

---

## 13. Security Testing Mindset

Shift focus from interface navigation to application mechanics:

❌ **Tool-Centric View**: *"Which button should I click next in Burp?"*

✅ **Target-Centric View**: *"Which request parameter controls the application logic, and how does the server react to modified inputs?"*

### Analyzing Request Mechanics
```http
GET /profile?id=1001 HTTP/2
Host: example.com
Cookie: session=ABC123
```

```text
GET           → Action requested
/profile      → Resource targeted
id=1001       → User object identifier
Cookie        → Current session identity
Response      → Server authorization logic output
```

---

## 14. Burp Suite vs. Wireshark

Understanding the distinct scope of web proxy vs. network packet analysis tools:

| Feature | Wireshark | Burp Suite |
| :--- | :--- | :--- |
| **Primary Level** | Network Layer (Packets) | Application Layer (HTTP/HTTPS) |
| **Protocol Focus** | TCP, UDP, IP, DNS, TLS, ARP | HTTP, HTTPS, WebSockets |
| **Key Capability** | Passive network analysis | Active request interception & replay |
| **Primary Use Case** | Troubleshooting network traffic | Manual web application security testing |

---

## 15. Core Operational Flow Summary

```text
                            BURP SUITE PROXY
                                   │
               ┌───────────────────┴───────────────────┐
               ↓                                       ↓
         Proxy Intercept                          HTTP History
      (Pause, Forward, Drop)                   (Passive Log Stream)
               │                                       │
               └───────────────────┬───────────────────┘
                                   ↓
                             Burp Repeater
                                   │
                        Modify Single Parameter
                                   │
                           Re-issue Request
                                   │
                        Analyze Response Delta
```

---

## 16. Practical Hands-On Walkthrough: Testing a Lab Request

This walkthrough details the step-by-step procedure for capturing, isolating, sending, and modifying an HTTP request using PortSwigger Web Security Academy or a local test target.

### Step 1: Open Target in Burp's Embedded Browser
1. Launch **Burp Suite**.
2. Navigate to **Proxy** -> **Intercept**.
3. Click **Open Browser** to launch Burp’s pre-configured browser.
4. Navigate to your target lab URL (e.g., PortSwigger Lab).

### Step 2: Capture Traffic and Locate Target Request
1. Ensure **Intercept is OFF** initially while navigating the application to populate **HTTP History**.
2. Perform an action on the site (e.g., clicking a product page, searching, or logging in).
3. Navigate to **Proxy** -> **HTTP History**.
4. Scroll through the logged requests and locate the relevant target endpoint (e.g., `GET /product?productId=1`).

### Step 3: Send Request to Repeater
1. Right-click the target request in **HTTP History**.
2. Select **Send to Repeater** (or press `Ctrl + R`).
3. Switch to the **Repeater** tab at the top of Burp Suite.

### Step 4: Establish Baseline Response
1. In Repeater, click **Send** without altering any parameters.
2. Observe the baseline response parameters:
   * **HTTP Status Code** (e.g., `200 OK`)
   * **Response Length / Bytes** (e.g., `3452 bytes`)
   * **Body Content** (e.g., product details for item `1`)

### Step 5: Perform Controlled Parameter Modification
1. Locate the target query or POST parameter in the request panel (e.g., `productId=1`).
2. Modify **one variable** cleanly (e.g., change `productId=1` to `productId=2`).

```http
GET /product?productId=2 HTTP/2
Host: target-lab.web-security-academy.net
Cookie: session=xyz123
```

### Step 6: Analyze & Compare Response Delta
1. Click **Send** in Repeater.
2. Compare the new response against the baseline response:
   * **Status Code Delta**: Did it return `200 OK`, `404 Not Found`, `403 Forbidden`, or `500 Error`?
   * **Content Length Delta**: Did the byte size increase/decrease substantially?
   * **Behavioral Change**: Is data for item `2` rendered, or did server validation block the modified input?
3. Document response discrepancies to draw conclusions regarding application logic and authorization controls.
