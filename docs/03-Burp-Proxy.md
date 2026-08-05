# 03 - Burp Suite Proxy Setup & Workflow

## Overview

Burp Suite Proxy operates as a Man-in-the-Middle (MitM) intercepting proxy positioned between the security tester's browser and target web servers. It allows security professionals to inspect, intercept, modify, and replay HTTP/HTTPS traffic in real time.

---

## 1. Intercepting Proxy Concept

```text
[ Browser ] ---> ( Intercepting Proxy : Port 8080 ) ---> [ Target Web Server ]
```

When Intercept is enabled:
1. The browser initiates an HTTP request.
2. Burp Proxy catches the request before it reaches the network.
3. The tester can review parameter values, headers, and request body.
4. The request can be **Forwarded** as-is, **Modified** before sending, or **Dropped** completely.

---

## 2. Configuration & Setup

### Setting Up Proxy Listener
1. Open Burp Suite -> Navigate to **Proxy** -> **Proxy Settings**.
2. Under **Proxy Listeners**, verify the listener is set to `127.0.0.1:8080` (Running).

### Browser Proxy Configuration (FoxyProxy)
1. Install **FoxyProxy Standard** extension in Firefox.
2. Add a new proxy profile:
   - **Proxy Type**: HTTP
   - **IP Address**: `127.0.0.1`
   - **Port**: `8080`
3. Toggle FoxyProxy to route traffic through Burp.

### Installing Burp CA Certificate (HTTPS Decryption)
To inspect encrypted HTTPS traffic without certificate security warnings:
1. Ensure proxy routing is active, then navigate to `http://burp` in your browser.
2. Click **CA Certificate** in the top right to download `cacert.der`.
3. Open Firefox Settings -> **Privacy & Security** -> **Certificates** -> **View Certificates...**
4. Under **Authorities**, click **Import...**, select `cacert.der`, and check **"Trust this CA to identify websites"**.

---

## 3. Core Proxy Features & Features Usage

### Target Scope Management
Adding targets to scope reduces noise from background browser requests (e.g., extensions, OS telemetry):
1. Navigate to **Target** -> **Site Map**.
2. Right-click the target domain (e.g., `http://192.168.10.30:3000`) and select **Add to scope**.
3. Under **Proxy** -> **Proxy Settings** -> **Request Interception Rules**, check **"And URL is in target scope"**.

### HTTP History Tab
* Logs every HTTP/HTTPS request and response passing through the proxy.
* Supports filtering by status code, MIME type, parameter presence, and search terms.
* Allows right-clicking any request to send it to other Burp tools (**Repeater**, **Intruder**, **Decoder**, **Comparer**).

### Intercept Tab Controls
* **Intercept is ON / OFF**: Toggles request pausing.
* **Forward**: Sends the current intercepted request to the server.
* **Drop**: Aborts the request, preventing it from reaching the server.
* **Action Menu**: Provides options to send to Repeater, change request method (`GET` <-> `POST`), or automatically modify parameters.

### Match and Replace Rules
Automatically modify requests or responses on the fly:
* **Location**: **Proxy** -> **Proxy Settings** -> **Match and Replace**.
* **Use Cases**:
  * Emulate different user agents (`User-Agent: Mobile`).
  * Remove `If-Modified-Since` headers to force server responses.
  * Inject custom headers (e.g., `X-Forwarded-For: 127.0.0.1` to test IP restriction bypasses).
