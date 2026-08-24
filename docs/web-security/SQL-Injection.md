# SQL Injection (SQLi)

SQL Injection (SQLi) is a critical web security vulnerability that allows an attacker to interfere with the queries that an application makes to its relational database (e.g., MySQL, PostgreSQL, MSSQL, Oracle).

---

## 1. Operating Mechanism & Vulnerability Root Cause

SQL injection occurs when untrusted user-supplied input is directly concatenated or interpolated into a dynamic SQL query string instead of being separated from the query logic via parameterized statements:

```text
Attacker Input: ' OR '1'='1' --

Vulnerable Dynamic Query:
SELECT * FROM users WHERE username = '' OR '1'='1' --' AND password = '...'
                                      └─────────────┘
                              (Evaluates to TRUE for all rows)
```

---

## 2. Primary SQL Injection Taxonomies

```text
SQL Injection Types
├── 1. In-Band (Classic) SQLi
│   ├── Error-Based SQLi (Database error messages disclose schema/data)
│   └── UNION-Based SQLi (Appends results of secondary query via UNION SELECT)
├── 2. Inferential (Blind) SQLi
│   ├── Boolean-Based Blind (Infers data via true/false application responses)
│   └── Time-Based Blind (Infers data by injecting database sleep/delay functions)
└── 3. Out-of-Band (OOB) SQLi
    └── Triggers external DNS/HTTP network interactions to exfiltrate data
```

---

### 2.1 UNION-Based SQL Injection
UNION-based attacks allow an attacker to extract data from arbitrary database tables by combining the original query result set with the result set of an attacker-crafted `SELECT` statement:

```sql
' UNION SELECT null, username, password FROM users--
```

* **Requirements:**
  1. Both queries must request the exact same number of columns.
  2. The data types of corresponding columns must be compatible.

---

### 2.2 Blind SQL Injection (Boolean & Time-Based)
When the application does not return database query results or error messages in the HTTP response:

* **Boolean-Based Blind:** The attacker injects conditional SQL expressions (e.g., `' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='admin')='a'--`) and observes whether the webpage renders normally (True) or changes (False).
* **Time-Based Blind:** The attacker injects sleep statements (e.g., `pg_sleep(10)` or `WAITFOR DELAY '0:0:10'`) to infer characters based on response latency.

---

## 3. Defense & Remediation: Parameterized Queries

The definitive defense against all forms of SQL injection is the use of **Parameterized Queries (Prepared Statements)**:

```php
// SECURE: Parameterized Query using PDO
$stmt = $pdo->prepare('SELECT id, username FROM users WHERE email = :email AND password = :password');
$stmt->execute(['email' => $email, 'password' => $password]);
$user = $stmt->fetch();
```

* **Mechanism:** The database treats parameters strictly as literal data values, never as executable SQL commands, rendering injection payloads harmless.
