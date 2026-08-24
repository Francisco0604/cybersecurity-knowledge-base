# JSON Web Token (JWT) Security

JSON Web Tokens (JWTs, RFC 7519) are a compact, URL-safe standard for securely transmitting claims between parties as a JSON object. They are widely used in modern web applications, single-page applications (SPAs), and microservice APIs for stateless authentication and authorization.

---

## 1. Anatomy of a JSON Web Token

A JWT consists of three Base64URL-encoded components separated by periods (`.`):

```text
Header.Payload.Signature
```

```text
┌─────────────────────────────────────────────────────────────┐
│ 1. HEADER (Algorithm & Token Type)                          │
│ {"typ": "JWT", "alg": "HS256"}                              │
├─────────────────────────────────────────────────────────────┤
│ 2. PAYLOAD (Claims & Identity Attributes)                   │
│ {"sub": "12345", "username": "user", "admin": 0, "exp":...} │
├─────────────────────────────────────────────────────────────┤
│ 3. SIGNATURE (Cryptographic Integrity Proof)                │
│ HMACSHA256(base64Url(Header) + "." + base64Url(Payload), K) │
└─────────────────────────────────────────────────────────────┘
```

> **Critical Rule:** JWTs are **encoded, not encrypted** by default. Anyone possessing a JWT can Base64URL-decode its header and payload. Sensitive data (passwords, secrets, PII) must never be stored in JWT claims.

---

## 2. JWT Vulnerability Taxonomies

```text
JWT Vulnerability Landscape
├── 1. Information Disclosure (Sensitive data in unencrypted claims)
├── 2. Signature Verification Bypass (Missing signature validation)
├── 3. Algorithm Downgrade (alg: none attacks)
├── 4. Weak Secret Cracking (Dictionary attacks against HS256)
├── 5. Algorithm Confusion (Asymmetric RS256 -> Symmetric HS256)
├── 6. Token Lifetime Weaknesses (Missing or excessive exp claim)
└── 7. Audience & Cross-Service Relay (Missing aud claim validation)
```

---

### 2.1 Missing Signature Validation
If the receiving backend application parses JWT claims without verifying the signature:

```python
# VULNERABLE: Signature verification disabled
payload = jwt.decode(token, options={"verify_signature": False})
```

An attacker can modify claims directly (e.g., changing `"admin": 0` to `"admin": 1`) and submit the modified token without generating a valid cryptographic signature.

---

### 2.2 `alg: none` Downgrade Attack
The JWT specification includes an algorithm identifier named `none`, intended for unsigned tokens where integrity is verified by transport-layer security:

* **Attack Vector:** An attacker modifies the JWT header to `{"typ": "JWT", "alg": "none"}` (or case variations like `None`, `NONE`), alters the payload claims, and strips the signature part (retaining trailing `.`):
  ```text
  base64Url(Header).base64Url(Payload).
  ```
* **Remediation:** Applications must explicitly reject `alg: none` and enforce a strict whitelist of accepted algorithms on the server side.

---

### 2.3 Weak Symmetric Secret Cracking (HS256)
Symmetric algorithms like HS256 use a shared secret key for both signing and verification.

* **Attack Vector:** If the signing secret is short, predictable, or a common dictionary word, an attacker captures a valid JWT from network traffic and runs offline dictionary cracking using tools like **John the Ripper** or **Hashcat**:
  ```bash
  john --wordlist=jwt.secrets.list --format=HMAC-SHA256 jwt.txt
  ```
* Once the secret is recovered, the attacker can sign arbitrarily forged tokens.

---

### 2.4 Signature Algorithm Confusion (RS256 $\rightarrow$ HS256)
Algorithm confusion occurs when a system supports both asymmetric (RS256) and symmetric (HS256) algorithms and improperly handles verification keys:

```text
Normal Asymmetric Flow (RS256):
Server Signs with PRIVATE RSA Key  ──► Client Verifies with PUBLIC RSA Key

Algorithm Confusion Attack (HS256):
Attacker Changes Alg to HS256      ──► Attacker Signs with PUBLIC RSA Key as HMAC Secret
Server Verifies with PUBLIC RSA Key ──► Verification SUCCEEDS!
```

* **Mechanism:**
  1. The attacker obtains the application's public RSA key (which is public knowledge).
  2. The attacker modifies the token header to `{"alg": "HS256"}`.
  3. The attacker signs the forged token using HMAC-SHA256 with the public key as the symmetric secret string.
  4. If the server blindly checks `alg` from the token and passes its verification key (the public key) to a generic verification function, the signature validates successfully.

---

### 2.5 Audience & Cross-Service Relay Attacks
When multiple backend services share an identity provider or signing key:

* **Mechanism:** A token legitimately issued for Service A (`"aud": "serviceA"`) is replayed against Service B.
* **Vulnerability:** If Service B fails to validate that the `"aud"` (audience) claim matches its own identifier, it accepts the privileges granted by Service A.

---

## 3. JWT Security Checklist & Best Practices

| Control | Defensive Requirement |
| :--- | :--- |
| **Signature Validation** | Always verify signatures before trusting any claim; never disable signature verification. |
| **Algorithm Whitelisting** | Statically define accepted algorithms on the server; never trust `alg` from user tokens. |
| **Reject `alg: none`** | Explicitly deny unsigned tokens in production environments. |
| **Cryptographic Secrets** | Use high-entropy keys ($\ge 256$ bits of cryptographically secure randomness). |
| **Enforce Expiration (`exp`)** | Require short token lifetimes (e.g., 5–15 minutes) combined with refresh tokens. |
| **Enforce Audience (`aud`)** | Validate that the token was explicitly generated for the receiving service. |
| **No Sensitive Data** | Never store passwords, secrets, or confidential user data in unencrypted claims. |
