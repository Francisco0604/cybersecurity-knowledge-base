# Server-Side Request Forgery (SSRF)

Server-Side Request Forgery (SSRF) is a web vulnerability that allows an attacker to induce the backend server-side application to make HTTP requests to an arbitrary domain or IP address chosen by the attacker.

---

## 1. Operating Mechanism & Vulnerability Patterns

In a typical SSRF attack, the attacker causes the server to establish a connection to internal-only services within the organization's infrastructure, cloud metadata endpoints, or external systems:

```text
[ Attacker ]
     │
     │ 1. Submits request with malicious internal URL (e.g., http://169.254.169.254/)
     ▼
[ Vulnerable Web Server (DMZ / Cloud) ]
     │
     │ 2. Server makes backend HTTP request on behalf of attacker
     ▼
[ Internal Infrastructure / Cloud IMDS ] (169.254.169.254 / localhost / 192.168.10.X)
     │
     │ 3. Returns sensitive internal metadata, API keys, or admin panels
     ▼
[ Attacker Receives Sensitive Internal Data ]
```

---

## 2. Common SSRF Attack Targets

### 2.1 Cloud Instance Metadata Services (IMDS)
Cloud instances (AWS, Azure, GCP) provide local HTTP metadata services accessible only from the instance itself at `169.254.169.254`:

* **Target URL:** `http://169.254.169.254/latest/meta-data/iam/security-credentials/`
* **Impact:** Discloses temporary cloud IAM access keys, secret keys, and security tokens, allowing complete cloud infrastructure compromise.

---

### 2.2 Internal Network Pivoting & Loopback Access
Applications often expose internal microservices, administrative consoles (e.g., Redis, Elasticsearch, RabbitMQ), or database APIs on `127.0.0.1` or private IP ranges (`10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`) without authentication, assuming perimeter firewalls protect them.

* **Exploit:** An attacker uses SSRF to probe internal subnets, discover live services, and execute unauthorized internal commands.

---

## 3. Defense & Remediation

1. **Strict Input Whitelisting:** Enforce strict whitelists of permitted domain names, schemes (only `https`), and ports.
2. **Block Private & Loopback IP Ranges:** Reject requests resolving to loopback (`127.0.0.0/8`), private RFC 1918 subnets, and cloud metadata (`169.254.169.254`).
3. **Enforce IMDSv2:** In cloud environments (e.g., AWS), enforce IMDSv2 which requires session token headers (`X-aws-ec2-metadata-token`) that simple GET-based SSRF cannot generate.
4. **Network Segmentation:** Place backend APIs in isolated subnets with strict egress firewall rules preventing the web server from contacting internal management interfaces.
