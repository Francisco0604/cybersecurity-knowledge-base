# Cross-Site Request Forgery (CSRF)

Cross-Site Request Forgery (CSRF / XSRF) is a web security vulnerability that allows an attacker to induce an authenticated user to perform unintended state-changing actions on a vulnerable web application (e.g., transferring funds, changing email addresses, or modifying passwords).

---

## 1. Operating Mechanism

CSRF exploits the default browser behavior of automatically including stored session cookies with every cross-site request sent to the target origin:

```text
[ Authenticated Victim ] ──► (Has active session cookie on vulnerable-bank.com)
            │
            │ 1. Victim visits malicious site (attacker.com)
            ▼
[ Attacker Website ] ──► (Hosts hidden auto-submitting HTML form)
            │
            │ 2. Submits POST request to vulnerable-bank.com/transfer
            ▼
[ Victim's Browser ] ──► (Automatically attaches victim's bank session cookie)
            │
            │ 3. Authenticated transfer request sent to bank
            ▼
[ Vulnerable Bank Server ] ──► (Executes unauthorized transfer!)
```

---

## 2. Conditions Required for CSRF Exploitation

A successful CSRF attack requires three primary conditions:
1. **A Relevant Action:** A state-changing operation within the application (such as updating account email, changing password, or modifying access permissions).
2. **Cookie-Based Session Handling:** The application relies solely on browser session cookies to identify the user submitting the request.
3. **No Unpredictable Request Parameters:** The request parameters can be completely predicted or constructed in advance by the attacker (no anti-CSRF tokens).

---

## 3. Defense & Remediation

```text
CSRF Defensive Controls
├── 1. Anti-CSRF Tokens (Unique, secret, unguessable tokens validated on server)
├── 2. SameSite Cookie Attributes (SameSite=Strict or SameSite=Lax)
└── 3. Custom Request Headers (e.g., X-Requested-With / Authorization Bearer)
```

* **Anti-CSRF Tokens (Synchronizer Token Pattern):** The server generates a cryptographically random, unpredictable token bound to the user's session and embeds it in HTML forms. When the form is submitted, the server verifies that the submitted token matches the session value. Cross-origin sites cannot read or forge this token due to the **Same-Origin Policy (SOP)**.
* **`SameSite=Lax/Strict` Cookies:** Modern browsers withhold `SameSite` cookies on cross-site requests, blocking CSRF payloads initiated from external sites.
