# Authentication Security & Credential Vulnerabilities

Authentication is the security process of verifying the claimed identity of a user, process, or device. In web applications, authentication mechanisms establish the foundational security boundary upon which all downstream session management and authorization controls rely.

---

## 1. Authentication Architecture & Identity Verification

```text
       [ User / Client ]
               │
               │ (Transmits Identity & Secret Proof)
               ▼
[ Authentication Endpoint ]
  ├── 1. Format & Input Sanitization
  ├── 2. Identity Lookup (Username/Email)
  ├── 3. Password Verification (Argon2id/bcrypt comparison)
  └── 4. Decision Engine
               │
       +───────┴───────+
       ▼               ▼
[ Valid Identity ]   [ Invalid Identity ]
  ├── Generate Session ├── Generic Error Response
  └── Reset Attempts   └── Rate-Limit / Account Lockout Counter
```

---

## 2. Authentication Vulnerability Taxonomies

### 2.1 Username & Account Enumeration
Account enumeration occurs when an application leaks whether a given username or email exists in the database.

* **Mechanisms of Information Disclosure:**
  1. **Different Error Messages:** Returning `"Invalid username"` vs. `"Invalid password"` or `"Email does not exist"` vs. `"Incorrect password"`.
  2. **Subtly Different Error Messages:** Minute differences in phrasing, punctuation, capitalization, or error code formats.
  3. **Response Timing Discrepancies:** The application hashes passwords with computationally heavy algorithms (e.g., bcrypt) only when a user exists, causing existent usernames to have measurably longer response latencies.
  4. **Account Lockout Behavior:** Attempting multiple logins causes an existing user account to lock (returning `"Account locked"`), revealing that the account is valid.
* **Security Impact:**
  Transforms an exponential search space (guessing Username + Password) into a linear dictionary search (known valid usernames + targeted passwords).

```text
Search Space Reduction:
Unknown Username + Unknown Password  ──►  Known Username + Unknown Password
```

---

### 2.2 Password Reset Vulnerabilities & Low-Entropy Tokens
Password recovery workflows often represent the weakest link in authentication architecture:

```text
Attacker Requests Reset for Target Account
                    │
                    ▼
[ Reset Token Generation ] ──(Low Entropy: 3-4 Digits / Sequential / Predictable)
                    │
                    ▼
[ Token Brute-Force / Prediction Attack ] (No Rate Limiting)
                    │
                    ▼
[ Account Takeover (ATO) ] ──► Resets Password & Takes Over Account
```

* **Vulnerability Patterns:**
  - **Predictable / Low Entropy Tokens:** Utilizing short numeric codes (e.g., 3-digit or 4-digit PINs) without rate limiting, allowing automated brute-forcing via tools like Burp Suite Intruder.
  - **Broken Reset Logic:** Reset mechanisms that accept a token parameter but fail to bind the token to the specific requesting email/account server-side.
  - **Password Reset Poisoning:** Relying on untrusted HTTP headers (e.g., `Host`, `X-Forwarded-Host`) to construct password reset links in emails, redirecting tokens to attacker servers.

---

### 2.3 HTTP Basic Authentication Security
HTTP Basic Authentication (RFC 7617) is an HTTP-native challenge-response mechanism:

```http
GET /admin HTTP/1.1
Authorization: Basic YWRtaW46cGFzc3dvcmQxMjM=
```

* **Core Principles & Limitations:**
  - **Base64 is Encoding, Not Encryption:** The `Authorization` header consists of `base64(username:password)` which is trivially decoded. Without HTTPS/TLS transport security, credentials are exposed in plaintext.
  - **No Inherent Anti-Automation:** The Basic Auth protocol has no built-in rate limiting, lockout, or challenge mechanisms (CAPTCHA), making unthrottled endpoints susceptible to rapid offline and online dictionary attacks (e.g., via THC-Hydra).

---

### 2.4 Brute-Force & Credential Stuffing Attacks
Automated credential attacks attempt to guess passwords against known or enumerated accounts:

```text
Credential Attacks
├── Password Spraying      (One common password against hundreds of user accounts)
├── Dictionary Attack      (Large wordlist against a single targeted account)
├── Credential Stuffing    (Automated injection of stolen breached username/password pairs)
└── Brute-Force Fuzzing    (Algorithmic generation of character combinations)
```

---

## 3. Defense-in-Depth Authentication Architecture

```text
┌─────────────────────────────────────────────────────────────┐
│ 1. Generic Error Handling                                   │
│ Return identical "Invalid username or password" responses   │
├─────────────────────────────────────────────────────────────┤
│ 2. Constant-Time Processing & Dummy Hashing                 │
│ Execute password hash operations even for non-existent users│
├─────────────────────────────────────────────────────────────┤
│ 3. Cryptographically Strong Tokens                          │
│ Generate high-entropy reset tokens (CSPRNG, >= 128 bits)    │
├─────────────────────────────────────────────────────────────┤
│ 4. Anti-Automation & Adaptive Rate Limiting                 │
│ IP & account-based rate limiting, progressive delays, CAPTCHA│
├─────────────────────────────────────────────────────────────┤
│ 5. Multi-Factor Authentication (MFA / 2FA)                  │
│ Enforce independent authentication factors                  │
└─────────────────────────────────────────────────────────────┘
```

---

## 4. Key Takeaways to Remember

1. **Error messages are part of the attack surface:** Descriptive messages leak identity state.
2. **Encoding $\neq$ Encryption:** Base64 provides zero confidentiality.
3. **Password resets must match authentication security:** A weak reset token invalidates strong user passwords.
4. **Rate limiting must be multi-dimensional:** Throttle based on both client IP and target username.
