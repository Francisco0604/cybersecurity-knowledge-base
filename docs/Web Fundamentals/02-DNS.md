# Module 1 – Web Fundamentals

---

# Lesson 2 – Domain Name System (DNS)

## Objective

Understand what DNS is, why it exists, how domain names are resolved into IP addresses, how DNS caching works, and why DNS is essential before any HTTP communication can occur.

---

## What is DNS?

**DNS (Domain Name System)** is the Internet's naming system.

Its primary purpose is to translate a human-readable domain name into an IP address that computers can use to communicate.

**Example:**

`google.com`  →  `142.xxx.xxx.xxx`

- **Humans** remember names.
- **Computers** communicate using IP addresses.

---

## Why Do We Need DNS?

It is much easier for people to remember:

- `google.com`
- `github.com`
- `amazon.com`

than remembering numerical IP addresses.

DNS acts as a translator between users and computers.

---

## Real-World Analogy

DNS works like the contacts application on a phone.

When you tap **Mom**, your phone actually dials `+91XXXXXXXXXX`.

- You remember the **name**.
- The phone uses the **number**.

Similarly:

- **Users** remember domain names.
- **Computers** use IP addresses.

---

## Website Communication Flow

When a user enters a website into the browser, DNS is the first major network service involved.

```text
User
  ↓
Browser
  ↓
DNS Lookup
  ↓
IP Address Returned
  ↓
TCP Connection
  ↓
TLS Handshake (HTTPS)
  ↓
HTTP Request
  ↓
Web Server
  ↓
HTTP Response
  ↓
Browser Displays Website
```

Without DNS, the browser does not know where to send the HTTP request.

---

## What Happens if DNS Fails?

If DNS is unavailable, the browser cannot resolve the domain name into its corresponding IP address.

Without the destination IP address, the browser cannot establish a connection with the web server.

As a result, the website fails to load and the user typically receives a DNS-related error.

---

## How DNS Finds an IP Address

When a browser needs the IP address of a domain:

1. It sends a DNS query to a DNS server.
2. If the DNS server already knows the answer (or has it cached), it immediately returns the IP address.
3. If it does not know the answer, it queries other DNS servers in the DNS hierarchy until the correct IP address is found.
4. The IP address is returned to the browser.
5. The browser can now establish a connection with the destination server.

> **Note:** DNS servers communicate with other DNS servers, not with the destination web server itself.

---

## DNS Caching

DNS responses are cached to improve performance.

If a domain has already been resolved recently, the cached IP address can be reused instead of performing another DNS lookup.

This makes websites load faster and reduces unnecessary network traffic.

DNS caching may occur at multiple levels:

- **Browser Cache**
- **Operating System DNS Cache**
- **Router Cache** (sometimes)
- **ISP DNS Cache**

---

## Key Concepts

- DNS translates domain names into IP addresses.
- DNS resolution happens before any HTTP communication.
- DNS servers can query other DNS servers if they do not know the answer.
- DNS responses are cached to improve performance.
- Browsers cannot connect to websites without first obtaining the destination IP address.

---

## Important Terms

### Domain Name
A human-readable name used to identify a website (e.g., `google.com`).

### IP Address
A numerical address used to uniquely identify a device on a network (e.g., `142.xxx.xxx.xxx`).

### DNS Server
A server responsible for resolving domain names into IP addresses. If necessary, it can query other DNS servers until it finds the correct answer.

### DNS Cache
Temporary storage used to save recently resolved domain names and their IP addresses, reducing the need for repeated DNS lookups.

---

## Practical Observations

- DNS is always performed before an HTTP request.
- If DNS fails, the browser cannot locate the destination server.
- Repeated visits to the same website are often faster because the IP address may already be cached.

---

## Interview Questions

### What is DNS?
DNS (Domain Name System) is a service that translates human-readable domain names into IP addresses so computers can communicate over a network.

---

### Why is DNS required?
Computers communicate using IP addresses, while humans remember domain names. DNS bridges this gap by translating domain names into their corresponding IP addresses.

---

### What happens if DNS is unavailable?
The browser cannot resolve the domain name into an IP address, preventing it from establishing a connection with the destination server.

---

### Why is DNS caching useful?
DNS caching stores recently resolved domain names and their IP addresses, reducing lookup time, improving browsing speed, lowering latency, and decreasing unnecessary network traffic.

---

## Lesson Summary

- DNS stands for **Domain Name System**.
- DNS translates domain names into IP addresses.
- DNS resolution occurs before TCP, TLS, and HTTP communication.
- DNS servers may query other DNS servers if they do not know the answer.
- DNS responses are cached to improve performance.
- Without DNS, users cannot reach websites using domain names.

---

## My Understanding

- **Burp Suite** works because the browser is configured to use it as a proxy.
- **DNS** is like the Internet's phonebook—it converts domain names into IP addresses.
- A browser cannot communicate with a web server until DNS resolution is complete.
- **DNS** uses caching to avoid repeated lookups and improve performance.
