# 04 - Burp Repeater Manual Manipulation Guide

## Overview

**Burp Repeater** is a manual request manipulation tool used to edit individual HTTP/HTTPS requests, send them to the server, and analyze the resulting responses. Unlike the Proxy Intercept feature, Repeater allows iterative testing without needing to re-trigger actions through the web browser.

---

## 1. Why Use Repeater?

* **Speed & Iteration**: Test multiple payload variations rapidly.
* **Isolation**: Tweak single parameters or headers without side effects on active browser sessions.
* **Response Inspection**: Analyze exact status codes, raw headers, response times, and rendering output.
* **History Tracking**: Repeater saves request history tabs for side-by-side comparison.

---

## 2. Sending Requests to Repeater

1. From **Proxy** -> **HTTP History** (or **Target** -> **Site Map**):
2. Select the target request.
3. Use shortcut **`Ctrl + R`** (Windows/Linux) or **`Cmd + R`** (macOS), or right-click and choose **Send to Repeater**.
4. Switch to the **Repeater** tab to begin testing.

---

## 3. Repeater Interface & Workflow

```text
+------------------------------------+------------------------------------+
| Request Panel                      | Response Panel                     |
|                                    |                                    |
| POST /api/user/profile HTTP/1.1    | HTTP/1.1 200 OK                    |
| Host: target.com                   | Content-Type: application/json     |
| Cookie: session=abc...             |                                    |
|                                    | { "id": 105, "role": "admin" }     |
| {"id": 105}                        |                                    |
+------------------------------------+------------------------------------+
| [ Send ]  <-- Button to fire       | Views: Pretty | Raw | Hex | Render |
+------------------------------------+------------------------------------+
```

### Key Controls & Features
* **Send Button**: Transmits the modified HTTP request to the server.
* **Request Tab Inspector**: Easily add, edit, or remove parameters, headers, and cookies via a structured sidebar.
* **Response Views**:
  * **Pretty**: Formats JSON, XML, and HTML for easy readability.
  * **Raw**: Displays unformatted HTTP response stream.
  * **Hex**: Shows byte-level response data.
  * **Render**: Renders response HTML inside an embedded browser view.
* **Response Inspector**: Analyzes header attributes, set cookies, and body byte count.

---

## 4. Practical Pentesting Workflows in Repeater

### Workflows #1: Parameter Tampering & Logic Flaws
* **Scenario**: E-commerce price manipulation.
* **Action**: Intercept checkout request, send to Repeater, change `price=99.99` to `price=0.01` or `price=-50.00`, click **Send**, and check if the server accepts the modified value.

### Workflows #2: Insecure Direct Object References (IDOR)
* **Scenario**: Accessing another user's profile.
* **Action**: Capture request `/api/users/102`, send to Repeater, change object ID to `/api/users/101` or `/api/users/100`, observe if unauthorized account details are returned in the response.

### Workflows #3: SQL Injection (SQLi) Payload Testing
* **Scenario**: Testing authentication bypass or error-based SQLi.
* **Action**: In Repeater, append payload `' OR '1'='1` to username input parameter, URL-encode payload (`Ctrl + U`), click **Send**, and check status line / response body for SQL errors or authentication bypass.

### Workflows #4: Authentication & Cookie Flags Inspection
* **Scenario**: Session fixation or missing security header validation.
* **Action**: Observe response headers returned after login (`Set-Cookie`) to confirm presence of `Secure`, `HttpOnly`, and `SameSite` flags.

---

## 5. Essential Shortcuts in Repeater

| Action | Windows / Linux | macOS |
| :--- | :--- | :--- |
| **Send Request to Repeater** | `Ctrl + R` | `Cmd + R` |
| **URL-Encode Selected Text** | `Ctrl + U` | `Cmd + U` |
| **URL-Decode Selected Text** | `Ctrl + Shift + U` | `Cmd + Shift + U` |
| **Send Request to Server** | `Ctrl + Space` | `Cmd + Space` |
